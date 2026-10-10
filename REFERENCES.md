# References

Links for this entry. Stage and priority live in `CONTEXT.md`. Rules for working in the folder live in `agent.md`. How Decider is implemented and wired lives in [`docs/01-Architecture/decider-2b.md`](docs/01-Architecture/decider-2b.md).

## Hackathon

- **Listing:** https://builder.aws.com/build/hackathons/4f50957c-1d08-37ab-b11c-97516b9adeb4/agent-experience-hackathon-strands-decider
- **Tags required on the submission:** `#strands-decider-2b` and `#policy`
- **Qualify with:** a working agent (Decider 2B for at least one meaningful decision, paired with Amazon Bedrock), a Builder Center article, a public repo containing the source and one reusable decision pattern, a demo video under three minutes, the declared pattern, and an agent that has not been published before.
- **Judging weights:** creativity 30%, effective use of Decider 2B 30%, communication 25%, originality 15%. A measured latency or cost comparison against doing the same decision on the LLM counts as a bonus under effective use. It is not required.
- **Prizes:** three winners. Each receives $5,000 in AWS credits and an AWS Builder swag bundle. Confirm eligibility (Builder Center profile, age, country, employee exclusions) in the official rules before submitting.

## Decider 2B

- **Launch post:** https://strandsagents.com/blog/introducing-strands-decider/
- **Code:** https://github.com/strands-labs/strands-decider
- **Package:** https://pypi.org/project/strands-decider/ (`strands-decider` 0.1.0)
- **Inference and HTTP API:** https://github.com/strands-labs/strands-decider/blob/main/docs/inference.md
- **Model design and confidence:** https://github.com/strands-labs/strands-decider/blob/main/docs/architecture.md
- **Strands agent example:** https://github.com/strands-labs/strands-decider/blob/main/examples/strands/README.md
- **Reference weights named in current inference docs:** https://huggingface.co/StrandsAgents/strands-decider-2B-qwen3.5-v1-2610
- **Hobson v21:** https://huggingface.co/StrandsAgents/strands-decider-2B-hobson-v21
- **Hobson v19:** https://huggingface.co/StrandsAgents/strands-decider-2B-hobson-v19

## Strands agent

- **Docs:** https://strandsagents.com/
- **Interventions:** https://strandsagents.com/docs/user-guide/sdk/agents/interventions/
- **Handler API:** https://strandsagents.com/docs/api/python/strands.interventions.handler/
- **Bedrock model provider:** https://strandsagents.com/latest/documentation/docs/user-guide/concepts/model-providers/amazon-bedrock/
