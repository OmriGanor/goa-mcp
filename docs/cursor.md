# Connect Cursor to Goa

## 1. Get a token

Goa web app → **Account → Connect Claude Code → Generate token** (this screen
mints tokens for any client, Cursor included). Copy the `goa_pat_…` (shown once)
and set a per-token daily spend cap. Make it available to Cursor as the `GOA_PAT`
environment variable (Cursor interpolates `${env:…}` in MCP config).

```bash
export GOA_PAT=goa_pat_xxxxxxxx…   # in your shell profile / launch env
```

## 2. Add the server

**One-click** — paste into your browser (opens Cursor and prefills the config):

```
cursor://anysphere.cursor-deeplink/mcp/install?name=goa&config=eyJ1cmwiOiJodHRwczovL2FwaS5nZXRnb2EuaW8vbWNwL2FnZW50LyIsImhlYWRlcnMiOnsiQXV0aG9yaXphdGlvbiI6IkJlYXJlciAke2VudjpHT0FfUEFUfSJ9fQ==
```

**Or manually** — add to `.cursor/mcp.json` (this project) or `~/.cursor/mcp.json`
(everywhere):

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

`${env:GOA_PAT}` keeps the token out of the committed file — Cursor reads it
from your environment at runtime.

## 3. Verify

In Cursor's agent, enable the `goa` server, then ask *"list my Goa projects"* or
*"what video models can you use?"*. The agent already carries Goa's operating
guide (delivered as the MCP connection `instructions`).

If `list_projects` is empty, create a project in the Goa web app first.

## Spending — keep the paid tools gated

Paid tools self-approve over MCP (two-call gate; the second call spends). Keep
Goa's paid tools behind Cursor's tool-approval prompt — don't auto-run them.
Spending is bounded by your credit balance and your token's daily cap, not by
per-action consent. See [`../AGENTS.md`](../AGENTS.md).

## Self-hosting

Point `url` at `https://<your-host>/mcp/agent/` (keep the trailing slash).
