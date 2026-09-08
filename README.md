# workshop-ai-lab

Throwaway marketplace for testing plugin distribution. Contains no client data and no
credentials. Delete it when the questions below are answered.

## What is in here

```
.claude-plugin/marketplace.json          the catalogue
plugins/plugin-probe/
  .claude-plugin/plugin.json             the manifest
  .mcp.json                              one remote MCP server, named probe-remote
  skills/probe/SKILL.md                  one skill that reports what loaded
```

No top-level `bin/` directory anywhere. claude.ai rejects any plugin that has one, on
sync and on direct upload both.

## Before you start

Set the repo to **private**. Organisation marketplaces reject public repos outright, and
whether a personal marketplace can read a private repo is the thing being tested.

`.mcp.json` currently points at `https://simpro-coach.fly.dev/mcp`. Change it if you would
rather probe something else. If the plugin fails to load at all, empty `mcpServers` to `{}`
and retry, which separates "the plugin is malformed" from "the MCP entry is the problem".

## Test 1: does a private repo work on a personal marketplace

The open question. Two minutes.

1. Push this repo to GitHub, private.
2. In claude.ai, open **Customize** in the left sidebar, go to the **Plugins** tab.
3. Under **Personal plugins**, click **+**, then **Add marketplace**.
4. Choose **Add from a repository** and give it `owner/workshop-ai-lab`.

Record which happened:

- It authenticated and the marketplace appeared. Private repos work on this surface.
- It asked you to connect GitHub first, then worked. Works, with a prerequisite.
- It failed or could not see the repo. Private repos do not work here. Fall back to
  Test 1b.

**Test 1b, only if Test 1 failed.** Flip the repo to public, repeat, then flip it back
to private immediately. There is nothing sensitive in here. This confirms the failure was
about visibility and not about the files.

## Test 2: does the plugin actually work in chat

1. Install `plugin-probe` from the marketplace.
2. Start a new chat. Type `/` and confirm the probe skill appears in the list.
3. Run it.

The skill reports five things. The one that matters is number three: whether a server
called `probe-remote` is present in the tool list. That is the blocking question from the
brief, answered by observation rather than argument.

Write down the answer even if it is no. A no is a finding, not a failure.

## Test 3: does a version bump propagate

1. Edit the marker string in `skills/probe/SKILL.md` from `PROBE-MARKER-A7F3` to
   `PROBE-MARKER-B2E9`.
2. Bump `version` in `plugin.json` from `1.0.0` to `1.0.1`.
3. Commit, open a pull request, merge it to the default branch.
4. Wait, then start a fresh chat and run the probe again.

If it reports the new marker, updates propagate. If it reports the old one, the update
did not reach you and you need to know why before any of this touches a client.

**Do not skip the version bump.** With `version` declared, an unchanged string means
existing installs keep the cached copy and see nothing. That silent no-op is the same
failure shape as the drift problem this project exists to fix.

## Test 4: does a broken sync do what the docs say

Only worth running once you have an organisation marketplace to break. Not applicable to
a personal marketplace.

1. Introduce a deliberate error: rename `plugin.json` to `plugin.json.bak`.
2. Commit, bump the marketplace, sync.
3. Record what a member sees, not what you expected them to see. Whether the plugin
   disappears, whether anything notifies you, how long it takes.
4. Restore the file, re-sync, then check whether installation preferences survived.

## Notes worth keeping

- Plugin skills are namespaced by plugin name, so the skill is `plugin-probe:probe`.
- Relative sources like `./plugins/plugin-probe` only resolve when the marketplace is
  added as a git repo. Add it by direct URL to `marketplace.json` and only that one file
  downloads, so relative paths break.
- Never set `version` in both `plugin.json` and the marketplace entry. `plugin.json`
  wins silently.
