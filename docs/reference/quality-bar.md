# Quality bar

What a plugin must satisfy to be merged into this marketplace.

## Plugin components

Every component type Claude Code supports is allowed here. Nothing is banned by
policy.

| Component           | Location                                           | Notes                                                                                                               |
| ------------------- | -------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Skill               | `skills/<name>/SKILL.md`                           | One directory per skill. A plugin shipping exactly one skill may put `SKILL.md` at the plugin root.                 |
| Agent               | `agents/<name>.md`                                 | One file per agent.                                                                                                 |
| Command             | `commands/<name>.md`                               | Flat markdown, the legacy form of skills. Prefer `skills/` for new content.                                         |
| Hooks               | `hooks/hooks.json`                                 | Event handlers. Same schema as the `hooks` object in `settings.json`.                                               |
| MCP servers         | `.mcp.json` (plugin root)                          | Server configurations, started when the plugin is enabled.                                                          |
| LSP servers         | `.lsp.json` (plugin root)                          | Requires the language server binary on the user's machine.                                                          |
| Background monitors | `monitors/monitors.json`                           | Long-running commands; each stdout line reaches Claude as a notification.                                           |
| Executables         | `bin/`                                             | Added to the Bash tool's `PATH` while the plugin is enabled.                                                        |
| Default settings    | `settings.json` (plugin root)                      | Only `agent` and `subagentStatusLine` are honored. `agent` activates one of the plugin's agents as the main thread. |
| Bundled scripts     | `skills/<name>/scripts/` or `scripts/`             | Referenced through `${CLAUDE_SKILL_DIR}` or `${CLAUDE_PLUGIN_ROOT}`.                                                |
| Persistent state    | `${CLAUDE_PLUGIN_DATA}` (runtime, not in the repo) | For `node_modules`, venvs, caches that must survive plugin updates.                                                 |

Only `plugin.json` belongs inside `.claude-plugin/`. Every other directory sits
at the plugin root.

## Components that act beyond the session

Skills, agents, and commands are markdown: installing one is about as risky as
reading it. These are not:

- `hooks/` runs commands on tool events, without per-call confirmation.
- `bin/` puts executables on the user's `PATH`.
- `.mcp.json` opens outbound connections to MCP servers.
- `monitors/` runs commands continuously in the background.
- `settings.json` with `agent` set replaces the main thread's system prompt.

They are allowed, and some things are only possible with them. The requirement
is **disclosure, not permission**: a plugin shipping any of the above states so
in its own `README.md`, in plain language, near the top — what it runs, on which
event, and what it touches outside the session. A reader deciding whether to
install must not have to open `hooks.json` to find out.

This marketplace is public and anyone may install from it. Undisclosed side
effects are the one thing that gets a plugin pulled.

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
