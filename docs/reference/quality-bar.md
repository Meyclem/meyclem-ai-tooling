# Quality bar

What a plugin must satisfy to be merged into this marketplace.

## Allowed plugin components

| Component        | Location                                                             | Notes                                                                                                   |
| ---------------- | -------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Skill            | `skills/<name>/SKILL.md`                                             | Markdown with frontmatter (`name`, `description`). One directory per skill.                             |
| Agent            | `agents/<name>.md`                                                   | Markdown with frontmatter (`name`, `description`, optional `model`, `tools`, etc.). One file per agent. |
| Command          | `commands/<name>.md`                                                 | Flat markdown, the legacy form of skills. Prefer `skills/` for new content.                             |
| Bundled scripts  | `skills/<name>/scripts/` (skill-local) or `scripts/` (plugin-shared) | Referenced through `${CLAUDE_SKILL_DIR}` or `${CLAUDE_PLUGIN_ROOT}`.                                    |
| Persistent state | `${CLAUDE_PLUGIN_DATA}` (runtime, not in the repo)                   | For `node_modules`, venvs, caches that must survive plugin updates.                                     |

## Forbidden components

| Component   | Why                                                                                   |
| ----------- | ------------------------------------------------------------------------------------- |
| `hooks/`    | Arbitrary code execution on tool events, without per-call confirmation from the user. |
| `bin/`      | Adds executables to the user's `PATH` while the plugin is enabled. Too invasive.      |
| `.mcp.json` | Opens outbound network connections to MCP servers.                                    |
| `.lsp.json` | Language servers are a user-local concern, not a marketplace one.                     |
| `monitors/` | Continuous background commands.                                                       |

These are the component types that act on a user's machine beyond the session
they were invoked in. Installing a plugin from here should never be riskier than
reading the markdown it ships. If you have a concrete case for one of them, open
an issue and make the argument — the list is a default, not a dogma.

## Manifest requirements

Each plugin's `.claude-plugin/plugin.json`:

- **Required**: `name`, kebab-case, matching the directory name.
- **Required**: `description`, one short sentence in model-actionable language.
- **Recommended**: `author`.
- **Omitted on purpose**: `version`. Claude Code falls back to the commit SHA,
  so every push propagates. Reintroduce it only for a stable release cycle.

The entry in `.claude-plugin/marketplace.json` requires:

- `name`, matching the plugin's `plugin.json` name.
- `source`, a relative path with the `./` prefix (`./plugins/<name>`). The bare
  form is rejected by the validator.
- `description` — separate from the plugin's own, this one shows in the
  marketplace UI.

## Skill, agent, and command frontmatter

```yaml
---
name: <kebab-case>
description: <one sentence stating what this does and when to invoke it>
---
```

Guidelines for `description`:

- Lead with what it does: verb, then object.
- Say when invoking it is appropriate, if that is not obvious from the name.
- Keep it under about 150 characters. The model reads it to decide whether to
  invoke.

When the trigger condition is non-trivial, split it out of `description` into a
separate `when_to_use:` field — `description` stays focused on _what_,
`when_to_use` on _when_. Claude Code concatenates the two into a single line
(`<description> - <when_to_use>`) before presenting the skill to the model, so
splitting them costs nothing.

## Documentation requirements

Each plugin ships a `plugins/<name>/README.md` covering: what it does, its
inventory of skills, agents, and commands, invocation examples, the install
command, and its limitations.

## Bundled scripts

Scripts under `skills/<name>/scripts/` or `scripts/` are reviewed in the pull
request like any other code. **No automated tests are required.** A broken
script ships silently to everyone at the next auto-update.

When script logic is non-trivial, put a short manual-test recipe in the plugin
README — a few `claude --plugin-dir plugins/<name> -p "..."` invocations a
contributor can run before pushing.

## What the validator checks

`claude plugin validate <path>` covers:

- `plugin.json` schema: required fields and types.
- Skill, agent, and command frontmatter syntax.
- The marketplace manifest schema, when run on the repo root.

Reviewers trust the validator on these points and focus on content quality,
naming, and whether the README matches what the plugin actually ships.
