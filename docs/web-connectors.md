# Connect Claude (web & desktop) and ChatGPT to Goa

claude.ai and ChatGPT connect to Goa as a **custom connector** over OAuth —
there's **no token to copy**. You add the connector once, sign in with Google
(the same login as the Goa web app), and approve a one-time consent screen.
Access is revocable and bounded by your Goa credit balance + daily cap, exactly
like a PAT.

> **OAuth (web) vs PAT (CLI).** Web clients — claude.ai and ChatGPT — use this
> OAuth flow. Command-line clients — Claude Code and Cursor — use a `goa_pat_…`
> bearer token instead; see [`claude-code.md`](claude-code.md) /
> [`cursor.md`](cursor.md).

The connector URL is the same Goa MCP endpoint everywhere:

```
https://app.getgoa.io/mcp/agent/
```

(The trailing slash is canonical; clients that strip it still connect.)

## Claude (web & desktop)

1. In a chat, click **Customize** (or go to **Settings → Connectors**) →
   **Add custom connector**.
2. Paste `https://app.getgoa.io/mcp/agent/` and name it **Goa**.
3. Click **Add** / **Connect**. Claude registers itself automatically (dynamic
   client registration) and opens a Goa sign-in page.
4. **Sign in with Google** (the same account as your Goa web app), then
   **Approve** on the consent screen.
5. Enable the **Goa** connector in your chat's tool list and you're set — ask
   *"list my Goa projects"* to verify.

## ChatGPT

Custom MCP connectors are gated behind **Developer Mode**:

1. **Settings → Connectors → Advanced** (developer settings) and turn on
   **Developer mode** — you'll have to acknowledge a warning that custom
   connectors can take actions on your behalf (it's worded as enabling
   "unverified"/risky connectors). That's expected.
2. Back in **Connectors**, add the **Goa** app with the URL
   `https://app.getgoa.io/mcp/agent/`.
3. **Sign in with Google** and **Approve** the consent screen. ChatGPT keeps the
   connection alive with a refresh token (`offline_access`), so you won't have
   to re-authorize every session.

> Menu labels shift between ChatGPT versions — the constant is: enable developer
> mode, accept the risk prompt, then add the connector by URL.

## Verify

Ask your agent *"list my Goa projects"* or *"what video models can you use?"*.
The agent already carries Goa's operating guide (delivered as the MCP connection
`instructions`; also via the `get_instructions` tool).

> **You need a project first.** Project creation is a web-app action — if
> `list_projects` is empty, create one in the Goa web app, then point the agent
> at it.

## Spending — same gate as everywhere

OAuth doesn't change the spend model. Goa's paid tools follow a **two-call
approval gate**, and your spend is bounded server-side by your **credit balance**
and a **daily cap** (the lower of the global cap and your connection's).

- Your consent moment for an individual generation is **the client's own tool
  prompt** — don't blanket-approve Goa's paid tools.
- The honest guarantee is **bounded spend**, not per-action consent, so keep the
  daily cap low. See [`../AGENTS.md`](../AGENTS.md).

## How the OAuth flow works (optional)

- **Authorization server:** `https://auth.getgoa.io` (self-hosted), reusing
  Goa's Google login for sign-in and showing a per-client consent screen.
- **Tokens are opaque and revocable.** Goa validates each call by introspecting
  the token, so revoking access takes effect within ~a minute — unlike a
  long-lived signed token.
- **Per-client consent.** Each connector you add consents separately; approving
  one client never silently authorizes another.
- **Disconnect** any time from your client's connector settings (or revoke from
  the Goa web app). Spending stops at your credit balance / daily cap regardless.
