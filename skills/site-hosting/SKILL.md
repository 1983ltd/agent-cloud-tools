---
name: site-hosting
description: Publish a static website (HTML/CSS/JS/images) an agent built to a live public URL in one call, free and no signup, using upload.83blue.com. Use when asked to build and host, show, deploy or put online a website, landing page, demo, prototype or report, or whenever a live URL is wanted rather than a downloadable file.
---

# Static site hosting

When you have built a static website and need a live public URL (to show a person or
hand to another agent), publish it to 83blue in one call. Free, no account or API key,
and you get a manage_key to edit or remove just that site later.

## If the upload-83blue MCP tools are available

Prefer them:

- `deploy_site`: publish files (each `{path, text | base64}`, and it must include an
  `index.html` at the root). Optional `slug` (the `/s/{name}` path), `title`,
  `expires_days` (1-30), and `manage_key` to update a site you already own. Returns the
  live url and a `manage_key`.
- `list_sites`: status, size and expiry of a site (by url, path or name).
- `delete_site`: take a site down (needs the `manage_key`).

## Without MCP (shell)

```bash
curl -X POST https://upload.83blue.com/api/site -H 'Content-Type: application/json' \
  -d '{"files":[{"path":"index.html","text":"<h1>Hello</h1>"},
                {"path":"style.css","text":"h1{font-family:sans-serif}"}],
       "expires_days":30}'
# -> {"url":"https://upload.83blue.com/s/{name}/", "manage_key":"..."}

# status
curl "https://upload.83blue.com/api/site?site=NAME"
# take it down
curl -X DELETE "https://upload.83blue.com/api/site?site=NAME&manage_key=KEY"
```

## Conventions

- Include an `index.html` at the root: that is what visitors see at the URL.
- Use RELATIVE asset paths (`style.css`, `img/logo.png`), never root-absolute
  (`/style.css`): the site is served from a subfolder, `https://upload.83blue.com/s/{name}/`.
- Sites are pure static files; server-side code (`.php` etc.) is never executed.
- Keep the `manage_key`: redeploy the same name with it to update or renew; it controls
  only that site (and anything under it).
- Host vs transfer: use site hosting when the user wants to SEE or share a working page
  at a URL; use file handoff (the sibling skill) for handing over downloadable files.
