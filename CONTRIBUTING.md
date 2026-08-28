# Contributing

Issues and pull requests are welcome — bug reports, fixes, improvements to an
existing plugin, or an entirely new one.

Be decent to other people in issues and reviews. That is the whole code of
conduct.

## Opening an issue

Useful things to include: what you ran, what you expected, what happened, and
your Claude Code version (`claude --version`).

## Setting up

```bash
git clone https://github.com/meyclem/meyclem-ai-tooling.git
cd meyclem-ai-tooling
pre-commit install
```

[`pre-commit`](https://pre-commit.com/) is the only tooling this repo uses.
Install it with `brew install pre-commit` or `pip install pre-commit`.

Three commands cover everything:

```bash
pre-commit install         # once, after cloning
pre-commit run --all-files # before opening a PR
claude plugin validate .   # spot-check the manifests
```

The hooks fire on every `git commit` and block anything that fails JSON syntax,
formatting, spelling, secret detection, or `claude plugin validate`.

## Adding a plugin

```text
plugins/<plugin-name>/
├── .claude-plugin/
│   └── plugin.json         # manifest (name, description, author)
├── skills/<skill-name>/
│   └── SKILL.md            # one directory per skill
├── agents/<agent-name>.md  # optional, one file per agent
├── commands/<cmd-name>.md  # optional, one file per command
└── README.md               # what it does, what it ships, how to invoke it
```

Then declare it in the catalog at `.claude-plugin/marketplace.json`:

```json
{
  "name": "<plugin-name>",
  "source": "./plugins/<plugin-name>",
  "description": "Short one-line summary."
}
```

`source` is the full relative path from the repo root, with the `./` prefix. The
shorthand `./<plugin-name>` (relative to `metadata.pluginRoot`) passes the
validator but is **not** honored at runtime — the install fails with
`Source path does not exist`.

[Quality bar](./docs/reference/quality-bar.md) lists what a plugin must satisfy.
Every component type is allowed — but anything that acts beyond the session
(hooks, `bin/`, MCP servers, monitors) has to be disclosed in your plugin's
README.

## Modifying an existing plugin

Edit files under `plugins/<plugin-name>/`. No version bump: `plugin.json`
deliberately omits `version`, so Claude Code falls back to the commit SHA and
every push to `main` propagates on its own.

If the change touches what users see — a new skill, a removed one, different
arguments — update that plugin's `README.md` in the same pull request.

## Testing before you push

```bash
claude --plugin-dir plugins/<plugin-name> -p "/<plugin-name>:<skill-name>"
```

This loads the plugin straight from your working tree, so you can iterate on a
`SKILL.md` without pushing and waiting.

## Opening a pull request

Commits follow [conventional commits](https://www.conventionalcommits.org/)
(`feat:`, `fix:`, `docs:`, `chore:`, `refactor:`, `ci:`). Scope with the plugin
name when the change is specific to one: `feat(echo): ...`.

```bash
git checkout -b feat/<short-description>
git commit -m "feat(<plugin-name>): <short message>"
git push -u origin feat/<short-description>
gh pr create
```

CI re-runs the same checks on your PR.
