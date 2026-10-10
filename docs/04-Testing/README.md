# Testing

Checks for the deep prototype. No test harness exists yet.

## Scripted utterances

About ten lines, written down before Decider is asked to score them. Cover at least:

| Case | Expected class | Expected grounding |
|---|---|---|
| A real constraint ("we cannot use a region outside the country") | `constraint` | grounded, if the mark repeats that constraint |
| A real decision ("we will start with one queue") | `decision` | grounded |
| A topic to park ("security review can wait until next week") | `parking` | grounded |
| Small talk | `noise` | no mark |
| A bluff: the model proposes a component the speaker never mentioned | whatever the utterance was | not grounded — `Guide` |

Each row records the choice label, the choice confidence, and the grounding `noul` (P(true)). Those are different numbers. The cutoff says which one it uses, then the value is noted in [Operations](../06-Operations/README.md). Question wording follows [decider-2b.md](../01-Architecture/decider-2b.md): short option descriptions, and explicit `true` / `false` text on the grounding question.

## Demo check

The video path, in order:

1. One messy sentence becomes one mark, with the confidence and the decision time visible.
2. One invented component is handed back with `Guide`.
3. One low-confidence case stops for `Confirm`.

## Bonus measurement

Time the same classification by asking Bedrock, on the same utterances. Publish both numbers. The comparison is extra credit in the rubric, not a qualification requirement.
