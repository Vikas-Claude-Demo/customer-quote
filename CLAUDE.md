# CLAUDE.md — customer-quote repo rules

This file is read automatically by Claude Code and Cowork at session start. Follow these rules whenever working in this repo.

## What this repo is

This repo hosts client proposal/quote pages. Each client gets a self-contained `index.html` (demo + cost + plan, CSS/JS inline) published via GitHub Pages.

- **Repo:** `Vikas-Claude-Demo/customer-quote`
- **Live base URL:** `https://vikas-claude-demo.github.io/customer-quote/`
- **Auth:** GitHub CLI (`gh`) over HTTPS only — no SSH, no PATs

## Folder structure — do not deviate

```
customer-quote/
├── README.md
├── CLAUDE.md                   ← this file
├── .gitignore
├── .claude/
│   └── skills/
│       └── customer-quote-pusher/
│           └── SKILL.md        ← the push workflow skill
├── clients/
│   └── <client-slug>/
│       ├── index.html          ← the actual proposal
│       └── README.md           ← internal notes (not public-facing)
└── _archive/
    └── <old-client-slug>/
        └── index.html
```

## Rules for adding a new client

1. Always use `clients/<client-slug>/index.html` — never put HTML files at the repo root or in a flat structure.
2. Slug format: lowercase, hyphens only (`acme-corp`, not `Acme_Corp` or `acmeCorp`).
3. One folder per client, even if it's just a single HTML file. Future-proofs for adding assets later.
4. Always include a `README.md` in the client folder with internal notes (contact, status, deal value). This file is not linked from the public page.
5. Each `index.html` must be self-contained — CSS and JS inline, no external file dependencies. External CDN links for fonts/libraries are fine.

## Rules for modifying existing clients

- Pull `main` first, always.
- Never force-push.
- Never delete a client folder — move to `_archive/<slug>/` instead via `git mv`.
- Never modify another client's folder when working on a different client.

## Commit message format

- New client: `Add proposal for <Client Name> (<client-slug>)`
- Update: `Update proposal for <Client Name> (<client-slug>)`
- Archive: `Archive proposal: <client-slug>`
- Repo-level changes: `chore: <description>` or `docs: <description>`

## What never to commit

- `.DS_Store`, `Thumbs.db`
- `node_modules/`, `.env`, `.env.*`
- API keys, tokens, passwords (including in HTML files — scan before committing)
- Any client's real financial data beyond what's in the proposal itself

## GitHub Pages

- Source: `main` branch, root folder
- Live URL for a client: `https://vikas-claude-demo.github.io/customer-quote/clients/<client-slug>/`
- Pages can take 30–60 seconds to refresh after push

## When in doubt

If a request is ambiguous (which client folder? overwrite or version? archive or delete?), stop and ask. Never guess.
