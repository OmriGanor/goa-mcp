# Goa — agent operating notes

These notes apply to **any** AI agent driving Goa over MCP (server `goa`):
Claude Code, Cursor, or any other MCP client. They follow the cross-tool
[`AGENTS.md`](https://agents.md) convention. (`CLAUDE.md` in this repo is a
thin pointer to this file for Claude Code.)

Goa delivers its full, evolving operating guide **live from the server** — the
asset / timeline / project contracts and the paid two-call approval flow arrive
as the MCP connection `instructions`. If you don't see them, call the
`get_instructions` tool, and read deeper guidance on demand with `read_skill(...)`
and `list_models(...)`. So this file stays short on purpose.

## Setup

Set the `GOA_PAT` environment variable to your Goa personal access token
(generate one in the Goa web app → **Account → Connect Claude Code**).

## Spending (read this)

Goa's paid tools cost real money. They use a **two-call approval gate**: the
first call (no token) returns a `cost_estimate` + an `approval_token`; calling
again with that token actually spends.

- **Keep the paid tools permission-gated** — do **not** blanket-allowlist
  `mcp__goa__*`. If both calls run with no human in the loop, a prompt-injection
  the agent reads (a malicious asset caption, a fetched page) could spend
  autonomously.
- **Always surface the `cost_estimate` to the user and get their go-ahead**
  before re-calling with the approval token.
- Spending is bounded server-side by your credit balance and a daily cap, so
  the guarantee is **bounded spend**, not per-action consent. Keep the cap low.
