# How to remove a plugin from the marketplace

Stop distributing a plugin to everyone using this marketplace. Removal is not
reversible on the consumer side: their local plugin data goes with it.

## Steps

1. Open a pull request removing the plugin's entry from
   `.claude-plugin/marketplace.json`.

2. Delete the `plugins/<name>/` directory in the same pull request, or leave it
   for a follow-up. Removing the catalog entry alone stops distribution;
   deleting the directory is housekeeping.

3. Merge to `main`.

## Verification

At their next session start, consumers see the plugin uninstalled: Claude Code
refreshes the catalog, finds the entry gone, and removes it. Their data under
`${CLAUDE_PLUGIN_DATA}` is deleted, unless they had previously run
`/plugin uninstall <name> --keep-data`.

If someone still has the plugin after their next two sessions, they have
auto-update disabled — the catalog never refreshes for them. Walk them through
[Auto-update prerequisites](../reference/lifecycle.md#auto-update-prerequisites).

## See also

- [Lifecycle](../reference/lifecycle.md) — how updates and removals propagate,
  and how to roll one back.
