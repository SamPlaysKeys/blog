# AGENTS.md — blog (Hugo)

## Stack

- **Hugo** (extended, v0.154.2) — static site generator
- **Theme:** Hugo Theme Stack v4.0.3 (Go module, `config/_default/module.toml`)
- **CSS:** Dart Sass via Hugo Pipes (no JS build step, no package.json)
- **Deploy:** GitHub Actions → GitHub Pages (`https://samplayskeys.github.io/blog`)

## Commands

| Action | Command |
|---|---|
| Dev server (with drafts) | `hugo server -D` |
| Build (dev) | `hugo` |
| Build (prod) | `hugo --gc --minify` |
| Theme update | `hugo mod get -u && hugo mod tidy` |

No test runner, linter, formatter, or typechecker.

## Content

- Posts: `content/post/<slug>/index.md` (Hugo leaf bundles). Cover images go in same directory.
- Pages: `content/page/` (Archives, Search — theme-managed layouts)
- WIP / drafts: `wip/` (not processed by Hugo)
- Categories: `content/categories/<name>/_index.md` with front-matter `title`, `description`, `image` (brand color via theme)
- Permalinks: Posts at `/p/:slug/`, pages at `/:slug/`

## Config

- `config/_default/` split by concern: `config.toml`, `params.toml`, `markup.toml`, `menu.toml`, `permalinks.toml`, `module.toml`, `related.toml`
- `markup.toml`: `unsafe = true` (raw HTML in markdown), LaTeX passthrough (`$$`/`\(`), code fences with line numbers
- `params.toml`: comment provider is Disqus (shortname `hugo-theme-stack` — not yet customized)

## CI (`.github/workflows/`)

- `deploy.yml`: build + deploy on push to `master` (Go 1.25.5, Node 24.12.0, Dart Sass 1.97.1)
- `update-theme.yml`: daily cron, commits theme bumps

## Quirks

- `go.mod` module path is `github.com/CaiJimmy/hugo-theme-stack-starter` (starter template, no impact)
- Disqus shortname is the template default — not production-configured
- Devcontainer at `.devcontainer/` for VS Code Remote, exposes port 1313
- No pre-commit hooks, no linting, no tests — `hugo` command is the only validation
