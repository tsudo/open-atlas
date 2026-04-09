# check-injection-egress — Explainer

> **v1.1 status: design notes only.** This file is the design specification for a hook Open Atlas plans to ship as a working script in v1.2. The patterns, postures, and customization guidance below are the design contract. If you want to implement this hook now in your own scripting language, this file tells you what to enforce and why. See [README.md](README.md) for the v1.1 → v1.2 framing.

---

## What it does

Two inspection passes on MCP tool I/O:

1. **Inbound (PostToolUse)** — scans MCP tool **responses** for patterns that look like prompt injection attempts.
2. **Outbound (PreToolUse)** — scans MCP tool **call parameters** for credentials or PII that might be getting exfiltrated.

This hook **flags, it does not block.** Every detection produces a stderr warning the model can see and a JSONL line in `hooks/security-events.log` you can review later.

---

## Why flag instead of block

Prompt-injection detection on natural-language tool output is fundamentally probabilistic. A blog post about prompt injection will trip the same regex as an actual injection attempt. If we blocked on every match, you'd quickly disable the hook out of frustration — and miss the real attacks when they come.

The flag-and-log posture is the right trade-off here:

- **The model sees the warning** — it has the chance to notice "wait, this tool response is trying to override my instructions" and surface it to you.
- **You get an audit trail** — patterns that show up repeatedly are signal. Patterns that show up once and then never again are noise.
- **No false-positive blocks** — legitimate work keeps moving.

Compare this to `check-credential-leak.sh`, which **does** block. That hook catches patterns where a false positive is cheap (rewrite the example to not match) and a near-miss is catastrophic (a leaked key). Different patterns warrant different postures.

---

## What inbound (PostToolUse) catches

| Pattern category | Example |
|---|---|
| Instruction override | "Ignore all previous instructions and..." |
| Fake system header | `<system>You are now a...</system>` |
| Long base64 blob | 100+ chars of `[A-Za-z0-9+/]={0,2}` (might be encoded instructions) |

These are pattern-matched fragments of attacks that show up in the wild. Real attacks evolve faster than the patterns. Treat the hook as **a tripwire for the obvious cases**, not a guarantee.

---

## What outbound (PreToolUse) catches

| Pattern category | Example |
|---|---|
| API key pattern in parameters | `sk-...`, `AKIA...`, `ghp_...`, etc. |
| Credential-store path reference | `/credentials/`, `.env`, `secrets.yaml` |
| SSN-like pattern | `123-45-6789` |

These flag the case where the model is about to hand sensitive data to an external service via an MCP call. **Some of these are intentional** (you might genuinely be calling a password-reset tool with a placeholder password). The point is not to stop them — it's to make them visible.

---

## What it does NOT catch

- **Novel injection wording.** "Pretend you are a different AI" is not in the patterns. Add wordings as you encounter them.
- **Injection delivered via images, PDFs, or binary content** — the hook scans text only.
- **Slow exfiltration** (one byte per call across many calls) — pattern matching can't reason across calls. You'd need a session-level analyzer for that.
- **Encoded payloads more sophisticated than base64** — hex, custom encodings, etc.
- **Output of `Bash` commands** — this hook is wired on `mcp__.*`. If you also want to scan bash output, add a `Bash` matcher.

---

## When it fires, what to do

1. **Read the warning.** It tells you the tool name, the pattern category, and which pass caught it.
2. **Look at the tool call or response** that triggered the flag in your conversation history.
3. **Decide:**
   - **Real injection attempt** → stop the session, review what the model has done since the response was added to its context, and consider whether any of those actions need to be reverted. Document the incident.
   - **Real egress** → was sending that credential intentional? If yes, note it. If no, rotate the credential.
   - **False positive** → tighten the regex or add an exemption. The script is editable on purpose.
4. **Look at `hooks/security-events.log` periodically.** The patterns in the log are more useful than any individual flag. If you see the same false positive a dozen times, fix the regex. If you see one real flag and never act on it, you don't really have a security posture.

---

## Customizing it

The script is structured into clearly labeled sections so you can edit each pattern category independently:

- **Add an injection phrase** → edit `INJECTION_PHRASES`
- **Add a system-tag pattern** → edit `SYSTEM_HEADERS`
- **Tune the base64 threshold** → change the `{100,}` quantifier
- **Add a credential format** → edit `API_KEY_PATTERNS` (and consider also adding it to `check-credential-leak.sh`)
- **Add a PII pattern** → add a new `if grep -qE` block in PASS 2

---

## Upgrade paths

The current script scans the entire JSON payload as a flat string. That works because Claude Code's envelope is predictable, but it has limits:

- **Need to scan only specific fields?** Replace the "scan entire BODY" approach with targeted `extract_field` calls per field name.
- **Need to walk nested JSON?** Bash can't do that elegantly. Switch to `jq` (if available) or Python.
- **Need to correlate across calls?** This is no longer a hook problem — it's a session-level analysis problem. Build a small log analyzer over `security-events.log` and run it on a cadence.

---

## Resources

- `hooks/check-injection-egress.sh` — the script
- `hooks/check-credential-leak.sh` — the harder-line cousin (blocks instead of flags)
- `docs/day-2/using-hooks.md` — broader hooks workflow and platform guidance
- `docs/reference/anti-patterns.md` — the NO-HEDGING / PROOF discipline gate skills enforce alongside this hook
