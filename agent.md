# Identity

You are helping Peter (GitHub `nyarundapeter`) with a **personal** AWS Builder hackathon entry. This folder (`Personal-Projects/AWS_Hackathon/`) is the project home.

Parent routing: `Personal-Projects/CONTEXT.md`. Program-wide personal rules there apply here.

**This file is the operating contract** for the folder. `CONTEXT.md` holds stage and priority. `REFERENCES.md` holds links. Do not treat those as a second rule set.

## What this project is

A working agent for the AWS Builder hackathon **Agent Experience: Strands Decider**. The generative half runs on Amazon Bedrock through a Strands agent. Strands Decider 2B makes one meaningful decision locally and returns a typed answer plus a calibrated confidence number.

**Working concept: Marker.** During a working session, Bedrock proposes a mark on a shared architecture board (constraint, decision, parked topic, or component). Before the mark is committed, Decider 2B classifies the utterance and scores whether the proposed arguments are grounded in what the speaker said. Grounded marks land. Ungrounded ones are handed back so the model asks instead of drawing something nobody requested.

**Decision pattern:** `#policy`, plus the required `#strands-decider-2b` tag.

The concept is recorded as proposed in `docs/05-Decisions/ADR-0001-decider-policy-on-the-board.md`. Confirm it before any implementation.

## Folder structure


| Path             | Purpose                                                                           |
| ---------------- | --------------------------------------------------------------------------------- |
| `agent.md`       | This contract.                                                                    |
| `CONTEXT.md`     | Stage, priority, what is in the tree                                              |
| `REFERENCES.md`  | Hackathon, Decider, and Strands links                                             |
| `docs/`          | Numbered documentation spine. Plans, decisions, and submission writing live here. |
| Application code | Not created yet. Add it only when implementation is requested.                    |




## Docs spine

The numbered layout is the map for this repo: a home page, a map of content, and a stable section number for each kind of document.


| #   | Section                  | What belongs here                                                  |
| --- | ------------------------ | ------------------------------------------------------------------ |
| —   | `docs/Home.md`           | Dashboard. Start here.                                             |
| 00  | `docs/00-MOC/`           | Map of content for the vault                                       |
| 01  | `docs/01-Architecture/`  | How Marker is put together: Bedrock, Decider, tools, board         |
| 02  | `docs/02-Guides/`        | How to run it, and how someone else reuses the decision pattern    |
| 03  | `docs/03-Features/`      | The agent spec once it is written                                  |
| 04  | `docs/04-Testing/`       | Scripted utterances and the demo check                             |
| 05  | `docs/05-Decisions/`     | ADRs. One file per decision.                                       |
| 06  | `docs/06-Operations/`    | Local Decider, Bedrock access, measured decision time              |
| 07  | `docs/07-Handover/`      | Submission pack: article outline, video beats, public readme notes |
| 09  | `docs/09-Working-Notes/` | In-progress plans. Not confirmed decisions.                        |
| 99  | `docs/99-Archive/`       | Superseded notes                                                   |
| —   | `docs/Templates/`        | Blank ADR template                                                 |


A section gets its own MOC file when it holds more than its `README.md`. Until then, `docs/00-MOC/README.md` is the index.

## Routing table


| Task                                                | Read                                                        | Skip                                               |
| --------------------------------------------------- | ----------------------------------------------------------- | -------------------------------------------------- |
| What is this project / how to work here             | This file                                                   | —                                                  |
| Stage, dates, current priority                      | `CONTEXT.md`                                                | —                                                  |
| Hackathon rules                                     | `REFERENCES.md`                                             | —                                                  |
| Decider 2B implementation, integration, use cases   | `docs/01-Architecture/decider-2b.md`                        | —                                                  |
| Where a new note goes                               | `docs/Home.md`, then the section README                     | Inventing a folder outside the spine               |
| Confirm or change the agent concept                 | `docs/05-Decisions/ADR-0001-decider-policy-on-the-board.md` | Implementing before that ADR is accepted           |
| Write the agent spec                                | `docs/03-Features/` after ADR-0001 is accepted              | Writing the spec before the ADR is accepted        |
| Implement                                           | Only after Peter asks                                       | This docs pass                                     |




## Naming conventions

- Root contract stays `agent.md`. Do not add a second `CLAUDE.md` unless Peter asks.
- Notes inside `docs/`: `YYYY-MM-DD-topic.md`.
- Plans: `docs/09-Working-Notes/plans/YYYY-MM-DD-topic.md`.
- Decisions: `docs/05-Decisions/ADR-NNNN-slug.md`. Copy `docs/Templates/adr-template.md`.
- Section indexes: `README.md` in that section.



## Operating rules

- **Docs until asked.** Do not add application code, dependencies, infrastructure, or a git remote in this folder unless Peter asks for implementation.
- **Personal repo identity**, when git is set up: local `user.name` `peter.nyarunda`, `user.email` `nyarundapm@gmail.com`. Do not set global git config.
- **Public-bound writing.** The hackathon requires a public repository. Write every file in this tree as if it will be published. Keep private names, private paths, and notes from other projects out of this tree.
- **Decider decides. Bedrock writes.** Decider 2B returns a choice, a yes/no, or a score, with confidence. It does not generate the board text. Do not hand it an open-ended reasoning task.
- **One declared pattern.** The submission tag is `#policy`. Other Decider uses, if added later, stay in support of that pattern.
- **Original agent.** This entry is a new agent. The Decider weather sample shows the handler shape. It is not the submission.
- **Reusable pattern.** The public repo must include the typed questions, the option set, the Strands `InterventionHandler` wiring, and a short note on how another builder drops it in. That asset is specified under `docs/02-Guides/` when implementation starts.
- **Plans stay in** `docs/09-Working-Notes/plans/`. Confirmed choices move to `docs/05-Decisions/`. Do not leave the plan only in chat.
- **Eligibility.** Peter confirms the Builder Center profile and the hackathon's country and employee exclusions before submission. Do not assume eligibility.

