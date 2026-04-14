# Integration Management

How to track the external services your workspace depends on — and keep that inventory honest across AI tools.

---

## The problem

Your workspace uses external services: task managers, version control, DNS providers, deployment platforms, API endpoints. Each AI tool you use (Claude Code, Codex, Cursor, etc.) has its own way of registering those services — MCP servers, config files, plugins, environment variables.

When you add a new AI tool, you configure it for the services you remember. The ones you forget stay unconfigured. When a service breaks, you troubleshoot the tool-specific config without a single source of truth to check against.

The fix is a logical inventory that exists independently of any tool's configuration.

---

## The inventory pattern

Maintain one file that lists every external service your workspace uses. For each service, record what it does, how you authenticate, what happens when it's unavailable, and which AI tools have access.

The inventory is the source of truth. Tool-specific configurations (MCP server lists, API key files, plugin configs) are projections of the inventory — they should reflect it, not replace it.

### Suggested format

A simple markdown table in your workspace governance directory:

```markdown
# Integration Inventory

| Service | Purpose | Auth | Fallback | Tools |
|---|---|---|---|---|
| GitHub | Version control, PRs, releases | OAuth token | Work locally, push later | Claude, Codex |
| Todoist | Task tracking | API key | Track tasks in local markdown | Claude |
| Cloudflare | DNS, deploys | OAuth / API token | Manual DNS changes via dashboard | Claude |
| Pinboard | Bookmark ingestion | API token | Skip bookmark sync | Claude |
```

### Fields

| Field | What to record |
|---|---|
| **Service** | The service name |
| **Purpose** | What your workspace uses it for (one line) |
| **Auth** | How you authenticate: OAuth, API key, token, none |
| **Fallback** | What happens when the service is unavailable. If the answer is "nothing works," that's a risk worth noting. |
| **Tools** | Which AI tools have this service registered and working |

---

## Health states

An integration can be in one of four states:

| State | Meaning |
|---|---|
| **Supported** | Listed in the inventory as something the workspace uses |
| **Registered** | Configured in a specific AI tool's settings |
| **Authenticated** | Credentials are present and valid |
| **Healthy** | Tested and working — queries succeed, writes land |

These states aren't binary across tools. A service might be supported + registered + authenticated + healthy in Claude Code but only supported + registered in Codex (auth pending). The inventory makes these gaps visible.

---

## When to update the inventory

- **New service:** Add it to the inventory before configuring it in any tool. Inventory first, config second.
- **New AI tool:** Walk the inventory and register each service in the new tool. The inventory tells you what to configure; without it, you're working from memory.
- **Service outage or auth failure:** Check the inventory for the fallback plan. If there isn't one, add it now while you're thinking about it.
- **Auth rotation:** Update the auth method in the inventory when credentials change. Then update each tool's config to match.
- **Service retirement:** Mark the service as retired in the inventory. Remove it from tool configs. Don't just delete the inventory entry — note why it was retired so future-you doesn't re-add it.

---

## What this is NOT

- **Not a secrets store.** The inventory records *that* you authenticate with an API key, not the key itself. Credentials go in your tool's secure storage, not in a markdown file.
- **Not a tool-specific config.** The inventory is tool-agnostic. It says "Todoist is used for task tracking with an API key." Your Claude MCP config and your Codex config both project that into their own formats.
- **Not required at setup.** If you use one AI tool with two services, the inventory adds overhead without value. It becomes useful when you have 4+ services or 2+ AI tools — the point where memory-based tracking starts to fail.

---

*Open Atlas v1.2. See [reliability-tiers.md](reliability-tiers.md) for how integration configuration fits into the enforcement spectrum (typically Tier 1 or Tier 0.5, depending on where you store the inventory).*
