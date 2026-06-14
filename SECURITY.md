# Security Policy

## What this repo is

This repository is a **configuration + documentation layer** for connecting an
MCP client to Goa's hosted remote MCP server at `https://app.getgoa.io/mcp/agent/`.
It contains no server code, no secrets, and no credentials — only client wiring
(`.mcp.json`, `.cursor/mcp.json`), the Claude Code plugin manifest, and docs. The
tools, authentication, and spend controls all live behind the hosted endpoint.

## Reporting a vulnerability

Please report security issues privately — **do not open a public GitHub issue**
for a suspected vulnerability.

- Email: **security@getgoa.io**
- Or use GitHub's [private vulnerability reporting](https://docs.github.com/en/code-security/security-advisories/guidance-on-reporting-and-writing-information-about-vulnerabilities/privately-reporting-a-security-vulnerability)
  on this repository.

We aim to acknowledge reports within a few business days. Please include enough
detail to reproduce (affected config/path, client, and steps).

In scope:
- Issues in the config/docs shipped here (e.g. a wiring example that leaks a token).
- Auth or spend-control weaknesses in the hosted endpoint reachable via the
  documented setup.

## Handling your token (`GOA_PAT`)

A `goa_pat_…` token grants **full read/write access to your Goa account** and can
**spend real money** until revoked. Treat it like a password:

- Store it in an environment variable (`GOA_PAT`); never paste it into a committed
  file. Cursor/VS Code interpolation (`${env:GOA_PAT}` / `inputs`) keeps it out of
  config files.
- Set a **per-token daily spend cap** when you generate it.
- **Revoke** any token you suspect is exposed from the Goa web app
  (**Account → Connect Claude Code**).
- **Keep paid tools permission-gated** — do not blanket-allowlist `mcp__goa__*`.
  See [`AGENTS.md`](AGENTS.md) and the "Spending & security" section of the
  [`README`](README.md) for the threat model (prompt-injection driving autonomous
  spend) and the two-call approval gate.
