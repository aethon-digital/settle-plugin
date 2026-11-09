---
name: create-group-poll
description: Create a Settle poll when a group has to pick between options, such as where to eat, which date works or which plan to go with, and give the user one link to share. Use when the user says "let's vote on", "make a poll", "help us decide between", or asks how to get the group to choose.
---

# Create a group poll

Use this when a group has to choose between a few options and the user wants the group
to decide, not the agent.

## Before you create the poll

When the question may have been asked before ("dinner on Friday", "which park"), run the
check-precedent skill first: call `get_precedent` with the question and tell the user
what the group chose last time. A past choice can inform the options; the group still
votes on the new poll.

## Call `create_poll`

- `question`: the decision, in the group's words. Up to 140 characters.
- `options`: 2 to 6 options, each up to 200 characters. Use the options the user gives
  you. If you suggest options, show them to the user before you create the poll.
- `deadline_minutes`: optional. How long the poll stays open, from 5 minutes to 10080
  (7 days). Default 60.
- `guest_token`: on the remote server, pass the `guest_token` from your previous
  `create_poll` in this conversation, if there was one. On the first poll, leave it out
  and the tool returns one.

## What the tool returns, and what to do with it

- **The poll link.** Give it to the user exactly as the tool returned it, character for
  character. Never shorten, rewrite or rebuild it.
- **A ready-to-paste message** for the group chat, when the tool returns one. Offer it to
  the user as is.
- **The short code**, for `check_results` later.
- **A `guest_token`** (remote server). Keep it for the next `create_poll`,
  `get_precedent` and `search_decisions` call in this conversation.
- **A `creator_secret`**, shown once. It is the only key to closing the poll early with
  `settle_poll`. Keep it in this conversation. Never put it in the share message and never
  give it to voters.

## How voting works

Voters open the one link and vote in their browser, with no account and no install. On
each option they answer yes, no or maybe. Up to 10 people can vote on one poll: once 10
people have voted, a new voter is refused, and the people who already voted can still
change their votes.

The poll closes at its deadline. It names the winner when anyone has voted yes on an
option; otherwise it closes with no decision.

## Limits, and what to do when one is hit

The tool returns an error message that says what to do next. Read it to the user and
follow it:

- **3 open polls per guest_token.** A fourth `create_poll` is refused until one of the
  open polls closes at its deadline. Tell the user. Close one early with `settle_poll`
  only if the user asks you to (see the check-results skill).
- **A shared hourly limit on new guest polls.** When it is reached, the error says how
  many seconds to wait. Wait that long, then create the poll again with the same
  question and options.
- **An unknown `guest_token`.** When the error says so, call `create_poll` again without
  `guest_token` to get a fresh one.
- **Input out of range** (an empty question, fewer than 2 or more than 6 options, a
  deadline outside 5 to 10080 minutes). Fix the input with the user and try again.

## What this skill never does

It never votes for anyone, never picks the winner, and never sends the link to anyone
itself: the user shares it.
