---
name: routines
description: Use in Claude Duet when creating, changing, retiring or relaying a scheduled routine (create_trigger, update_trigger, delete_trigger), or when a routine's job needs a GitHub repo, a thread or the channel's files. Covers what a routine run can't do and the nudge-then-thread pattern, leaving connectors off create_trigger, routines asked for from another channel, keeping routine posts one message, and disabling instead of deleting.
---

# Routines in Duet

Facts about scheduled routines (triggers) in Duet, learned while setting up
Kyle's routines. Each one was observed, not guessed.

## What a routine run can't do

- **A routine's run can't reach GitHub repos, can't start thread sessions, and
  doesn't see the channel's shared files (`/mnt/project-files`).** A thread in
  the channel can reach repos and the shared files; the channel's coordinator
  is the one that starts threads.
- So when a routine's job needs any of those, split it: the routine only posts
  a short nudge, and when Kyle replies (e.g. "run the sweep"), the channel's
  coordinator starts a thread that does the work. Keep the full instructions in
  a spec file under `/mnt/project-files/<routine>/` that the thread reads, not
  in the routine's prompt.

## Creating one

- **Don't pass `connectors` to `create_trigger`.** Leave the field out: runs get
  Kyle's connectors automatically. Passing a list narrows the run to only those
  (and `[]` gives it none).
- **A routine another channel's Claude asks for is blocked until Kyle says yes
  in the channel that owns it.** A `create_trigger` made on a relay from the
  main chat or another channel won't go through on that relay alone. Ask Kyle
  in the owning channel first, then create it on his yes.
- Put routines in the channel they belong to (subject routines in that subject's
  channel, everything else in his "scheduled" channel), and keep them separate
  rather than merged into one roundup unless Kyle asks.

## Posts and relays

- Empty lines in a posted message split it into separate chat bubbles (see
  `duet-basics`, "Messages"). A routine post meant to arrive as one message
  has no empty lines, and its prompt should say so.
- When relaying a routine's post, keep its layout exactly: no lead line of your
  own, and no empty lines added or removed.

## Changing or retiring one

- **To retire a routine, disable it (`update_trigger` with `enabled: false`);
  don't delete it.** Deleting wipes its run history, and the tool says it also
  deletes every session the routine started.
- A wrong prompt or schedule doesn't need delete-and-recreate either:
  `update_trigger` changes `prompt` or `cron_expression` in place and keeps the
  routine's id and history.
