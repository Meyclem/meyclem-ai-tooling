# CLAUDE.md

Guidance for Claude Code when working in this repo.

## What this is

A personal Claude Code plugin marketplace, public and open source
(`github.com/meyclem/meyclem-ai-tooling`). The marketplace identifier
(`meyclem-ai-tooling`) and the repo name are aligned, on purpose: users type one
in `/plugin marketplace add` and the other after `@` when installing.

## Repo layout

- `.claude-plugin/marketplace.json` — catalog manifest. Source of truth for
  which plugins are distributed.
- `plugins/<name>/` — one directory per plugin, each with its own
  `.claude-plugin/plugin.json`, `skills/`, `agents/`, `commands/`, `README.md`.
- `docs/` — marketplace-wide documentation (tutorial / how-to / reference).
- `CONTRIBUTING.md` — contributor entry point. Setup, plugin structure, PR flow.
- `.pre-commit-config.yaml` — the validation contract (`claude plugin validate`
  plus standard hooks).
- `.github/workflows/ci.yml` — CI. Re-runs the same hooks, minus the ones
  needing the Claude Code CLI.

## Conventions

- **Conventional commits** (`feat:`, `fix:`, `docs:`, `chore:`, `refactor:`,
  `ci:`). Scope with the plugin name when the change is plugin-specific, e.g.
  `feat(echo): ...`.
- **Plugins ship without a `version` field in `plugin.json`.** Claude Code falls
  back to the git commit SHA, so every push to `main` propagates as an update at
  the next session start. No release ceremony.
- **`source` in `marketplace.json` is the full relative path** from the repo
  root (`./plugins/<name>`), never the shorthand. The shorthand passes the
  validator and then fails at runtime.
- **Everything in this repo is written in English** — specs, docs, plugin
  content, READMEs, comments. Consumers are global.
- **Forbidden plugin component types**: `hooks/`, `bin/`, `.mcp.json`,
  `.lsp.json`, `monitors/`. Allowed: `skills/`, `agents/`, `commands/`, plus
  bundled files reached through `${CLAUDE_SKILL_DIR}` / `${CLAUDE_PLUGIN_ROOT}`.
  See `docs/reference/quality-bar.md`.

## This repo is public

Everything committed here is world-readable, forkable, and indexed. Before
writing anything into this repo, check it carries no employer-internal detail,
no private hostname or URL, no personal email address, and no credential — not
in docs, not in a code comment, not in an example.

## Updating documentation alongside changes

When a change touches the structure or a feature — adding or removing a plugin,
changing the manifest schema, evolving the validation tooling, altering the
install or contribute flow — propose the matching doc updates in the same pull
request:

- `docs/tutorials/get-started.md` — if the install flow changed.
- `CONTRIBUTING.md` — if the contribution flow changed.
- `docs/reference/quality-bar.md` — if the rules for plugins changed.
- `docs/reference/lifecycle.md` — if update propagation or removal changed.
- `plugins/<name>/README.md` — if a plugin's user-facing surface changed.
- This file — if conventions or layout evolved.

If a doc change is genuinely out of scope, say so explicitly in the PR
description rather than skipping it silently.

## Validation before pushing

```bash
pre-commit install         # one-time after clone
pre-commit run --all-files # before opening a PR
claude plugin validate .   # spot-check the manifests
```

The pre-commit hook already runs `claude plugin validate` on staged plugin
changes, so a normal `git commit` covers it.

## Local plugin testing

```bash
claude --plugin-dir plugins/<name> -p "/<name>:<skill>"
```

Loads a plugin from the working tree without going through the marketplace.

## What lands here reaches every consumer

A broken `marketplace.json` or a malformed `plugin.json` on `main` silently
degrades every installed user's session at their next auto-update. There is no
staging step between this repo and them. Run the validator and the local install
test before committing.
