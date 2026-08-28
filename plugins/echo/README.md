# echo

A deliberately minimal plugin. It echoes back whatever you pass it.

Its job is to prove the pipe works: that the marketplace is registered, that the
plugin installed, and that its skill is reachable from your session. If `echo`
answers, anything else in this marketplace will too.

## What it ships

| Skill  | Invocation          | Behavior                    |
| ------ | ------------------- | --------------------------- |
| `echo` | `/echo:echo <text>` | Returns `<text>` unchanged. |

## Install

```text
/plugin install echo@meyclem-ai-tooling --scope user
/reload-plugins
```

## Try it

```text
/echo:echo hello
```

Expected answer: `hello`, and nothing else.

Called with no argument, it answers `(echo: no arguments provided)`.

## Test it locally

Without going through the marketplace, from a clone of this repo:

```bash
claude --plugin-dir plugins/echo -p "/echo:echo hello"
```
