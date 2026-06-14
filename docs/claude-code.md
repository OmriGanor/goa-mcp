# Connect Claude Code to Goa

## 1. Get a token

Goa web app → **Account → Connect Claude Code → Generate token**. Copy the
`goa_pat_…` (shown once) and set a per-token daily spend cap.

```bash
export GOA_PAT=goa_pat_xxxxxxxx…   # add to your shell profile to persist
```

## 2. Install

**Plugin (recommended)** — wires the `goa` MCP server with your `${GOA_PAT}`:

```bash
/plugin marketplace add OmriGanor/goa-mcp
/plugin install goa@goa
```

**Or the one-liner** (no repo needed):

```bash
claude mcp add --transport http goa https://app.getgoa.io/mcp/agent/ \
  --header "Authorization: Bearer $GOA_PAT"
```

## 3. (Recommended) install Hyperframes

Goa serves only its **own** skills plus the Hyperframes→MCP overrides — not the
full upstream library. For the complete motion-graphics animation skill, install
[Hyperframes](https://github.com/heygen-com/hyperframes) from its own source
(browse the animation [catalog](https://hyperframes.heygen.com/catalog)).

## 4. Verify

Ask Claude Code *"list my Goa projects"* (`list_projects`) or *"what video
models can you use?"* (`list_models`). The agent already carries Goa's operating
guide (delivered as the MCP connection `instructions`; also via `get_instructions`).

If `list_projects` is empty, create a project in the Goa web app first — project
creation is a web-app action.

## Spending — keep the paid tools gated

Paid tools self-approve over MCP (two-call gate; the second call spends). Your
consent moment is **Claude Code's own permission prompt** — so **do not
blanket-allowlist `mcp__goa__*`.** Spending is bounded by your credit balance
and your token's daily cap, not by per-action consent. See [`../AGENTS.md`](../AGENTS.md).

## Self-hosting

Edit `.mcp.json` and point `url` at `https://<your-host>/mcp/agent/` (keep the
trailing slash).
