---
atlas_tier: framework
title: Using Hooks
doc_type: doc
status: reviewed
created: 2026-04-07
updated: 2026-04-07
owner: open-atlas
tags: [day-2, hooks, security]
---

# Using Hooks

> **v1.1 status:** Open Atlas v1.1 ships hook *designs* (explainers in `hooks/`), not working scripts. The working scripts and a test harness ship in v1.2. This doc describes the discipline that hooks enforce, the wiring you'll use when v1.2 ships, and how to implement your own version of any of these hooks now if you want. The "is this for you yet?" preflight below applies whether you're planning to wire them in v1.2 or build your own today.

Hooks are scripts that run before or after a tool call. They make conventions enforceable instead of just hopeful.

This doc is for the moment you've decided you want hooks. If you're not sure yet, read the preflight first.

---

## Is this for you yet?

Hooks are the most "infrastructure-y" part of Open Atlas. **You can run a perfectly useful workspace without them.** Skip this entire doc if any of the following is true:

- **You haven't created a single skill yet.** Build a skill first. Hooks make sense after you've felt the gap between "I told the AI not to do X" and "I wish the AI was *physically prevented* from doing X."
- **You're not comfortable reading bash, and you don't have someone who is.** The hooks are written to be readable by a careful non-bash-native, but if you're not going to read them, you shouldn't run them. Hooks you don't understand are a worse problem than no hooks.
- **You're on Windows without Git Bash, WSL, or a willingness to install one.** The scripts are bash. They won't run on bare PowerShell. See [Platform notes](#platform-notes) below.
- **You haven't had a near-miss yet.** The right time to add a hook is *just after* you've caught yourself making a mistake the hook would have prevented. Adding hooks before you've felt the pain produces hooks that don't match your actual risks.

If you read all four and one of them is true, close this file and come back later. That's the right move.

If you read all four and none of them is true — keep going.

---

## What hooks Open Atlas designs

| Hook | Posture | What it catches |
|---|---|---|
| `check-credential-leak` | **Blocks** | API keys, tokens, hardcoded passwords in `Write`/`Edit` calls |
| `check-injection-egress` | **Flags** | Prompt-injection patterns in MCP tool responses; credential/PII patterns in MCP tool parameters |
| `check-pre-action` | **Flags** | Convention slip-ups (plan output location, template-load reminder, pre-build discipline) |

The design notes live at `hooks/check-credential-leak.md`, `hooks/check-injection-egress.md`, and `hooks/check-pre-action.md`. Each one covers what the hook catches, what it does NOT catch, the when-it-fires playbook, customization guidance, and the upgrade path. Working scripts implementing these designs ship in v1.2.

---

## Wiring hooks into Claude Code (when v1.2 ships)

When v1.2 ships the working scripts, hooks will wire into `.claude/settings.local.json` under the `hooks` key. The shape (so you can plan ahead, or implement your own version against the design notes today):

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          { "type": "command", "command": "bash hooks/check-credential-leak.sh" },
          { "type": "command", "command": "bash hooks/check-pre-action.sh" }
        ]
      },
      {
        "matcher": "mcp__.*",
        "hooks": [
          { "type": "command", "command": "bash hooks/check-injection-egress.sh" }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "mcp__.*",
        "hooks": [
          { "type": "command", "command": "bash hooks/check-injection-egress.sh" }
        ]
      }
    ]
  }
}
```

The matcher uses regex. `Write|Edit` runs the hook on either tool. `mcp__.*` runs on any MCP tool call. Adjust to taste.

### Wiring into other AI tools

Hooks are a Claude Code feature. Cursor, Codex, and most other AI tools don't have a direct equivalent yet. If you're not on Claude Code, you have two options:

- **Run the hooks manually** as a pre-commit or CI step. The credential-leak hook in particular makes sense as a pre-commit check.
- **Wait** — most AI tools are converging on hook-like primitives. The bash scripts will be portable when they get there.

---

## How to add your own hook

1. **Identify the slip-up.** Something the AI did that you wish it had refused to do. Be specific. "It wrote a credential to a file" is specific. "It was sloppy" is not.
2. **Write a regex that matches the slip-up.** Test the regex against the actual file content from the incident. If your regex doesn't catch the original incident, it won't catch the next one either.
3. **Decide block vs flag.** If a false positive is cheap and a near-miss is catastrophic → block. If a false positive is expensive and a near-miss is recoverable → flag.
4. **Copy an existing hook as a starting template.** `check-pre-action.sh` is the easiest to extend; `check-credential-leak.sh` is the simplest overall.
5. **Wire it.** Add to `.claude/settings.local.json`.
6. **Test it deliberately.** Try to trip it on purpose. Then try to trip it with the kind of input that *should not* fire it. Both halves of the test matter.

---

## Platform notes

### macOS / Linux

Bash is universal. Hooks just work.

### Windows — Git Bash

If you have Git for Windows, you have bash. Use `bash hooks/check-credential-leak.sh` in your settings.local.json `command` field. Make sure `bash` is on your `PATH` (it usually is after a Git for Windows install).

### Windows — WSL

WSL gives you a real Linux environment. If you're doing serious dev on Windows, this is the best long-term path. Hooks run identically to Linux.

### Windows — bare PowerShell

The scripts won't run. You have three options:

1. Install Git Bash (smallest delta)
2. Install WSL (best long-term)
3. Port the scripts to PowerShell (most work, but a PR is welcome)

---

## Troubleshooting (v1.2)

> The troubleshooting guidance below applies to the working scripts that ship in v1.2. If you've implemented your own version of one of these hooks against the design notes, the same diagnostic patterns apply — adapt the file paths and commands to your scripting language.

### "The hook isn't firing"

- Verify your `.claude/settings.local.json` is valid JSON (Claude Code will silently ignore an invalid file).
- Verify the matcher is correct — `Write|Edit` is regex, not glob.
- Run the hook manually with a sample input: `echo '{"tool_name":"Write","tool_input":{"file_path":"test.md","content":"sk-abc123def456ghi789jkl012mno"}}' | bash hooks/check-credential-leak.sh`. If the hook works manually but not via Claude Code, the wiring is the issue.

### "The hook is firing on something legitimate"

- Read the explainer (`hooks/check-*.md`) for the customization section.
- Tighten the regex, add an exemption, or rewrite the legitimate content so it doesn't match.
- **Do not just disable the hook.** That's how you end up with a hook that catches nothing.

### "The hook is blocking everything"

- This usually means a parsing bug — the hook is misreading the JSON envelope.
- Run the hook manually with a sample input (see above). If it fails on a known-good input, the issue is the script.
- Worst case: comment out the hook line in `settings.local.json` until you've fixed the script.

---

## Where to go next

- **Hook orientation and the full wiring snippet** → `hooks/README.md`
- **Per-hook explainers** → `hooks/check-credential-leak.md`, `hooks/check-injection-egress.md`, `hooks/check-pre-action.md`
- **The discipline hooks enforce in code** → [../reference/anti-patterns.md](../reference/anti-patterns.md)
- **Why Open Atlas separates hooks (deterministic) from skills (judgment)** → [../reference/why-this-works.md](../reference/why-this-works.md)
