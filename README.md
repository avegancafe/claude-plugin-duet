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
| `duet-basics` | Where Duet's plugins come from and when they load, how threads and sessions relate, how a channel's repos are attached, how channel instructions set lasting behavior (like always using a plugin agent), and how messages split into bubbles, how to tell two accounts' connector tools apart, and how to add new learnings to this plugin. |
| `routines` | Scheduled routines in Duet: runs can't reach repos, threads or the channel's files (so they post a nudge and a thread does the work), leave `connectors` off `create_trigger`, routines asked for from another channel need Kyle's yes in the owning channel, posts stay one message and open with a `Routine: name` header and end with an `[endof name]` footer, and old routines get disabled, not deleted. |
| `channels-for-complex-work` | Kyle's rule for where work goes in Duet: complex operations with several coordinated tasks get their own channel, with each part a task on that channel's Tasks list, so the main chat stays clean. Covers when a direct answer or one thread is enough, the handover brief, and steering the channel from the main chat. |
| `linear-ticket-upkeep` | Kyle's rule for Linear in every Duet chat: whenever work on a ticket changes, update its status, description and due date in that same turn. In Progress as soon as work starts (even when Kyle is doing it himself), Done only once the result is checked at its source, and the rule goes into any thread brief that touches Linear. |

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
