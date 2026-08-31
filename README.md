# 83blue transfer

**Free, no-signup file transfer for AI agents.** Upload with one request, hand the
returned url to any other agent, model, machine or human. No accounts, no API keys, no
cookies. Files up to 1 TB, auto-deleted after 1-180 days. Automated use is welcome.

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

## MCP server (no auth)

```
https://upload.83blue.com/mcp
```

Tools: `share_file`, `share_text`, `share_conversation` (packages a transcript plus
files behind one handoff url any model can fetch for a full briefing) and
`receive_file`. Listed in the official MCP registry as `com.83blue.upload/transfer`.

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
npx plugins add 1983ltd/83blue-transfer
```

Or add this repo as a plugin marketplace/source in Codex, Copilot CLI, Cursor or VS Code.

## A2A

Agent card: https://upload.83blue.com/.well-known/agent-card.json (JSON-RPC endpoint
at /a2a, protocol 0.3.0). Works as the hosting layer for any A2A `FileWithUri` part.

## The handoff pattern

Agent A uploads everything the next agent needs (`HANDOFF.md` briefing at the zip
root), then passes one url. Agent B fetches it and picks up the whole context:

```
Pick up where I left off. Fetch this url for the full briefing and files:
https://upload.83blue.com/h/TOKEN/KEY
```

## Limits

| Thing | Limit |
|---|---|
| Max file size | 1 TB (chunked); 512 MB single request; 100 MB per MCP call |
| Retention | 1-180 days (default 180 HTTP, 30 MCP) |
| Simultaneous uploads | 3 site-wide (429 + Retry-After when full: just retry) |
| Download speed | 1 Mbit/s per connection, 3 connections per IP |
| Price | Free, within fair use |

Machine-readable docs: [llms.txt](https://upload.83blue.com/llms.txt) ·
[OpenAPI 3.1](https://upload.83blue.com/openapi.json) ·
[full reference](https://upload.83blue.com/llms-full.txt)

Abuse reports: https://upload.83blue.com/report · Terms: https://upload.83blue.com/terms
