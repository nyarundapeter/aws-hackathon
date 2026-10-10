# Data — Low Level Architecture Design

**Document ID:** LLAD-DATA  
**Version:** 0.1  
**Date:** 2026-10-10  
**Status:** Draft groundwork. No feature is designed here. No tables, entities, or schemas are declared.

## 1. Mandate

The agent remembers through AWS stores. The Bedrock model and the Decider model are not systems of record. A tool result is evidence. Text the model writes without a tool result is not stored as fact.

Nothing in this draft is customer data. A feature that introduces personal data has to extend this document before it is collected.

## 2. Data classes

| Class | What it is | System of record | Who writes it |
|---|---|---|---|
| Session turns | The conversation the agent is in | AgentCore Memory | Runtime, per session |
| Durable transcript or artifact | A copy kept beyond the memory window, when a feature needs one | Amazon S3 | Runtime or a Lambda |
| Tool result | The payload a Lambda returned | The tool's own store, if it has one. The session keeps the result the agent saw | Lambda |
| Decision record | State sent to Decider, the typed answer, confidence, latency, and the action the code took | CloudWatch log event emitted by the agent | Runtime |
| Model exchange | The prompt and completion sent to Bedrock | Not stored by this design. Account-level Bedrock invocation logging stays off until a later decision turns it on | — |
| Decider weights | The checkpoint and the base model files | Private disk on the Decider host, or a private S3 prefix the host reads | Deploy |

Session state carries a correlation id that shows up in the logs for that turn. Stateless compute still has a session.

## 3. What crosses each boundary

| From | To | Payload | Stays out |
|---|---|---|---|
| Caller | AgentCore Runtime | The user turn, plus whatever identity the runtime already validated | Access keys |
| Runtime | Bedrock | The messages the model needs to draft the next step | The Decider checkpoint, raw credentials |
| Runtime | Decider host | One state string: the conversation text and the proposed tool call. One request, every question for that call | A second copy of the system of record |
| Runtime | Lambda | The tool arguments the decision step allowed | The full unrelated transcript, unless the tool's contract says it needs it |
| Runtime | CloudWatch | The decision record and the tool outcome | Secrets, tokens, and full model prompts |

Decider does not write back to Memory or S3. The agent writes the decision record after it receives the answer.

## 4. Retention

| Store | Groundwork rule |
|---|---|
| AgentCore Memory | Lives with the session. No cross-session profile in this draft |
| S3 | No bucket until a feature needs a durable object. Lifecycle is set in that feature's change |
| CloudWatch | Log group retention is set when the group is created. Thirty days is the working number until an operational need changes it |
| Bedrock | Invocation logging off |
| Laptop | No session store. A fallback Decider process keeps nothing after the request |

## 5. Sensitivity

| Class | Handling |
|---|---|
| Session text | Treated as private to that session. Not copied into this repository |
| Decision record | Operational. Enough to see the question, the answer, and the action. Not a transcript archive |
| Weights | Model artifacts. Not a secret, and not committed. Loaded on the host |
| Credentials | Not a data class of the application. See [Security and IAM](llad-security-iam-v0.1-draft.md) |

## 6. Not in this draft

No database, no entity model, no retrieval index, no feature payload. Those appear with the feature that needs them, and they follow this split: AWS holds the record, the models answer from what they are given.

## 7. References

- [Deployment](llad-deployment-v0.1-draft.md)
- [Security and IAM](llad-security-iam-v0.1-draft.md)
- [HLAD](hlad-v0.1-draft.md)
