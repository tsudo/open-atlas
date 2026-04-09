---
atlas_tier: framework
title: Hooks — Design Notes for v1.2
doc_type: doc
status: reviewed
created: 2026-04-07
updated: 2026-04-07
owner: open-atlas
tags: [hooks, design-notes, v1.2-preview]
---

# Hooks — Design Notes for v1.2

> **v1.1 ships hook designs, not working scripts.** This directory contains explainers describing the three hooks Open Atlas plans to ship as working scripts in v1.2. Each explainer covers what the hook should catch, why, when it should block vs flag, and how to wire it. If you want to wire your own version of any of these hooks now, the explainers tell you what to enforce and why.

---

## Why design-notes-only in v1.1

The original v1.1 plan included three working bash scripts. Two things stopped them from shipping:

1. **Portability gaps.** Bash + Git Bash work on most systems, but the parsing approach (naive sed-based JSON extraction) has known edge cases under unusual escaping. Shipping known-fragile code with the v1.1 label is a worse user experience than shipping no code at all.
2. **No test harness yet.** Hooks need sample inputs, expected outputs, and regression cases. Without those, every user becomes the QA team. v1.2 will ship the harness alongside the working scripts.

The explainers are still worth shipping on their own. They're the design contract for what v1.2 will deliver.

---

## What's in this directory

| File | What it covers |
|---|---|
| `check-credential-leak.md` | Blocks Write/Edit calls containing API keys, tokens, hardcoded passwords. Block-on-detect posture. |
| `check-injection-egress.md` | Flags prompt-injection patterns in MCP tool responses; flags credential/PII patterns in MCP tool calls. Flag-not-block posture. |
| `check-pre-action.md` | Flags convention slip-ups (plan output location, template-load reminder, pre-build discipline). Flag-not-block posture. |

Each explainer is structured the same way: what it catches, what it does NOT catch, when-it-fires playbook, customization, upgrade paths. Read them as design specs.

---

## If you want to wire your own version now

You don't need to wait for v1.2. Each explainer describes:

- The exact patterns the hook should match
- The exemption logic
- The block-vs-flag posture and why
- The audit-log shape

Pick your scripting language (bash, PowerShell, Python — anything that can read stdin and exit with a code), implement against the explainer, wire it into your AI tool's hook system. The conventions are the value; the implementation is whatever runs on your machine.

---

## What v1.2 will add

- Working bash scripts implementing each design
- A test harness with sample inputs and regression cases
- A community-contributed PowerShell port if a PR lands
- Wiring snippets for Claude Code and (where applicable) Cursor / Codex

If you'd like to influence the v1.2 hook design, open an issue.

---

*Hooks ship as designs in v1.1, working code in v1.2. The discipline lives in the explainers.*
