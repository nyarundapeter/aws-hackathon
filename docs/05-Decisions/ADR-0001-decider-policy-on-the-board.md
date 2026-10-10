# ADR-0001 — Decider 2B owns board policy; Bedrock writes the mark

| | |
|---|---|
| Status | Proposed |
| Date | 2026-10-10 |
| Pattern tag | `#policy` |
| Hackathon tag | `#strands-decider-2b` |

## Context

The AWS Builder hackathon requires an original agent that uses Strands Decider 2B for at least one meaningful decision, paired with Amazon Bedrock for generation. Submissions are judged heavily on creativity and on how well Decider is used. A reusable decision pattern must ship in the public repo.

Decider returns a choice, a yes/no, or a score, with confidence, in a single pass. It does not generate text. It is a poor fit for open-ended design reasoning. It is a good fit for a repeated classification that sits in front of a tool call.

## Decision

Build **Marker**, a working-session agent.

- Bedrock proposes the words of one mark and the tool call `add_mark`.
- Decider 2B, running locally, answers two questions on the same state before that tool runs: what kind of utterance this is (`constraint`, `decision`, `parking`, `noise`), and whether the proposed arguments are grounded in what the speaker said.
- A Strands `InterventionHandler` on `before_tool_call` turns those answers into `Proceed`, `Guide`, or `Confirm`.
- The submission declares `#policy`.

The questions, the handler, and a short reuse note are the reusable asset.

## Why this decision

The decision is bounded, repeated on every mark, and visible in a three-minute demo: a real sentence becomes a mark, an invented component does not. Confidence and decision time can be shown on screen. That matches what Decider is built to do, and it gives another builder a policy check they can put on their own tool path.

## Consequences

- Implementation waits until this ADR is accepted.
- The confidence cutoff is set from the scripted utterances in `docs/04-Testing/`, not chosen in advance.
- Latency published in the article is measured on Peter's machine.
- The official weather-tool sample is not the agent and not the reusable pattern.
- Accounts, multi-user boards, and cross-session memory are out of this prototype.

## Alternatives considered

| Alternative | Why it was set aside |
|---|---|
| Republish an existing sample agent | The rules require an original agent that has not been published. |
| Use the launch-post weather grounding check as the entry | It is the illustration every reader of the Decider post has already seen. Marker asks whether a mark belongs on a shared board. |
| `#model-routing` or `#guardrails` as the declared tag | Grounding is part of the check, and a later measurement may compare Decider with Bedrock. The act the agent performs is classifying the utterance before it touches the board, so the declared pattern is policy. |
