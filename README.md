# Family Contribution Management System — Documentation

This is the requirements & design documentation site for the Family Contribution Management System, built with [MkDocs](https://www.mkdocs.org/) and the [Material theme](https://squidfunk.github.io/mkdocs-material/).

## Viewing the docs

### Locally
```bash
pip install mkdocs-material
mkdocs serve
```
Then open `http://127.0.0.1:8000` in your browser.

### On GitHub Pages
```bash
pip install mkdocs-material
mkdocs gh-deploy
```
This builds the site and pushes it to a `gh-pages` branch, which GitHub Pages will serve automatically (enable Pages → "Deploy from branch: gh-pages" in repo settings).

## Structure
```
.
├── mkdocs.yml              # site config and navigation
└── docs/
    ├── index.md                          # project overview & scope
    ├── roles-and-permissions.md          # who can do what
    ├── functional-requirements.md        # what the system must do
    ├── non-functional-requirements.md    # security, performance, quality constraints
    ├── data-model.md                     # ER diagram + entity design rationale
    ├── security-and-privacy.md           # how sensitive flows are handled safely
    ├── build-plan.md                     # tech stack + branch-by-branch delivery order
    └── decision-log.md                   # resolved & open design decisions
```

## Status
Backend and database design in progress. Frontend work begins once the API surface is stable.
