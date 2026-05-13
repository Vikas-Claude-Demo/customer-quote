# customer-quote

Client proposal and quote pages — each client gets a self-contained HTML page (demo + cost + plan) published via GitHub Pages.

## Live URLs

Each client's page is available at:

`https://vikas-claude-demo.github.io/customer-quote/clients/<client-slug>/`

## Structure

```
clients/<client-slug>/index.html   ← the proposal
clients/<client-slug>/README.md    ← internal notes
_archive/                           ← closed deals
```

See [CLAUDE.md](./CLAUDE.md) for the full rules used when adding or modifying client proposals.

## Adding a new client

If you're using Claude / Cowork: just say "add a new client proposal for `<client name>`" along with the HTML file. The `customer-quote-pusher` skill (at `.claude/skills/customer-quote-pusher/`) handles the rest.

Manually:

```bash
gh repo clone Vikas-Claude-Demo/customer-quote
cd customer-quote
mkdir -p clients/<client-slug>
# place index.html and README.md inside
git add clients/<client-slug>/
git commit -m "Add proposal for <Client Name> (<client-slug>)"
git push origin main
```
