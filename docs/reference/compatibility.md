---
atlas_tier: framework
title: Compatibility — What Works Where
doc_type: doc
status: reviewed
created: 2026-04-07
updated: 2026-04-07
owner: open-atlas
tags: [reference, compatibility, tools, platforms]
---

# Compatibility — What Works Where

An honest table of what Open Atlas features work in which AI tools, on which platforms. No marketing — actual support.

---

## AI tools

| Feature | Claude Code | Cursor | Codex | Generic AI (any tool that reads files) |
|---|---|---|---|---|
| **Workspace structure** (read files, follow conventions) | ✅ | ✅ | ✅ | ✅ |
| **Personas** (read files manually or via context config) | ✅ | ✅ | ✅ | ✅ |
| **Skills** (read files manually) | ✅ | ✅ | ✅ | ✅ |
| **Skills via slash command** | ✅ (settings.local.json) | ⚠️ (no native equivalent) | ⚠️ (varies) | ❌ |
| **Skills via auto-loaded context** | ✅ (.claude/CLAUDE.md) | ✅ (.cursorrules) | ✅ (AGENTS.md) | ⚠️ (depends on tool) |
| **Hotword routing** | ✅ (with slash command) | ⚠️ (manual invocation) | ⚠️ (manual invocation) | ⚠️ (manual invocation) |
| **Hooks** | ✅ (PreToolUse / PostToolUse) | ❌ (no native equivalent) | ❌ (no native equivalent) | ❌ |
| **Memory / persistence** | ✅ (auto-memory system) | ⚠️ (.cursorrules as static memory) | ⚠️ (AGENTS.md as static memory) | ❌ (you provide context each session) |
| **Templates** (read and follow) | ✅ | ✅ | ✅ | ✅ |
| **Manifest** (tier tags) | ✅ (informational) | ✅ (informational) | ✅ (informational) | ✅ (informational) |

**Legend:** ✅ works natively · ⚠️ works with caveats · ❌ not supported

### What "works with caveats" means

- **Cursor / Codex skill invocation:** there's no slash command equivalent. You invoke skills by telling the AI to read the skill file at the start of a session, or by referencing the skill in `.cursorrules` / `AGENTS.md` so it's always in context.
- **Hotword routing in Cursor / Codex:** the AI can recognize the hotword if the relevant skill file is in context, but there's no automatic dispatcher. Manual invocation (paste the hotword + the file path) works.
- **Cursor / Codex memory:** these tools use a single static config file as their persistent context. You can put a "remember these conventions" section in there, but there's no append-on-correction loop the way Claude Code's auto-memory has.

### The honest summary

**Open Atlas was designed in Claude Code and is most fully featured there.** Hooks in particular are a Claude Code feature. If you're on Cursor or Codex, you get the workspace structure, personas, skills, and templates — most of the value. You don't get hooks. You can build hooks as a pre-commit step in git instead.

If you're using a different tool entirely, the workspace structure and the markdown files still work — you'll be doing more manual "read this file" instructions to the AI, but the discipline holds.

---

## Operating systems

| Feature | macOS | Linux | Windows + Git Bash | Windows + WSL | Windows + bare PowerShell |
|---|---|---|---|---|---|
| **Workspace structure** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Personas / skills / templates** (markdown) | ✅ | ✅ | ✅ | ✅ | ✅ |
| **Hooks (bash)** | ✅ | ✅ | ✅ | ✅ | ❌ |
| **Path conventions** (forward slash, no spaces) | ✅ | ✅ | ✅ (normalized) | ✅ | ⚠️ (tools may need backslash) |

### Windows guidance

If you're on Windows, you have three paths:

1. **Git Bash** (smallest install) — comes with Git for Windows. Hooks just work. Recommended for most users.
2. **WSL** (best long-term) — if you're doing serious dev work on Windows, you probably want this anyway. Hooks run identically to Linux.
3. **Bare PowerShell** — the markdown side of Open Atlas works fine. Hooks won't run. Either install Git Bash, install WSL, or skip the hooks directory entirely.

The Open Atlas hooks are written in bash deliberately — bash is universal where it's available, and the alternative (writing them as polyglot scripts that run on PowerShell *and* bash) makes the scripts harder to read for the larger non-PowerShell audience. If a PowerShell port shows up as a community PR, that'd be welcome.

---

## What's NOT compatible

Be explicit about the things Open Atlas does not support:

- **No vendor lock-in to a specific AI provider.** Open Atlas works with any tool that can read markdown files. Anthropic, OpenAI, Google, local models — doesn't matter.
- **No required IDE.** The workspace is a directory. Open it in any editor.
- **No required dependency runtime.** No Python, no Node, no Ruby. Bash for the hooks (if you want them), markdown for everything else.
- **No central server.** Open Atlas is local. Nothing phones home.
- **No telemetry.** POSITIVE_SIGNAL events are local log lines, not analytics.

---

## What you might be missing

If you're coming from a more opinionated framework, here's what Open Atlas deliberately *doesn't* include:

- **No agent framework** (LangChain, AutoGen, CrewAI). Open Atlas governs how an AI works in a workspace, not how the AI is built.
- **No vector store integration.** Files are markdown. Search is whatever your tool provides.
- **No execution sandbox.** Hooks are tripwires, not jails.
- **No multi-user support.** Open Atlas is single-user by design. Team adoption means each person has their own copy + a shared convention set.
- **No project management.** No Kanban, no calendars, no scheduling. Use a real PM tool.

These are deliberate omissions, not gaps. Adding them would make Open Atlas a heavier framework with more failure modes.

---

## Where to go next

- **Wiring per AI tool** → [../day-2/creating-skills.md](../day-2/creating-skills.md) (Wiring section)
- **Hooks platform notes** → [../day-2/using-hooks.md](../day-2/using-hooks.md)
- **Why Open Atlas avoids vendor lock-in** → [why-this-works.md](why-this-works.md)
