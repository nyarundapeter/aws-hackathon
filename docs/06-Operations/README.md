# Operations

Records of how the prototype is run. The running environment is AWS. See [Deployment](../01-Architecture/llad-deployment-v0.1-draft.md). A Decider process on the operator machine is the fallback only, while the in-account host does not exist. Procedures for a new reader live in [Guides](../02-Guides/README.md).

Nothing has been installed or measured yet. Install, serve, and device notes are in [Decider 2B](../01-Architecture/decider-2b.md). When a run happens, record it here:

| Record | What to write down |
|---|---|
| Decider | Package version, Hub id, revision tag, device (`cuda`, `cpu`, `mps`, or `mlx`) |
| Where it ran | Native CPU, or a WSL2 server reached at `127.0.0.1` |
| Decision time | `latency_ms` from `/v1/systemone`, median on the scripted utterances |
| Answer keys | The JSON fields for `choice` and `noul` on the first live response |
| Bedrock | Region and model id used for the board text |
| Confidence cutoff | The number the handler uses, which field it reads (`noul` or choice `confidence`), and which test rows set it |
| Comparison | Same utterances classified by Bedrock, with time and rough cost, if measured |

The published 115 ms figure is an RTX 3090 median for v19. The article uses the number measured here.
