# duet

How [Claude Duet](https://claude.ai/messages) works, written for the Claude agents
running inside it. Install it to your **claude.ai chat plugins** (not Claude Code)
so every new Duet thread and channel starts out knowing how plugins, threads,
channels, repos and sessions behave there.

It lives in its own repo, [`avegancafe/claude-plugin-duet`](https://github.com/avegancafe/claude-plugin-duet),
and is listed in [`avegancafe-marketplace`](https://github.com/avegancafe/avegancafe-marketplace)
(it was vendored there under `plugins/duet/` until 0.3.0; its history came with it).

The plugin grows as Duet behavior is learned: whenever a Duet session learns
something Duet-specific, it adds or updates a skill here through a PR on this repo.

## Install

Install `duet` from the `avegancafe-marketplace` account marketplace in your
claude.ai chat plugins. It takes effect in threads started after that.

## Skills

| Skill | Purpose |
|-------|---------|
| `duet-basics` | Where Duet's plugins come from and when they load, how threads and sessions relate, how a channel's repos are attached, how channel instructions set lasting behavior (like always using a plugin agent), and how to add new learnings to this plugin. |
| `channels-for-complex-work` | Kyle's rule for where work goes in Duet: complex operations with several coordinated tasks get their own channel, with each part a task on that channel's Tasks list, so the main chat stays clean. Covers when a direct answer or one thread is enough, the handover brief, and steering the channel from the main chat. |

## Contributing

Add a skill under `skills/<name>/SKILL.md` (or extend the one that fits), list it in
the table above, bump `version` in [`.claude-plugin/plugin.json`](.claude-plugin/plugin.json)
(minor for a new skill or fact, patch for a fix) and open a PR. CI runs
`claude plugin validate .` and checks every skill is listed here.

## Releasing

Merging a `plugin.json` version bump to `main` is the release.
[`release.yml`](.github/workflows/release.yml) then sends a `plugin-released` event to
`avegancafe-marketplace`, whose sync workflow updates this plugin's entry there.

It needs the `MARKETPLACE_DISPATCH_TOKEN` repo secret: a fine-grained PAT scoped to
`avegancafe/avegancafe-marketplace` only, with Contents read & write.

```bash
gh secret set MARKETPLACE_DISPATCH_TOKEN -R avegancafe/claude-plugin-duet
```

Without it the release run fails and the marketplace entry stops tracking new versions.
