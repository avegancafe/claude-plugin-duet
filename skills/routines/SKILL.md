---
name: routines
description: "Use in Claude Duet whenever you create, reschedule, edit, pause, retire or relay a scheduled routine or recurring watch (create_trigger, update_trigger, delete_trigger), write or change a routine's prompt, or are a routine run yourself. Kyle's rule: every routine post opens with a 'Routine: <kebab-name>' line and ends with an '[endof <kebab-name>]' line. Also covers what a run can't reach (repos, threads, the channel's files) and the nudge-then-thread pattern, leaving connectors off create_trigger, routines asked for from another channel, one-message posts, and disabling instead of deleting."
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

- **Every routine post opens with a header line and ends with a footer line**
  (Kyle's rule, for all Duet channels), so he can tell where a routine's output
  starts and ends:

  ```
  Routine: <routine-name-in-kebab-case>
  ...the routine's post...
  [endof <routine-name-in-kebab-case>]
  ```

  The angle brackets only mark where the name goes: a routine called
  "Morning digest" opens with `Routine: morning-digest` and ends with
  `[endof morning-digest]`. Keep the square brackets in the footer; send no
  angle brackets, XML or other markup. Use the same name in both lines.
- When creating or updating a routine (`create_trigger`, `update_trigger`),
  its prompt tells the run to post with this header and footer, giving the
  exact name to use. When you touch an existing routine whose prompt doesn't
  say so yet, add it.
- When relaying a routine's post, add the header and footer if the post is
  missing either one; don't add them twice.
- Empty lines in a posted message split it into separate chat bubbles (see
  `duet-basics`, "Messages"). A routine post meant to arrive as one message
  has no empty lines, and its prompt should say so.
- When relaying a routine's post, keep its layout exactly: no lead line of your
  own (the `Routine:` header is the only first line), and no empty lines added
  or removed.

## Changing or retiring one

- **To retire a routine, disable it (`update_trigger` with `enabled: false`);
  don't delete it.** Deleting wipes its run history, and the tool says it also
  deletes every session the routine started.
- A wrong prompt or schedule doesn't need delete-and-recreate either:
  `update_trigger` changes `prompt` or `cron_expression` in place and keeps the
  routine's id and history.
