# Get started

This tutorial takes you from zero to having the `echo` plugin installed and
answering in your Claude Code session. It takes about five minutes.

## What you will do

By the end, you will have:

- The `meyclem-ai-tooling` marketplace registered in Claude Code.
- `echo` installed at user scope, available across all your projects.
- **Auto-update enabled** for the marketplace — it is off by default, and
  nothing propagates without it.
- Confirmed the plugin answers when invoked.

## What you need

[Claude Code](https://claude.ai/code), installed and working. Nothing else: the
repository is public, so there is no token and no credential to set up.

## Step 1 — Register the marketplace

In a Claude Code session:

```text
/plugin marketplace add meyclem/meyclem-ai-tooling
```

Accept the confirmation prompt. The marketplace is now registered under the name
`meyclem-ai-tooling`.

## Step 2 — Install a plugin

Plugins install one at a time, by name:
`/plugin install <plugin>@meyclem-ai-tooling --scope <user|project>`. Start with
`echo`:

```text
/plugin install echo@meyclem-ai-tooling --scope user
/reload-plugins
```

Confirm at the prompt. `--scope user` makes the plugin available across all your
projects; `--scope project` limits it to the one you are in and records it in
that repository's settings, for everyone working on it. `/reload-plugins`
activates it in the current session — a fresh session does this on its own, but
a mid-session install needs the explicit reload.

## Step 3 — Enable auto-update

> [!important] Do not skip this step
>
> Claude Code enables auto-update by default only for official Anthropic
> marketplaces. Third-party ones — this one included — start with it
> **disabled**.
>
> Until you turn it on, your plugins stay frozen at the version you just
> installed: pushes to `main` never reach you, and **nothing tells you so**.

```text
/plugin
```

Go to the **Marketplaces** tab, select `meyclem-ai-tooling`, and choose **Enable
auto-update**. The panel then reads:

> Auto-update enabled. Claude Code will automatically update this marketplace
> and its installed plugins.

That line is also how you check the setting later: if the action offered is
**Enable auto-update**, auto-update is currently off.

If you prefer editing settings directly, add `autoUpdate` to the entry Claude
Code already created:

```jsonc
// ~/.claude/settings.json
{
  // ...
  "extraKnownMarketplaces": {
    "meyclem-ai-tooling": {
      "source": {
        "source": "github",
        "repo": "meyclem/meyclem-ai-tooling"
      },
      "autoUpdate": true
    }
  }
  // ...
}
```

Both are equivalent; the setting is read at session start.

## Step 4 — Invoke a skill

Skills are namespaced by the plugin shipping them:
`/<plugin-name>:<skill-name>`. Type the prefix and let completion list what is
available:

```text
/echo:
```

Run `/echo:echo hello`. It answers `hello`, and nothing else. That reply is the
confirmation you need: the plugin is installed, reachable, and active in this
session.

Each plugin's README lists what it ships — start from
[the plugins directory](../../plugins/).

## Step 5 — Confirm auto-update works

With step 3 done, a push to `main` reaches you on its own shortly after one of
your sessions starts — never in a session that was already running.
[Update propagation](../reference/lifecycle.md#update-propagation) gives the
exact delay.

To check which version you are running:

```bash
claude plugin list
```

Each entry prints a `Version` that is the short commit SHA of the marketplace it
was installed from — every plugin moves together, whatever the commit touched:

```text
  ❯ echo@meyclem-ai-tooling
    Version: b2595f29ddb9
    Scope: user
    Status: ✔ enabled
```

Compare its leading characters with the SHA of the latest commit on `main`. A
match means you are current. A different SHA means either the push has not
propagated yet, or nothing is propagating at all — wait for your next session,
then return to [step 3](#step-3--enable-auto-update) and confirm auto-update is
still enabled.

To refresh the current session without waiting, run both commands:

```text
/plugin marketplace update meyclem-ai-tooling
/reload-plugins
```

One alone is not enough.

## What you learned

You now know how to:

- Register a third-party marketplace in Claude Code.
- Install plugins at user scope with `/plugin install`.
- Invoke skills through the `/<plugin-name>:<skill-name>` namespace.
- Enable auto-update, and verify it actually propagates.

## Next

- [CONTRIBUTING.md](../../CONTRIBUTING.md) — to add or modify a plugin.
- [Quality bar](../reference/quality-bar.md) — what is allowed in a plugin.
- [Lifecycle](../reference/lifecycle.md) — how updates and removals work.
