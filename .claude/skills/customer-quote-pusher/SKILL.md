---
name: customer-quote-pusher
description: Add a new client proposal to the Vikas-Claude-Demo/customer-quote GitHub repo and publish it via GitHub Pages. Use this skill whenever the user wants to push a client quote, add a new customer proposal, create a demo page for a client, publish a client's HTML quote, deploy a proposal page, or says things like "add this client to the repo", "push a new quote", "deploy this proposal", "create a demo page for [client name]", or mentions the customer-quote repo. Also trigger when the user provides an index.html and a client/project name together. This skill handles the full workflow: pulling the repo, creating the correct folder structure, placing the file, committing, pushing via gh CLI, and returning the live GitHub Pages URL.
---

# Customer Quote Pusher

This skill adds a new client proposal page to the `customer-quote` GitHub repo and publishes it via GitHub Pages.

## Hardcoded context — do not change

- **GitHub repo:** `https://github.com/Vikas-Claude-Demo/customer-quote`
- **GitHub org/user:** `Vikas-Claude-Demo`
- **Repo name:** `customer-quote`
- **Auth method:** GitHub CLI (`gh`) over HTTPS
- **Default branch:** `main`
- **Working clone path:** the current working directory IS the repo (this skill assumes you're already inside `customer-quote/`)
- **Live URL pattern:** `https://vikas-claude-demo.github.io/customer-quote/clients/<client-slug>/`

## When the user invokes this skill

The user will typically provide:
1. A client name or project name (e.g., "Acme Corp", "globex-fintech-app")
2. An `index.html` file (uploaded, pasted, or already in the workspace)

If either is missing, ask before proceeding. Do not generate placeholder content.

## Workflow

### Step 1: Verify prerequisites

```bash
gh auth status
```

If not authenticated, stop and tell the user to run `gh auth login`. Do not use SSH or PATs.

Confirm the current working directory is the repo root:

```bash
git remote get-url origin
```

This should return a URL pointing to `Vikas-Claude-Demo/customer-quote`. If not, stop and tell the user they're in the wrong directory.

### Step 2: Pull the latest

```bash
git checkout main && git pull origin main
```

### Step 3: Derive the client slug

Convert the client/project name to a URL-safe slug:
- Lowercase
- Spaces and underscores → hyphens
- Remove anything that isn't `a-z`, `0-9`, or `-`
- Collapse multiple hyphens
- Trim leading/trailing hyphens

Examples:
- `Acme Corp` → `acme-corp`
- `Globex Fintech App!` → `globex-fintech-app`
- `Stark_Industries 2026` → `stark-industries-2026`

Confirm the slug with the user if the name was ambiguous.

### Step 4: Check for existing folder

```bash
ls clients/<client-slug>/ 2>/dev/null
```

If the folder exists, ask: "A folder for `<client-slug>` already exists. Do you want to (a) overwrite the existing `index.html`, (b) create a versioned folder like `<client-slug>-v2`, or (c) cancel?" Wait for explicit confirmation.

### Step 5: Create the folder and files

```bash
mkdir -p clients/<client-slug>
```

Place the user's `index.html` at `clients/<client-slug>/index.html`.

Create `clients/<client-slug>/README.md` with internal notes:

```markdown
# <Client Name> — Internal Notes

- **Slug:** <client-slug>
- **Created:** <YYYY-MM-DD>
- **Status:** sent
- **Contact:** (fill in)
- **Deal value:** (fill in)
- **Notes:** (fill in)

Live URL: https://vikas-claude-demo.github.io/customer-quote/clients/<client-slug>/
```

### Step 6: Commit and push

```bash
git add clients/<client-slug>/
git commit -m "Add proposal for <Client Name> (<client-slug>)"
git push origin main
```

Use `Update proposal for ...` instead of `Add proposal for ...` for overwrites.

### Step 7: Return the live URL

```
✅ Pushed successfully.

Live URL: https://vikas-claude-demo.github.io/customer-quote/clients/<client-slug>/

Note: GitHub Pages may take 30–60 seconds to refresh.
```

If GitHub Pages isn't enabled yet, mention this once and tell the user to enable it under Settings → Pages → Source: main branch, root folder.

## Never do

- Push to any account other than `Vikas-Claude-Demo`.
- Push to any repo other than `customer-quote`.
- Modify other clients' folders unless explicitly asked.
- Force-push (`--force` or `-f`).
- Delete client folders — move to `_archive/` instead.
- Commit `.DS_Store`, `node_modules`, `.env`, or any untracked junk.
- Use SSH, PATs, or any auth other than `gh` CLI.

## Edge cases

- **No `gh` installed:** Tell user to install (`brew install gh` / `apt install gh` / `winget install GitHub.cli`).
- **`gh auth status` fails:** Tell user to run `gh auth login` and choose GitHub.com → HTTPS → browser auth.
- **Push permission denied:** Authenticated account lacks write access. Surface error, ask user to confirm account.
- **Merge conflict on pull:** Stop and surface to user. Don't auto-resolve.
- **Multiple HTML files:** Ask whether they want them in one folder or separate client folders.

## Archiving a closed deal

If the user says "archive client X" or "move client X to archive":

```bash
git pull origin main
mkdir -p _archive
git mv clients/<client-slug> _archive/<client-slug>
git commit -m "Archive proposal: <client-slug>"
git push origin main
```
