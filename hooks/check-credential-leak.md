# check-credential-leak — Explainer

> **v1.1 status: design notes only.** This file is the design specification for a hook Open Atlas plans to ship as a working script in v1.2. The patterns, postures, and customization guidance below are the design contract. If you want to implement this hook now in your own scripting language, this file tells you what to enforce and why. See [README.md](README.md) for the v1.1 → v1.2 framing.

---

## What it does

Scans every `Write` and `Edit` tool call before it executes. If the content contains anything that looks like an API key, token, private key, or hardcoded password, the hook **blocks the call** and tells the model what tripped.

This is the highest-value hook in Open Atlas. If you only ever wire one hook, wire this one.

---

## What it catches

| Pattern | Example |
|---|---|
| OpenAI / Anthropic-style key | `sk-proj-abc123def456...` |
| AWS access key ID | `AKIAIOSFODNN7EXAMPLE` |
| GitHub PAT | `ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` |
| GitLab PAT | `glpat-xxxxxxxxxxxxxxxxxxxx` |
| Slack token | `xoxb-1234-5678-abcdef` |
| Hardcoded `api_key = "..."` | `api_key = "abc12345"` |
| Hardcoded `password = "..."` | `password: "hunter22"` |
| Private key header | `-----BEGIN RSA PRIVATE KEY-----` |

---

## What it does NOT catch

Be honest about the limits:

- **Novel credential formats.** A new vendor's token format won't match until you add it. The patterns are explicit, not heuristic.
- **Obfuscated keys.** A key split across two lines, base64-encoded, or interpolated from variables will slip through.
- **Credentials in `Bash` heredocs.** This hook only watches `Write` and `Edit`. If you let the model write files via `bash -c "cat > file.env"`, you need to also hook `Bash` (or stop letting the model do that).
- **Credentials already in the workspace.** This is a tripwire on *new writes*, not an audit of existing files. Use a separate scanner for that (e.g., `gitleaks`, `trufflehog`).

---

## Exemptions (paths the hook skips)

By design, some paths are *expected* to contain secrets and should not be blocked:

- Anything under `*/credentials/*`
- `.env` and `.env.*` files

If you want to enforce that the model **cannot** write to these paths either, remove the exemptions in the script. That's a stricter posture and a valid choice — but make sure you have a way to manage credentials some other way before you do it.

---

## When it fires, what to do

1. **Read the path it blocked.** The hook tells you `BLOCKED: credential pattern detected in [path]`.
2. **Look at what the model was trying to write.** It's in your conversation history immediately above the block.
3. **Decide:** is this a real credential that should never have been in the file, or a false positive (e.g., a pattern in documentation)?
   - **Real:** route the value to an environment variable or your credential store, and ask the model to retry with a placeholder.
   - **False positive:** add a more specific exemption in the script, or rewrite the example so it doesn't match the pattern (e.g., `sk-EXAMPLE` instead of `sk-abc123def456...`).
4. **Never just disable the hook to get past it.** That defeats the purpose. Investigate, fix, and re-run.

---

## Customizing it for your workspace

The script is written to be edited. Common changes:

- **Add a new credential format.** Edit the `PATTERNS` variable in `check-credential-leak.sh`. One regex per format. Add the format to the table in this explainer too.
- **Add an exempt path.** Edit the `case "$NORM_PATH"` block. Use forward-slash paths even on Windows — the script normalizes for you.
- **Change the block message.** Edit the `echo "BLOCKED: ..."` line. Keep it short and actionable; the model has to reason about it.

---

## Upgrade paths

The current script uses bash + sed for portability. If you outgrow it:

- **Need real JSON parsing?** Replace the `extract_field` function with `jq` or Python (`py -c "import sys, json; ..."`). This is what AIOS — the internal workspace this hook was derived from — does.
- **Need to scan more fields?** Right now we only look at `content` and `new_string`. Add other fields by pulling them with another `extract_field` call and concatenating them into `BODY`.
- **Need to log every block?** Add an `echo` redirect to a log file before the `exit 2` line. The block message is already structured enough to grep later.

---

## Why the hook blocks instead of warning

Credentials in source-controlled files are a hard "no." There is no graceful recovery from "the API key is in the git history" — you have to rotate the key, scrub the history, and explain it to whoever notices first. A flag-and-continue posture would only delay the problem.

For checks where the cost of a false positive is higher than the cost of a near-miss (e.g., prompt injection detection), see `check-injection-egress.md` — that one flags instead of blocks, and explains why.

---

## Resources

- `hooks/check-credential-leak.sh` — the script itself
- `hooks/README.md` — hooks orientation and wiring
- `docs/day-2/using-hooks.md` — broader hooks workflow
- `docs/reference/anti-patterns.md` — the discipline this hook enforces in code form
