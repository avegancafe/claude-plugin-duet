---
name: duet-basics
description: Use when working inside Claude Duet (a main chat, a channel or a thread) and unsure how plugins, threads, channels, channel instructions, repos or sessions behave there — e.g. why a plugin isn't showing up, how to pick up a newly installed plugin, how to give a channel access to a GitHub repo, or how to make a channel always use a plugin agent. Also covers how to add what you learn about Duet back into this plugin.
---

# Claude Duet basics

Facts about how Claude Duet behaves, learned while working in it. Each one was
observed, not guessed. If something here turns out wrong, fix it (see the last
section) rather than working around it silently.

## Plugins

- **Duet loads the user's claude.ai chat plugins, not their Claude Code plugins.**
  A plugin installed only in Claude Code (`/plugin install …` in the CLI) does not
  show up in Duet. To use one in Duet, install it to the claude.ai chat plugins.
- Kyle's marketplace, `avegancafe/avegancafe-marketplace`, is linked to his
  claude.ai account as an account marketplace, so its plugins (this one included)
  can be installed from there. The marketplace only lists plugins; this plugin's
  source lives in its own repo, `avegancafe/claude-plugin-duet`.
- **Plugins load when a session starts.** There is no command to resync plugins or
  restart a running session.
- **Every new thread is a fresh session.** To pick up a plugin that was just
  installed, start a new thread; the current one won't see it.
- The plugin list a channel's coordinator session reports can be empty even while
  account plugins are loading in its threads, so an empty list there doesn't prove
  a plugin is missing.

## Channels, threads and repos

- A channel's GitHub repos are added by the channel's coordinator session
  (`update_project_settings` with `add_repo`).
- The coordinator session itself can't read those repos. Threads it starts
  **after** the repo was added get them automatically.
- **A thread that started before a repo was added can still attach it** by
  calling `add_repo` (claude-code-remote tools) with that owner/repo and
  `access: "push"`. The thread's system prompt keeps listing only the repos it
  started with, but the attach works and the repo can be cloned, pushed and
  used with the GitHub tools. Try this before asking for a new thread.
- **A thread can't create a new GitHub repo** (`create_repository` is barred in
  thread sessions). When work needs a repo that doesn't exist yet, ask Kyle up
  front to create it on GitHub and add it to the channel's repos, then do the
  work in a thread started after that. Don't try to create it first.
- **A brand-new empty GitHub repo has no branch to open a PR against.** Push a
  single empty commit (`git commit --allow-empty -m "Initial commit"`) to `main`
  as the base, then put all the work on a branch and open the PR from it. That
  empty base commit is the one push to `main` that's fine; everything else goes
  through the PR.
- For deciding whether work belongs in the chat, a thread or a new channel (and
  how to run a channel's parts through its Tasks list), see
  `channels-for-complex-work`.

## Channel instructions

- A channel's lasting behavior (e.g. "always use agent X here") is set through
  the channel's instructions, not memory or a one-off brief.
- The coordinator proposes new instructions with `propose_channel_instructions`,
  which shows the user a card. They only change when the user taps Update, and
  the coordinator can't see whether they did.
- Threads don't pick an agent on their own. To make a channel always use a
  plugin agent (like Noelle), put that in the channel instructions so the
  coordinator briefs every thread to run as that agent and load its skills.

## Adding to this plugin

When you learn something specific to how Duet works (not general Claude or
Claude Code behavior), add it here so future threads and channels know it.
This plugin lives in **`avegancafe/claude-plugin-duet`**, not the marketplace
repo; don't open Duet learnings as PRs on `avegancafe/avegancafe-marketplace`.

1. Work in `avegancafe/claude-plugin-duet`. If your thread doesn't have it,
   attach it with `add_repo` and `access: "push"`. Only if that's refused, ask
   the channel's coordinator to add the repo to the channel and start a new
   thread (see "Channels, threads and repos").
2. Put the fact in the skill that fits its subject, or add a new skill at
   `skills/<name>/SKILL.md` when it's a new subject. Keep each line a fact plus,
   when useful, what to do about it. Don't record guesses.
3. List any new skill in the repo's `README.md` and bump `version` in
   `.claude-plugin/plugin.json` (minor for a new skill or fact, patch for a fix).
   Don't touch the marketplace repo: once the bump merges, this repo's release
   workflow tells the marketplace, which updates the `duet` entry itself.
4. Open a PR on `avegancafe/claude-plugin-duet` for Kyle to merge. Never push to
   `main` or merge it yourself.
