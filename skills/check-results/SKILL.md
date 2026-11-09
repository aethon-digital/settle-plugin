---
name: check-results
description: Read the votes and status of a Settle poll, and close it early only when the user explicitly asks. Use when the user asks "how is the vote going", "who won", "what did the group pick", or says "close the poll now".
---

# Check results

## Read the poll with `check_results`

Call `check_results` with the poll's `short_code` (the 6-character code from
`create_poll`). It is read only, and anyone with the code can read the result.

The reply gives:

- `Status`: `open` while voting continues. `settled` or `expired` once the poll has
  closed. A poll past its deadline closes when it is read.
- `Voters`: how many people have voted.
- One line per option with its yes, no and maybe votes.
- When the poll is open: how many minutes are left.
- When the poll has closed: the winner the tool names, or "No decision" when no option
  got a yes vote ("nobody voted" when no one voted at all).
- The vote link, unchanged.

Report the counts as the tool gives them. While the poll is open, say which option is in
the lead, not which one won: the group can still vote and change votes.

## Close the poll early with `settle_poll`

Call settle_poll only when the user explicitly asks to close the poll. AI proposes,
people decide: never close a poll on your own judgment, not when one option leads by a
lot, not when the deadline is near, and not to make room for a new poll.

When the user asks:

1. Call `settle_poll` with the `short_code` and the `creator_secret` that `create_poll`
   returned in this conversation. The `guest_token` does not work here.
2. The poll closes now, with the option currently in the lead as the winner.
3. If no option has a yes vote yet, `settle_poll` refuses and the poll stays open. Tell
   the user.
4. If the `creator_secret` was lost or does not match, the poll cannot be closed early.
   Tell the user it will close by itself at its deadline.

Only the poll's creator can close it early, because only the creator has the
`creator_secret`.
