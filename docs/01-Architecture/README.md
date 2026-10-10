# Architecture

## Substrate

AWS is the running environment. These drafts name no feature. They place the Strands agent on AgentCore, generation on Bedrock, tools on Lambda, and Decider on a private in-account host. A laptop process is only a last resort for Decider.

| Document | What it settles |
|---|---|
| [HLAD](hlad-v0.1-draft.md) | The short view |
| [Architecture LLAD](llad-architecture-v0.1-draft.md) | Components, the turn, failure |
| [Data LLAD](llad-data-v0.1-draft.md) | What is stored, and where |
| [Security and IAM LLAD](llad-security-iam-v0.1-draft.md) | Who calls, what is allowed |
| [Deployment LLAD](llad-deployment-v0.1-draft.md) | What runs in the account |

How Decider 2B is called: [decider-2b.md](decider-2b.md).

## Feature concept

How Marker is put together. The decision to build it this way is [ADR-0001](../05-Decisions/ADR-0001-decider-policy-on-the-board.md). The substrate above is the runtime. This section is the proposed behaviour, and it is still a proposal.

## Intended shape

| Piece | Role |
|---|---|
| Strands agent | Holds the tools and the conversation |
| Amazon Bedrock | Writes the short board text and proposes one mark |
| Strands Decider 2B | Classifies the utterance and scores whether the mark is grounded. Returns confidence. Does not write prose. Runs on the in-account host from the deployment draft. |
| `InterventionHandler.before_tool_call` | Runs on `add_mark` before the tool commits anything |
| Board | Shows the marks that were allowed through |

## Questions Decider is asked

Both questions are asked on the same state (the conversation plus the proposed tool arguments).

| Id | Question | Type | Options |
|---|---|---|---|
| `utterance_class` | What kind of utterance is this? | `choice` | `constraint`, `decision`, `parking`, `noise` — each option needs a one-line description in `criteria` |
| `args_grounded` | Are the proposed mark's arguments grounded in what the speaker said? | `noul` | `true` / `false`, each with a description. The answer is P(true), which is the confidence. |

## Handler outcomes

| Decider result | Handler returns |
|---|---|
| `noise` | `Proceed`, and the tool does not add a mark |
| grounded `constraint`, `decision`, or `parking` | `Proceed` |
| arguments not grounded | `Guide` — ask which of these they actually want on the board |
| confidence under the cutoff | `Confirm` — the person keeps the pen |

The cutoff number is chosen after the scripted utterances in `docs/04-Testing/` have been scored. It is not invented here.

## What this section will hold later

Tool arguments for `add_mark`, the exact state string passed to Decider, and where the board is drawn. The question builders and the handler follow [decider-2b.md](decider-2b.md). No code until implementation is requested.
