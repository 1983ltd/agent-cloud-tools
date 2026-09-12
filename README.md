# Free cloud tools for AI agents

**Free, no-signup cloud tools for AI agents: file transfer, static site hosting, durable memory and conversation handoff.** Upload with one
request and hand the returned url to any other agent, model, machine or human, or publish
a static website in one call to a live url. No accounts, no API keys, no cookies. Files up
to 2 TB, auto-deleted after 1-180 days. Also works as durable **scratchpad memory** for
agents. Automated use is welcome.

This repo is the official [Agent Plugin](https://agent-plugins.org) for the service
(works with ChatGPT, Codex, Cursor, GitHub Copilot, Kiro and VS Code) plus copy-paste
examples for everything else.

## Send a file

```bash
# transfer.sh style: raw PUT, plain-text url back
curl --upload-file ./report.zip https://upload.83blue.com/report.zip

# richer JSON (capability_url, handoff_url, password, sha256)
curl -F "file=@report.zip" https://upload.83blue.com/api/upload
```

## Receive a file

```bash
curl -fL -OJ "https://upload.83blue.com/f/TOKEN/KEY/report.zip"
```

## Delete when done

```bash
curl -X DELETE "https://upload.83blue.com/f/TOKEN/KEY"
```

## Host a static site

Publish a static website (HTML, CSS, JS, images) an agent has built to a live public url
in one call, no signup:

```bash
curl -X POST https://upload.83blue.com/api/site -H 'Content-Type: application/json' \
  -d '{"files":[{"path":"index.html","text":"<h1>Hello</h1>"},
                {"path":"style.css","text":"h1{color:#2f81f7}"}],
       "expires_days":30}'
# -> {"url":"https://upload.83blue.com/s/brave-otter-482/", "manage_key":"..."}
```

You get a live url at `https://upload.83blue.com/s/{name}/` and a `manage_key` to update
or delete just that site later. Over MCP: `deploy_site`, `list_sites`, `delete_site`.
Sites are served as pure static files (server-side code is never executed), so use
**relative** asset paths (`style.css`, not `/style.css`). Include an `index.html` at the
root. Up to 50 MB and 300 files per site; auto-expires after 30 days (redeploy with the
same name and `manage_key` to renew or update).

## MCP server (no auth)

```
https://upload.83blue.com/mcp
```

Tools: `share_file`, `share_text`, `share_conversation` (packages a transcript plus
files behind one handoff url any model can fetch for a full briefing), `receive_file`,
plus `deploy_site`, `list_sites` and `delete_site` for one-call static site hosting.
Listed in the official MCP registry as `com.83blue.upload/agent-cloud-tools`.

```bash
# Claude Code
claude mcp add --transport http transfer https://upload.83blue.com/mcp
# Codex CLI
codex mcp add upload83blue --url https://upload.83blue.com/mcp
# Gemini CLI
gemini mcp add --transport http upload83blue https://upload.83blue.com/mcp
```

Setup for Cursor, VS Code, ChatGPT, Grok, Windsurf, Cline, Zed, JetBrains, Warp and
Raycast: https://upload.83blue.com/docs#mcp

## Install as an Agent Plugin

```bash
npx plugins add YOURORG/83blue-transfer
```

Or add this repo as a plugin marketplace/source in Codex, Copilot CLI, Cursor or VS Code.

## A2A

Agent card: https://upload.83blue.com/.well-known/agent-card.json (JSON-RPC endpoint
at /a2a, protocol 0.3.0). Works as the hosting layer for any A2A `FileWithUri` part.
(Site hosting is available via the MCP server and the HTTP API, not over A2A.)

## The handoff pattern

Agent A uploads everything the next agent needs (`HANDOFF.md` briefing at the zip
root), then passes one url. Agent B fetches it and picks up the whole context:

```
Pick up where I left off. Fetch this url for the full briefing and files:
https://upload.83blue.com/h/TOKEN/KEY
```

## Agent memory / scratchpad

Every transfer is a durable blob you fetch back by url, so agents use it as short and
long-term memory: stash text or JSON with `share_text` (or `POST /api/upload`), keep it
up to 180 days, and retrieve it by its capability url in a later session or on another
machine. Blob-by-url (no full-text search).

## Limits

| Thing | Limit |
|---|---|
| Max file size | 2 TB (chunked); 512 MB single request; 100 MB per MCP call |
| Retention | 1-180 days (default 180 HTTP, 30 MCP) |
| Site hosting | 50 MB and 300 files per site, static only, 30-day expiry |
| Simultaneous uploads | 3 site-wide (429 + Retry-After when full: just retry) |
| Download speed | 1 Mbit/s per connection, 3 connections per IP |
| Price | Free, within fair use |

Machine-readable docs: [llms.txt](https://upload.83blue.com/llms.txt) ·
[OpenAPI 3.1](https://upload.83blue.com/openapi.json) ·
[full reference](https://upload.83blue.com/llms-full.txt)

Abuse reports: https://upload.83blue.com/report · Terms: https://upload.83blue.com/terms
