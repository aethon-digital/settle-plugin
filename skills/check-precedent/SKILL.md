---
name: check-precedent
description: Before creating a Settle poll, check whether the group already settled the same question. Use when the user asks "what did we pick last time", "have we decided this before", or is about to create a poll on a question the group may have asked already.
---

# Check precedent

Before you create a new Settle poll, you can look up how the same question was settled
before, among the polls that closed with a winner, so the options you propose start from
what the group chose last time.

## Tools

- `get_precedent` with `question`: the settled polls on the same question, newest
  first. Matching ignores case and punctuation ("Where to eat?" and "where to eat"
  are the same question).
- `search_decisions` with `query`: the settled polls whose question contains every
  word of the query. Omit `query` to list the newest settled polls.

Both are read only. They cover the polls created with the same guest_token (on the
remote server, pass the `guest_token` from your earlier `create_poll`; the local server
uses the one it stored). Without a guest_token there is nothing to look up yet.

Each result gives the question, the options, the option chosen, when it settled, how
many voted, and a status:

- `valid`: the newest settled poll on this question.
- `superseded`: a newer poll on the same question settled later. Prefer the newer one.

## How to use a precedent

1. Call `get_precedent` with the question the user is about to ask.
2. If there is a valid result, tell the user what the group chose and when, for example:
   "Last time (6 days ago, 4 voted) the group picked Sushi."
3. Offer to create a new poll. You may suggest including last time's choice among the
   options, and let the user change them.
4. Create the poll with `create_poll` only when the user wants one.

A past choice informs the options; it never replaces asking the group. Never present a
precedent as the group's answer to the new question, and never act on it: every result
carries `execution_authorized: false`. When the user wants a decision, the group votes
on a new poll.
