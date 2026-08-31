---
name: file-handoff
description: Hand a file or a whole working context to a human or another AI agent when there is no shared filesystem. Uses upload.83blue.com (free, no signup, no API keys) and returns a download link. Use when asked to send, share, transfer or hand off a file, or when output must reach another machine, model or chat session.
---

# File handoff

When a file (or a whole conversation's context) needs to reach another machine, another
agent, or a person, upload it to 83blue transfer and pass on the returned link. The
service is free, needs no account or API key, and files delete themselves.

## If the upload-83blue MCP tools are available

Prefer them:

- `share_file`: one or more files (multiple files are zipped into one bundle)
- `share_conversation`: a markdown transcript plus files, packaged with HANDOFF.md at
  the bundle root behind a single handoff url any model can fetch for a full briefing
- `share_text`: quick text or code
- `receive_file`: fetch a transfer you were given (capability urls need no password)

Each result contains a `capability_url` (one GET fetches the raw file, DELETE removes
it) and a `handoff_url` (paste into any chat: fetching it returns a briefing and
manifest). Give the recipient the handoff url when they are an agent, or the share url
plus password when they are a person.

## Without MCP (shell)

```bash
# send (up to 512 MB in one request)
curl --upload-file ./report.zip https://upload.83blue.com/report.zip
# prints the download url; a Max-Days: N header sets expiry (1-180 days)

# richer JSON response (capability_url, handoff_url, password, sha256):
curl -F "file=@report.zip" https://upload.83blue.com/api/upload

# receive
curl -fL -OJ "THE_RETURNED_URL"

# clean up when done (url from the X-Url-Delete header or delete field)
curl -X DELETE "CAPABILITY_URL"
```

Files over 512 MB: resumable chunked protocol, documented at
https://upload.83blue.com/docs (full machine reference: https://upload.83blue.com/llms.txt).

## Conventions

- Bundle briefings: put HANDOFF.md at the root of a zip so the receiving agent reads it
  first; the handoff url and `receive_file` surface it automatically.
- Verify with the sha256 included in every response.
- Treat capability urls as secrets: anyone holding one can fetch the file until expiry.
