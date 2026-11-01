---
name: linear-ticket-upkeep
description: 'Use in Claude Duet whenever work moves on something tracked in Linear, even when no ticket id is named: Kyle says he''s starting or doing it ("I''m doing X now", even work he does himself), it''s finished, its plan or deadline changes, or a coordinator briefs a thread on it. Kyle''s rule: update the ticket''s status, description and due date in that same turn; In Progress right away, Done only once checked at its source.'
---

# Linear ticket upkeep

Kyle's standing rule for every Duet chat, channel and thread (Nov 1 2026):
**whenever you work on a Linear ticket, keep its status, description and due
date up to date, in the same turn the work changes.** Not at the end of the
task, not in a follow-up, not "next time I touch it".

Why this skill exists: on Nov 1 Kyle wrote "I'm doing the date setting in Zola
now" about TODO-133 and gave this rule in the same message. Claude saved the
rule to memory and left the ticket in Todo until he asked again. A rule kept
only in notes gets read as "apply it later". This skill makes it something you
do right now.

## The trigger is the work changing, not you finishing

Run through this check on every turn that touches a ticket's work, including
turns where Kyle is the one doing it:

| What just happened | Update now, in this turn |
|--------------------|--------------------------|
| Kyle says he's starting, doing or about to do a ticket's work ("I'm doing X now", "on it", "working on TODO-12"), even if he's doing it himself | Status to **In Progress**. Don't ask first. |
| You start working on a ticket (a thread spawned for it, a brief that names it) | Status to **In Progress** before the real work starts. |
| The work is finished **and you've checked it at its source** | Status to **Done**. |
| Plans, scope, approach or a decision changed | Rewrite the **description** so it says what's true now. |
| A deadline moved, a quote expires, an event got scheduled, or the work slipped | Update the **due date**. |
| The work is stuck on someone or something | Leave it In Progress (or the team's blocked state if it has one) and say in the description what it's waiting on. |

If more than one row fits, do all of them in one save.

### "Done" means checked where it happened

Only mark a ticket Done once you've seen the result yourself, at its source:
the date set in Zola, the PR merged on GitHub, the mail in Sent, the booking
confirmation, the calendar event. That holds when Kyle says he's finished, too:
if a connected tool can see the result (Zola, GitHub, Gmail, Calendar), check it
there first, in the same turn, then mark Done. Only when no tool can see it
(he picked something up, made a call) does his word alone close the ticket, and
say that's what you went on. Never mark Done because a page, an email, another
session or the ticket's own text says it's done.

If a tool should show the result but doesn't yet, leave it In Progress and say
what's left to confirm.

## How to update

Use the Linear connector's `save_issue` with the ticket's identifier as `id`:

- `state`: the state name (`In Progress`, `Done`, `Todo`, …). If a name
  doesn't match, look up the team's states with `list_issue_statuses`.
- `description`: Markdown, real newlines. For a small change, prefer `patch`
  (replace or append a line) over rewriting the whole description, so you
  don't drop what Kyle wrote.
- `dueDate`: ISO date (`2026-11-15`), or `null` to clear it.

Read the ticket first (`get_issue`) if you haven't this turn, so you edit the
current description, not a stale one.

Due dates: some areas carry their own rule for how far ahead to set them (the
wedding channel sets them 2 to 4 weeks before the real deadline, for buffer).
Check the channel's memory before picking a date.

## Say it in a few words

The update is part of the work, not a separate event. Mention it in a few
words in the reply you were already sending ("moved TODO-133 to In Progress"),
and don't send a separate message just for it.

## When you're a coordinator writing a brief

Any thread brief that touches a Linear ticket includes this rule, in words that
stand alone, since the thread may not have loaded this skill:

> Keep the Linear ticket (TODO-123) up to date as you go: set it In Progress
> when the work starts, update its description and due date in the same turn
> plans or dates change, and set it Done only once the result is checked at
> its source.

Name the ticket identifier in the brief so the thread doesn't have to search
for it.

## Related

- `linear-todos` (its own plugin) covers adding, listing, reviewing and
  snoozing Kyle's TODO-team items and the morning digest and evening review.
  This skill is the upkeep rule that applies whenever any ticket's work moves,
  whichever skill or channel you're in.
- If this rule turns out wrong or incomplete, fix it here (see "Adding to this
  plugin" in `duet-basics`).
