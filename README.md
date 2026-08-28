# meyclem-ai-tooling

A personal Claude Code plugin marketplace. Skills, agents, and commands I use,
packaged so they install anywhere in one command — and so anyone else can use
them too.

## Install

In a Claude Code session:

```text
/plugin marketplace add meyclem/meyclem-ai-tooling
/plugin install echo@meyclem-ai-tooling --scope user
/reload-plugins
```

Then enable auto-update for the marketplace, or nothing will ever reach you
again: `/plugin` → **Marketplaces** → `meyclem-ai-tooling` → **Enable
auto-update**. It is off by default for third-party marketplaces, and it fails
silently. [Get started](./docs/tutorials/get-started.md) walks through it.

## Catalog

| Plugin | What it does                                                                 |
| ------ | ---------------------------------------------------------------------------- |
| `echo` | Echoes back what you pass it. Minimal plugin to check the wiring is working. |

## Docs

[Documentation](./docs/) — installing, contributing, how updates propagate, and
what a plugin must satisfy.

## Contributing

Issues and pull requests are welcome, including new plugins. See
[CONTRIBUTING.md](./CONTRIBUTING.md).

## License

[WTFPL](./LICENSE). Do what the fuck you want to.
