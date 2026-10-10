# Guides

How to run Marker, and how another builder reuses the decision pattern. Both are written when there is something to run.

## Reusable pattern (required by the hackathon)

The public repo includes one folder another builder can copy:

| Piece | What it contains |
|---|---|
| Typed questions | The `utterance_class` choice and the `args_grounded` yes/no, with the option text Decider sees |
| Wiring | A Strands `InterventionHandler` on `before_tool_call` for the mark tool |
| Note | How to pass conversation plus proposed arguments, how to set the confidence cutoff, and how `Guide` means "ask again" |

That folder is specified here and added to the repo when implementation starts. The questions are sketched in [Architecture](../01-Architecture/README.md). The handler shape, the HTTP client, and the official example's limits are in [Decider 2B](../01-Architecture/decider-2b.md).

## Run guide

Added after Decider is installed locally and Bedrock credentials are confirmed. Steps, model ids, and measured times go in [Operations](../06-Operations/README.md), not in this page, so the guide stays a procedure and the measurements stay a record.
