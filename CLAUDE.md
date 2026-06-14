# Goa (Claude Code)

This repo follows **[`AGENTS.md`](./AGENTS.md)** — read it for the operating
notes (setup + spending rules). Claude Code doesn't auto-load `AGENTS.md` yet,
so the essentials are restated here:

- Goa delivers its full operating guide **live** as the MCP connection
  `instructions`. If you don't see them, call `get_instructions`; read deeper
  guidance with `read_skill(...)` and `list_models(...)`.
- Set the `GOA_PAT` environment variable to your Goa personal access token
  (Goa web app → **Account → Connect Claude Code**).
- **Spending:** paid tools cost real money. Keep them permission-gated — do not
  blanket-allowlist `mcp__goa__*`. Always show the user the `cost_estimate` from
  the first (no-token) call and get their go-ahead before re-calling with the
  approval token.
