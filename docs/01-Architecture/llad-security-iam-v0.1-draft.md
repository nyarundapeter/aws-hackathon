# Security and IAM — Low Level Architecture Design

**Document ID:** LLAD-SEC  
**Version:** 0.1  
**Date:** 2026-10-10  
**Status:** Draft groundwork. No feature is designed here.

Identity answers who called. Authorization answers what that caller may do. The generative model does neither. This draft separates the two, and keeps every credential inside AWS.

## 1. Mandate

| | |
|---|---|
| Inbound | A caller reaches the agent only with a credential the runtime accepts |
| Outbound | The agent reaches Bedrock, Lambda, Memory, S3, logs, and the Decider host with roles, not with keys in source or in the image |
| Decision checks | Typed allow / ask / stop decisions sit in front of a tool. They are not a second identity system |
| Laptop | No listener on the public internet. A laptop process exists only as the Decider fallback in [Deployment](llad-deployment-v0.1-draft.md) |

## 2. Identity

Two directions, same as a managed agent on AWS.

| Direction | Caller | Mechanism |
|---|---|---|
| Inbound, AWS service to the agent or gateway | Runtime, Lambda, Gateway | IAM (SigV4) |
| Inbound, a person | Signed-in user, when a feature adds one | JWT validated by AgentCore Identity. No person-login in this groundwork |
| Outbound, agent to Bedrock, Lambda, S3, logs | Runtime execution role | IAM |
| Outbound, agent to a tool that is not an AWS API | AgentCore Identity workload credential, minted per request | Secret stays in the identity vault, not in the image |
| Outbound, agent to Decider | Private network path to the in-account host | Security group, not a shared password |

Amazon Cognito, or another issuer, is how a JWT appears when a feature needs a person. It is not deployed for the groundwork.

## 3. Roles

One role per running component. Names are illustrative until a stack exists. None of them use `*` on action and resource together.

| Role | May | May not |
|---|---|---|
| Runtime execution | `bedrock:InvokeModel` and `bedrock:Converse` on the chosen model ARNs in the chosen Region; write logs; read and write the session store; call the gateway | Hold static access keys; call models outside the allow-list |
| Lambda tool role | The AWS APIs that one tool needs | Call Bedrock, unless that tool's job is a model call |
| Decider host role | Read its own model artifacts from a private bucket or from the host disk; write its own logs | Call Bedrock; accept traffic from `0.0.0.0/0` |
| Deploy role, used by the operator | Create and update the stack | Be the role the agent assumes at runtime |

Credentials for the operator use the standard AWS chain on the machine that deploys. They are not committed.

## 4. Authorization

| Check | Who decides | Where it runs |
|---|---|---|
| Can this role call this AWS API? | IAM | AWS |
| Can this principal use this tool? | A policy outside the model. Amazon Verified Permissions is the managed option when a feature needs per-principal tool sets | Beside the agent, not inside the prompt |
| Is this tool call well-formed enough to run? | Decider 2B returns a typed answer and a confidence. Application code maps that to proceed, guide, or confirm | On the private Decider path, before the tool runs |
| Confidence under the cutoff | A person | The agent stops and asks |

A missing principal, once tool filtering is turned on, means no tools. The model is not told to "try anyway."

Deny is the default for a tool the policy does not name. The model does not explain the deny in a way that leaks whether a tool exists.

## 5. Data protection

| Path | Control |
|---|---|
| Agent to Bedrock | AWS API, TLS, IAM |
| Agent to Lambda through Gateway | IAM on the gateway; function role on the tool |
| Agent to Decider | Private address in the VPC. No public listener. No authentication on the Decider server itself, so the network is the control |
| Laptop fallback | SSM port forward, or an equivalent tunnel, to that private path. The Decider port is not opened on the internet |
| S3 and log groups | Encryption at rest with the AWS default key for the account, until a feature requires a customer-managed key |
| Source tree | No account ids, access keys, session transcripts, or live prompts |

## 6. Audit

Every agent action that matters can be read later in CloudWatch: who called, which agent, which tool, when, and whether the call was allowed. AgentCore runtime and gateway log deliveries are turned on when the runtime is created (application logs, and usage logs on the runtime).

Decider's own line — the question, the answer, the confidence, the latency — is logged by the agent next to the tool-call decision, so the audit does not depend on reading the Decider host by hand.

## 7. Not in this draft

No Cedar policies, no user pool, no production threat model, no penetration test. Those start when a feature names a principal and a tool.

## 8. References

- [Deployment](llad-deployment-v0.1-draft.md)
- [Data](llad-data-v0.1-draft.md)
- [HLAD](hlad-v0.1-draft.md)
