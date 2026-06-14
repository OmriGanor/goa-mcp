# Goa — connect your AI agent to your reel studio

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
&nbsp;![MCP](https://img.shields.io/badge/MCP-remote_server-blue)
&nbsp;![plugin](https://img.shields.io/badge/plugin-v0.1.0-blue)

Drive [Goa](https://getgoa.io) from **your own AI agent** and generate videos — images, voiceovers, talking-heads, motion-graphics compositions. Use a timeline to edit scenes. Your agent (Claude Code, Cursor, …) is the orchestrator; Goa's paid engines do the generation, billed against your Goa credit balance.

It's one authenticated **remote MCP server** — `https://api.getgoa.io/mcp/agent/`.
Connect to that endpoint. Goa's whole operating
guide (asset / timeline / project contracts, model guidance, skills) is served
**live** by the server, so the config you add is a thin, near-static wiring
layer that rarely changes.

## What you can do

Tools are **served live by the server and may change — the server is the source
of truth** (your agent fetches them on connect; see `get_instructions`). At a
glance, Goa exposes:

- **Projects** — list/create/update reel projects (`list_projects`, …).
- **Models & voices** — discover available engines and pick one (`list_models`, `list_voices`).
- **Generation** (paid) — images, voiceovers, talking-heads, and video clips.
- **Timeline & composition** — assemble scenes and export the finished reel.
- **Skills** — on-demand guidance the agent reads as it works (`read_skill`).
- **Jobs** — track long-running generations (`list_jobs`, `get_job`).

Paid tools are gated by a two-call approval flow — see [Spending & security](#spending--security).

## 1. Get a token

In the Goa web app: **Account → Connect Claude Code → Generate token**. Copy
the `goa_pat_…` value (shown once) and set a per-token daily spend cap.

```bash
export GOA_PAT=goa_pat_xxxxxxxx…   # add to your shell profile to persist
```

> A token grants full read/write access to your Goa account until revoked —
> treat it like a password. Revoke any time from the same screen.

## 2. Connect your client

### Claude Code

```bash
# Plugin (recommended):
/plugin marketplace add OmriGanor/goa-mcp
/plugin install goa@goa

# …or the one-liner, no repo needed:
claude mcp add --transport http goa https://api.getgoa.io/mcp/agent/ \
  --header "Authorization: Bearer $GOA_PAT"
```

Full walkthrough: [`docs/claude-code.md`](docs/claude-code.md).

### Cursor

One-click — paste this into your browser (it opens Cursor):

```
cursor://anysphere.cursor-deeplink/mcp/install?name=goa&config=eyJ1cmwiOiJodHRwczovL2FwaS5nZXRnb2EuaW8vbWNwL2FnZW50LyIsImhlYWRlcnMiOnsiQXV0aG9yaXphdGlvbiI6IkJlYXJlciAke2VudjpHT0FfUEFUfSJ9fQ==
```

…or add it to `.cursor/mcp.json` (project) or `~/.cursor/mcp.json` (global):

```json
{
  "mcpServers": {
    "goa": {
      "url": "https://api.getgoa.io/mcp/agent/",
      "headers": { "Authorization": "Bearer ${env:GOA_PAT}" }
    }
  }
}
```

Full walkthrough: [`docs/cursor.md`](docs/cursor.md).

### VS Code

[![Install in VS Code](https://img.shields.io/badge/VS_Code-Install_Server-0098FF?style=flat-square&logo=visualstudiocode&logoColor=white)](https://insiders.vscode.dev/redirect/mcp/install?name=goa&inputs=%5B%7B%22id%22%3A%22goa_pat%22%2C%22type%22%3A%22promptString%22%2C%22description%22%3A%22Goa%20personal%20access%20token%20%28goa_pat_...%29%22%2C%22password%22%3Atrue%7D%5D&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fapi.getgoa.io%2Fmcp%2Fagent%2F%22%2C%22headers%22%3A%7B%22Authorization%22%3A%22Bearer%20%24%7Binput%3Agoa_pat%7D%22%7D%7D)

The button prompts for your token and stores it as a secret `input` (not in
plaintext). Or add it manually to `.vscode/mcp.json` — note VS Code uses a
`servers` key (**not** `mcpServers`):

```json
{
  "servers": {
    "goa": {
      "type": "http",
      "url": "https://api.getgoa.io/mcp/agent/",
      "headers": { "Authorization": "Bearer ${input:goa_pat}" }
    }
  },
  "inputs": [
    { "id": "goa_pat", "type": "promptString", "description": "Goa PAT", "password": true }
  ]
}
```

### Other MCP clients (Windsurf, …)

Any client that lets you set a custom `Authorization` header on a remote MCP
server works: point it at `https://api.getgoa.io/mcp/agent/` with the header
`Authorization: Bearer <your GOA_PAT>`.

## 3. Verify

Ask your agent *"list my Goa projects"* or *"what video models can you use?"*.
The agent already carries Goa's operating guide (delivered as the MCP
connection `instructions`; also available via the `get_instructions` tool).

> **You need a project first.** Project creation is a web-app action — if
> `list_projects` is empty, create one in the Goa web app, then point the
> agent at it.

## Which file does my client read?

There is **no universal MCP config file** — every client reads its own path. The
`{ "mcpServers": … }` *schema* is shared by several, but the filename/location is
client-specific, so don't assume one file works everywhere.

| Path | Read by | Notes |
|---|---|---|
| `.mcp.json` | **Claude Code only** | Both this plugin's bundled server config and Claude Code's project-scope config. Other clients ignore it. |
| `.claude-plugin/` | **Claude Code only** | Plugin + marketplace manifest (`/plugin install goa@goa`). |
| `.cursor/mcp.json` | **Cursor** | Same `mcpServers` schema, different path. |
| *(VS Code)* `.vscode/mcp.json` | **VS Code** | Uses a `servers` key, **not** `mcpServers`. Not shipped here — see the [VS Code](#vs-code) section. |
| `AGENTS.md` | Cursor, Codex, Copilot, Windsurf | Cross-tool agent operating notes. |
| `CLAUDE.md` | Claude Code | Thin pointer to `AGENTS.md`. |
| `README.md`, `docs/` | humans | Per-client setup walkthroughs. |

## Spending & security

Goa's paid tools follow a **two-call approval gate**: the first call (no token)
returns a `cost_estimate` + an `approval_token`; the agent re-calls with that
token to actually spend.

- The agent **self-approves** by re-calling — there's no separate web approval
  card on this path, so your consent moment is **your client's own permission
  prompt**.
- **Do not blanket-allowlist `mcp__goa__*`.** If the paid tools run with no
  human in the loop, a prompt-injection your agent reads (a malicious asset
  caption, a fetched page) could spend autonomously. Keep them permission-gated.
- Spending is bounded server-side by your **credit balance** and a **daily cap**
  (the lower of the global cap and your token's). The honest guarantee is
  **bounded spend**, not per-action consent — so keep the cap low. Note the cap
  is soft under heavy concurrency, and multiple tokens share one user-wide
  budget (they're not additive).
- **Auth today is a bearer token (PAT).** A scoped OAuth connect flow is on the
  roadmap; until then, set a low daily cap and store the token via your client's
  secret/env mechanism (never in a committed file).

See [`AGENTS.md`](AGENTS.md) for the agent-facing version of these rules.

To report a security issue, see [`SECURITY.md`](SECURITY.md).


## License

[MIT](LICENSE).
