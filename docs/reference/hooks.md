---
title: Pre-Execution Hooks
doc_type: reference
status: reviewed
created: 2026-03-31
updated: 2026-04-07
owner: open-atlas
tags: [reference, hooks, security, governance, claude-code]
atlas_tier: framework
---

# Pre-Execution Hooks

> **v1.1 status:** Open Atlas v1.1 ships hook *designs* (explainers in the `hooks/` directory at the repo root), not working scripts. Working scripts and a test harness ship in v1.2. This reference doc describes the hook concept and the discipline hooks enforce — the patterns below are the design contract that v1.2 will deliver, and that you can implement in your own scripting language now if you want.

---

## What Are Hooks?

Hooks are pre-execution checks that run before your AI tool writes or edits files. They intercept tool calls at the boundary — after the AI decides what to do, but before the action takes effect.

The distinction matters: documentation tells the AI what it should do. Hooks enforce what it can do. A well-written CLAUDE.md might say "don't modify governance files." A hook makes it impossible.

**Important framing:** Hooks are mechanical guardrails on top of conventions. They are not a substitute for convention discipline, and they do not catch novel mistakes — only the patterns you've explicitly programmed them to detect. Without hooks, the rest of Open Atlas is conventions enforced by you and your AI together: useful, but not bulletproof. With hooks, you get a backstop on the patterns that matter most to you.

## What Hooks Protect

Hooks are most useful for three categories of protection:

**Prompt-injection and credential egress.** The highest-leverage modern use of hooks is scanning MCP tool input/output for prompt injection patterns (instruction-override attempts, suspicious base64 payloads, role-injection) and outbound data egress (API keys leaving in tool responses, credential paths in logs). This is the area where hooks earn their place fastest, because the failure mode is invisible without them.

**Credential leaks.** Scan file content before writes to catch API keys, tokens, or secrets being written to non-secret files. Catches mistakes that `.gitignore` only handles after the fact.

**Governance files.** Mark directories or files as read-only so the AI cannot modify its own operating rules, even if prompted to do so. This prevents instruction drift across long sessions.

**Directory freezes.** Limit edits to a specific scope during focused work. If you're working in `src/api/`, a freeze hook can block writes to `src/ui/` to prevent scope creep.

## Example: Prompt-Injection and Egress Detection

A Claude Code hook that scans MCP tool input and output for injection patterns (inbound) and credential egress (outbound). This is the example to lead with because the failure mode is the most invisible without enforcement.

**`.claude/settings.json`:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "mcp__.*",
        "command": "bash hooks/check-injection-egress.sh"
      }
    ],
    "PostToolUse": [
      {
        "matcher": "mcp__.*",
        "command": "bash hooks/check-injection-egress.sh"
      }
    ]
  }
}
```

**`hooks/check-injection-egress.sh`** (conceptual — see `hooks/check-injection-egress.sh` in the repo for the working version):

```bash
#!/usr/bin/env bash
# Scan MCP tool input/output for injection patterns and credential egress.
# Hook receives tool input as JSON on stdin.

INPUT=$(cat)
CONTENT=$(echo "$INPUT" | jq -r '.content // .input // .output // empty')

# Inbound: prompt injection patterns
if echo "$CONTENT" | grep -qiE '(ignore (previous|prior|all) instructions|system: you are|<\|im_start\|>)'; then
  echo "FLAGGED: possible prompt injection in MCP tool I/O" >&2
  exit 1
fi

# Outbound: credential egress
if echo "$CONTENT" | grep -qE '(sk-[a-zA-Z0-9]{20,}|AKIA[A-Z0-9]{16}|ghp_[a-zA-Z0-9]{36})'; then
  echo "FLAGGED: possible credential egress in MCP tool output" >&2
  exit 1
fi

exit 0
```

This is intentionally simple. Real injection detection should cover the patterns specific to your toolchain and threat model. The point is the pattern: intercept at the MCP boundary, scan content, flag or block when you see something you don't trust.

## Example: Governance File Protection

A Claude Code hook that blocks writes to any file under a `governance/` directory.

**`.claude/settings.json`:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "command": "bash hooks/protect-governance.sh"
      }
    ]
  }
}
```

**`hooks/protect-governance.sh`:**

```bash
#!/usr/bin/env bash
# Block writes to governance directory.
# Hook receives tool input as JSON on stdin.

INPUT=$(cat)
FILE_PATH=$(echo "$INPUT" | jq -r '.file_path // .path // empty')

if [[ "$FILE_PATH" == *governance/* ]]; then
  echo "BLOCKED: governance/ is read-only. These files cannot be modified during a session." >&2
  exit 1
fi

exit 0
```

When the hook exits with a non-zero status, the tool call is blocked and the error message is shown to the AI. The AI can then explain the restriction to the user rather than silently failing.

## Example: Credential Leak Detection on Writes

A simpler hook that scans write content for common API key patterns before allowing the write to proceed.

```bash
#!/usr/bin/env bash
# Scan for common secret patterns in file content.

INPUT=$(cat)
CONTENT=$(echo "$INPUT" | jq -r '.content // empty')

# Common API key patterns
if echo "$CONTENT" | grep -qE '(sk-[a-zA-Z0-9]{20,}|AKIA[A-Z0-9]{16}|ghp_[a-zA-Z0-9]{36})'; then
  echo "BLOCKED: Content appears to contain an API key or secret. Use environment variables or a secrets manager instead." >&2
  exit 1
fi

exit 0
```

This is intentionally simple. Real credential detection should cover your specific providers and secret formats. See `hooks/check-credential-leak.sh` in the Open Atlas repo for a working version with thorough comments.

## Tool-Specific Notes

**Claude Code** — PreToolUse and PostToolUse hooks in `.claude/settings.json`. Hooks receive tool input as JSON on stdin and block execution on non-zero exit. This is the most mature hook implementation currently available.

**Cursor** — Custom rules can approximate some hook behavior, but there is no formal pre-execution hook system. Rules are suggestions, not enforcement.

**Codex** — Uses a sandbox model where the AI operates in an isolated environment. Hooks are not yet supported, but the sandbox itself provides some protection.

**Generic fallback** — Pre-commit git hooks catch problems at commit time rather than write time. Less immediate, but works with any tool. Use `pre-commit` hooks to scan for secrets, validate file paths, or enforce directory restrictions before code enters version control.

---

*This is a pattern, not a product. Adapt for your tool and risk model. The Open Atlas repo ships three working hook scripts under `hooks/` — see those for ready-to-use implementations.*
