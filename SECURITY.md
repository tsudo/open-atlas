# Security Policy

## Scope

Open Atlas is a markdown-only framework — a set of conventions, templates, and bash hooks. The repo contains:

- Markdown files (governance, templates, docs, examples)
- Bash scripts in `hooks/` (optional, user-wired)
- A YAML manifest (`.atlas-manifest.yml`)
- Static assets (logo, etc.)

There is no application code, no server, no executable runtime, no telemetry, no network calls. The "attack surface" of Open Atlas is whatever you wire it into.

## What counts as a security issue

We treat the following as security issues:

- **A bash hook that fails open** when it should fail closed (e.g., `check-credential-leak.sh` letting a credential pattern through under specific input shapes)
- **A bash hook that executes attacker-controlled content** as code (e.g., a parsing bug that turns input into a shell command)
- **Documentation that recommends an unsafe pattern** as if it were safe (e.g., suggesting users disable a hook to "get past it")
- **A template or example that contains an actual credential, real PII, or a path to a real internal system**

## What does NOT count as a security issue

These are real concerns — but they're framework limits, not bugs:

- Hooks not catching novel credential formats. Add the pattern; that's expected maintenance.
- Hooks producing false positives. Tune the regex; that's expected maintenance.
- Open Atlas being unable to prevent the AI from doing X via a tool that isn't hooked. Hooks are tripwires for specific tool calls — they don't replace least-privilege.
- The AI ignoring conventions in `workspace/`. Conventions are read by the AI like everything else; if your AI ignores them, that's a model/tool problem, not an Open Atlas vulnerability.

The line: a security issue is something Open Atlas claimed to do and didn't, or did unsafely. It is not "Open Atlas didn't solve a problem it never claimed to solve."

## Reporting

If you find something that fits the "what counts" list:

1. **Do not open a public issue.** Open Atlas is small enough that a public issue is effectively a disclosure.
2. **Open a private security advisory** via GitHub's "Security" tab on the repository, OR email the maintainer (contact in repo metadata).
3. **Include:** what you found, how to reproduce, what you think the impact is, and whether you've tested it against the latest version.

You should expect an acknowledgment within a week. Open Atlas is maintained by a small team; complex issues may take longer to triage.

## What to expect

- **Confirmed issues** get fixed in the next release with a credit (unless you'd rather stay anonymous) and a note in the release changelog.
- **Disputed issues** get a written reply explaining the reasoning. If you disagree, the conversation can continue — but the maintainer's call is final.
- **Out-of-scope reports** get a polite "thanks, but this isn't a security issue" reply with a pointer to the relevant doc (often [docs/reference/why-this-works.md](docs/reference/why-this-works.md) on the conventions-vs-enforcement principle).

## Workspace content is a prompt-injection surface

Open Atlas's core convention is that the AI reads files in `workspace/` and changes its behavior based on them. By construction, this is a prompt-injection surface: if a malicious actor (or a careless import) gets a file into your workspace, the AI will read it and may act on it.

Treat untrusted workspace content the way you would treat any data the AI sees:

- Be cautious about cloning or merging third-party Open Atlas workspaces. Read what you're adding before you add it.
- The `check-injection-egress` hook design (see `hooks/check-injection-egress.md`) describes flagging injection patterns in MCP tool responses; the working script ships in v1.2. Either way, it does not scan files at rest in the workspace.
- A workspace audit (the `review-context` example skill, run on a cadence) is the right shape for catching content that drifted in.
- If you're sharing a workspace across a team, the same review discipline applies to anything anyone else commits.

This is a property of the design, not a bug. The same convention that makes Open Atlas valuable (the AI reads conventions and follows them) makes the workspace content load-bearing for the AI's behavior. Manage it accordingly.

---

## Honesty about what Open Atlas is

Open Atlas is **not** a security tool. The hooks and gate skills enforce *discipline*, not *policy*. They catch the obvious things deterministically and create an audit trail. They are not a substitute for:

- Least-privilege credentials
- Real secret management (Vault, AWS Secrets Manager, etc.)
- Actual vulnerability scanners (gitleaks, trufflehog, semgrep)
- An organizational AI governance program

If you're using Open Atlas as your *only* line of defense against credential leaks or prompt injection, please add more layers. The hooks are tripwires, not walls.

---

*Open Atlas — CC-BY-4.0. See [LICENSE](LICENSE) for terms.*
