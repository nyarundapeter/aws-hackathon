# Hackathon build window

**Date:** 2026-10-10  
**Status:** plan, not a decision. The concept decision is [ADR-0001](../../05-Decisions/ADR-0001-decider-policy-on-the-board.md).

## Dates

| When | Work |
|---|---|
| 10–15 October 2026 | Docs are in place. Accept or revise ADR-0001. After acceptance, score about ten scripted utterances with local Decider. Keep the concept only if the grounding yes/no survives a live demo. |
| 16 October 2026 | Hackathon opens. |
| 16–20 October 2026 | Implement, only when asked: Strands agent, Bedrock, intervention handler, small board. |
| 21–22 October 2026 | Video, article, public repo, both tags. |
| 23 October 2026 | Submit. |

## Names

`AWS_Hackathon` is a container name. **Marker** is a working title for the agent. Both are broad for a public repository. Pick one folder name before the repo is created. Candidates, with the agent name matching the folder:

| Folder | What a reader hears |
|---|---|
| `grounded-board` | A mark lands only when the utterance is in policy and the arguments were actually said |
| `session-policy` | The decision is a policy class on each turn of a working session |
| `mark-check` | The check that runs before a mark is committed |
| `board-gate` | The intervention in front of the board tool |

`grounded-board` is the one that says what the demo shows. Leave the folder as `AWS_Hackathon` until that choice is made.

## Order

1. Folder name chosen. ADR-0001 accepted or replaced.
2. Utterance sheet in `docs/04-Testing/`.
3. Local Decider scores, written into `docs/06-Operations/`.
4. Code, when requested.
5. Submission pack in `docs/07-Handover/`.

## Done when

A public repo shows Marker making the policy decision, the reusable pattern folder is in that repo, the article and the video match what the repo does, and both tags are on the submission.
