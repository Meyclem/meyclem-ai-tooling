# Lifecycle

How updates and removals propagate from this marketplace to the people using it.

## Update propagation

`plugin.json` deliberately omits the `version` field. Claude Code falls back to
the git commit SHA of the marketplace's tracked branch (`main`) as the cache
key. Consequences:

- **Every push to `main` propagates as an update** — to everyone who has
  _enabled auto-update_ for this marketplace. That is not the default; see
  [Auto-update prerequisites](#auto-update-prerequisites).
- Propagation happens **after** a session starts, with a random delay of up to
  ten minutes. It is not instantaneous, and a session never picks up a change
  pushed after it launched: it keeps the versions it loaded, and the new ones
  apply at the next launch or after `/reload-plugins`.
- Both the catalog and the plugin code move. The cache directory is re-cloned at
  the new SHA and `installed_plugins.json` follows.
- No version bump required. No release ceremony.
- A mid-session manual refresh takes two commands:
  `/plugin marketplace update meyclem-ai-tooling` to fetch the new SHA, then
  `/reload-plugins` to activate it. One alone is not enough.

When a prerequisite is missing, auto-update fails **silently**. The plugin keeps
working at the installed version, nothing surfaces the gap, and the user has no
reason to suspect they are running stale content. This is the first thing to
check when a change does not seem to reach anyone.

## Auto-update prerequisites

- The marketplace registered (`/plugin marketplace add ...`).
- The plugin installed (`/plugin install <name>@meyclem-ai-tooling ...`).
- **Auto-update enabled for the marketplace.** Claude Code enables it by default
  only for official Anthropic marketplaces; third-party ones such as this one
  start **disabled**. Turn it on in `/plugin` → **Marketplaces** →
  `meyclem-ai-tooling` → **Enable auto-update**, or set `"autoUpdate": true` on
  the `extraKnownMarketplaces` entry in `~/.claude/settings.json`. See
  [Get started, step 3](../tutorials/get-started.md#step-3--enable-auto-update).

The repository is public, so no credential is involved in the fetch.

## Removal propagation

Removing a plugin's entry from `.claude-plugin/marketplace.json` stops its
distribution. At the next session start, Claude Code refreshes the catalog, sees
the plugin is gone, and uninstalls it. The user's data under
`${CLAUDE_PLUGIN_DATA}` is deleted unless they had previously run
`/plugin uninstall ... --keep-data`.

Deleting the `plugins/<name>/` directory is optional: the catalog entry is what
consumers read. For the procedure, see
[How to remove a plugin](../how-to/remove-a-plugin.md).

`disabled: true` on a marketplace entry is reported to keep a plugin installed
but invisible, without uninstalling it consumer-side. **This has not been
verified against a real consumer** — treat it as a lead, not documented
behaviour.

## Deprecation

**No deprecation convention is defined.** The first time one is needed, it gets
set then, informed by the actual constraint rather than a guess. Until then, do
not assume a `DEPRECATED:` prefix in a description will be respected by anyone,
and do not ship a `DEPRECATED.md` expecting people to find it. Open an issue
describing the affected plugin and the timeline.

## Rolling back

With commit-SHA versioning, rolling back means reverting on `main`:

```bash
git revert <bad-commit>
git push
```

The revert commit is the new SHA, which becomes the new cache key — consumers
get the rolled-back state at their next session.

There is no way to pin someone to a previous version short of asking them to
uninstall and reinstall against a specific branch. Ship a fix forward instead.

## What changes if explicit versions are introduced

If this marketplace ever switches to explicit `version` fields — for a stable
release cycle, or to use the `dependencies` constraint feature —
[Update propagation](#update-propagation) needs rework: updates would only
propagate on a version bump, a per-plugin `CHANGELOG.md` becomes worthwhile, and
release tags become useful for reproducibility. This page gets updated then.
