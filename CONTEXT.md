# AWS Hackathon — Context

Operating contract: `agent.md`. Links: `REFERENCES.md`. Decider research: `docs/01-Architecture/decider-2b.md`.

## What this folder is for

Peter's entry to the AWS Builder hackathon **Agent Experience: Strands Decider**. A Strands agent uses Amazon Bedrock for language and Strands Decider 2B for one policy decision. The public submission is an original agent, a Builder Center article, a public repo with a reusable decision pattern, and a demo video under three minutes.

## Stage

**Docs only.** No application code yet.

| Item | Detail |
|---|---|
| Working name | Marker |
| Pattern | `#policy` and `#strands-decider-2b` |
| Stage | Deep prototype — can the decision work in a demo? |
| Status of the concept | Proposed. See ADR-0001. |
| Hackathon launch | 16 October 2026 |
| Projects due | 23 October 2026 |
| Gate 1 | Week of 26 October 2026 |
| Gate 2 | Week of 2 November 2026 |
| Winners | Week of 9 November 2026 |

Deep prototype, for this entry, means: one board, English, a single operator, curated example utterances, console or a small UI, and a measured decision time on Peter's machine. Accounts, multi-user sessions, persistence across days, and production hardening wait until a later project.

## Who decides what

| Decision | Owner | Why |
|---|---|---|
| What kind of utterance is this? (`constraint`, `decision`, `parking`, `noise`) | Decider 2B | Typed choice. Fast. Repeated on every mark. |
| Are the proposed mark's arguments grounded in what the speaker said? | Decider 2B | Yes/no plus confidence. Asked in the same pass as the classification. |
| Wording of the mark on the board | Amazon Bedrock | Prose. Decider does not generate text. |
| Confidence under the cutoff Peter sets | Human (`Confirm`) | The pen stays with the person in the room. |

## Current priority

As of 10 October 2026:

1. **Vault is in place** — `agent.md`, this file, `REFERENCES.md`, and the docs spine.
2. **Accept or revise ADR-0001** before any code.
3. **Through 15 October** — after acceptance, prove Decider on about ten scripted utterances locally. Continue only if the grounding yes/no is reliable enough for a live demo.
4. **16–20 October** — agent, Bedrock, intervention handler, small board. Implementation starts only when Peter asks.
5. **21–22 October** — video, article, public repo, both tags.
6. **23 October** — submit.

## What's in the tree

```
AWS_Hackathon/
  agent.md
  CONTEXT.md
  REFERENCES.md
  docs/
    Home.md
    00-MOC/README.md
    01-Architecture/README.md
    01-Architecture/hlad-v0.1-draft.md
    01-Architecture/llad-architecture-v0.1-draft.md
    01-Architecture/llad-data-v0.1-draft.md
    01-Architecture/llad-security-iam-v0.1-draft.md
    01-Architecture/llad-deployment-v0.1-draft.md
    01-Architecture/decider-2b.md
    02-Guides/README.md
    03-Features/README.md
    04-Testing/README.md
    05-Decisions/README.md
    05-Decisions/ADR-0001-decider-policy-on-the-board.md
    06-Operations/README.md
    07-Handover/README.md
    09-Working-Notes/README.md
    09-Working-Notes/plans/2026-10-10-hackathon-build-window.md
    99-Archive/README.md
    Templates/adr-template.md
```

No git repo has been created in this folder yet.

## What good looks like

- A reader can start at `docs/Home.md` and find the concept, the pattern tag, and the next action.
- The reusable decision pattern is specified clearly enough that another builder could drop it into their own Strands agent.
- The article and the video can be drafted from `docs/07-Handover/`.
- Decision time is measured on the machine that will run the demo, and that number is what the article uses.

## Names

`AWS_Hackathon` and **Marker** are working titles. Both are broad for a public repo. A rename is open; see the proposal in the 10 October plan. Do not rename the folder until Peter picks one.

## What to avoid

- Resubmitting the Decider weather sample as this agent.
- Writing code, adding dependencies, or opening a public remote before Peter asks.
- Putting private names, private paths, or notes from other projects in any file here.
