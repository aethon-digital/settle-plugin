# Settle group decisions

Settle helps a group pick between a few options. Ask your agent to make a poll, share the
one link it gives you, and your friends vote in their browser, with no app or account. The
agent reads the result back to you. The agent proposes; the group decides.

This plugin connects to Settle's remote MCP server at `https://settle.ink/api/mcp`
(Streamable HTTP, no sign-in) and adds three skills that tell the agent how to use it.

## Tools

| Tool | What it does |
| :- | :- |
| `create_poll` | Creates a poll from a question (up to 140 characters) and 2 to 6 options, open for 5 minutes to 7 days (default 60 minutes). Returns the link to share, a ready-to-paste message, a short code, a `guest_token` and a `creator_secret`. |
| `check_results` | Reads a poll's status, voter count and yes/no/maybe votes per option, and the winner once it has closed. Read only. |
| `settle_poll` | Closes a poll before its deadline with the option in the lead. Needs the `creator_secret`. Refuses when no option has a yes vote. |
| `get_precedent` | Finds how the same question was settled before, among the polls created with your `guest_token`. Read only. |
| `search_decisions` | Searches those settled polls by words in the question. Read only. |

## Skills

- `create-group-poll`: when to make a poll, how to call `create_poll`, the limits, and
  sharing the returned link exactly as given.
- `check-results`: reading `check_results`, and calling `settle_poll` only when the user
  explicitly asks to close the poll.
- `check-precedent`: looking up what the group chose last time before asking again.

## How voting works, and the limits

- Voters open the link and vote in their browser, with no account and no install. They
  answer yes, no or maybe on each option.
- Up to 10 voters per poll.
- Up to 3 open polls per `guest_token` at a time.
- A shared hourly limit on new guest polls across all users. When it is reached, the
  error says how many seconds to wait.
- The poll closes at its deadline and names the winner when anyone has voted yes;
  otherwise it closes with no decision.

## Privacy

The plugin itself stores nothing on your computer. Every call goes to
`https://settle.ink/api/mcp`, which calls Settle's API at `https://settle.ink/api/v1`.

- Settle stores each poll: its question, options, deadline and votes, a `guest_token`
  that groups the polls you create, a hash of the `creator_secret`, and which client
  created the poll (a client label and the User-Agent, truncated).
- Settle records product analytics events for guest polls, such as a poll being created
  or a limit being hit, with the client label.
- The `guest_token` is not an account and carries no name or email. Your agent keeps it
  in the conversation and sends it with later calls, so the 3-open-polls limit and the
  precedent lookups apply to your own polls.
- Voters are identified by a token tied to their browser, not by an account.

The full policy is at https://settle.ink/privacy and the terms at https://settle.ink/terms.

## Support

https://settle.ink/support

## License

MIT
