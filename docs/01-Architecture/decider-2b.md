# Strands Decider 2B

How the model is implemented, how a Strands agent calls it, and which decisions it is built for. This note is the source for Marker's handler. The agent concept stays in [ADR-0001](../05-Decisions/ADR-0001-decider-policy-on-the-board.md).

**Read on 10 October 2026** from the published package, the model cards, and the code repository. Checkpoints and the reference Hub id move. Pin a revision before the demo, and record the pin in [Operations](../06-Operations/README.md). Nothing in this note was measured on our machine yet.

| Source | What it is |
|---|---|
| [Launch post](https://strandsagents.com/blog/introducing-strands-decider/) | What the model is, and the weather-tool illustration |
| [GitHub `strands-labs/strands-decider`](https://github.com/strands-labs/strands-decider) | Code, training recipe, `docs/inference.md`, `docs/architecture.md`, `examples/strands/` |
| [PyPI `strands-decider` 0.1.0](https://pypi.org/project/strands-decider/) | Apache-2.0, Python >= 3.10, uploaded 1 October 2026 |
| [Hugging Face `…-qwen3.5-v1-2610`](https://huggingface.co/StrandsAgents/strands-decider-2B-qwen3.5-v1-2610) | Reference weights named in current `docs/inference.md` |
| [Hugging Face `…-hobson-v21`](https://huggingface.co/StrandsAgents/strands-decider-2B-hobson-v21) | Later Hobson seed. Same files also under `amazon/strands-decider-2B-hobson-v21` |
| [Hugging Face `…-hobson-v19`](https://huggingface.co/StrandsAgents/strands-decider-2B-hobson-v19) | The checkpoint the 0.1.0 README still prints in its examples |
| [Strands interventions](https://strandsagents.com/docs/user-guide/sdk/agents/interventions/) | `Proceed`, `Deny`, `Guide`, `Confirm`, `Transform` |

## What it is

Strands Decider 2B is a small decision model. It reads a **state** (the text to judge) and one or more **typed questions**, and returns a choice, a yes/no probability, or a score. Each answer carries a calibrated confidence. It does not generate text.

The torso is `Qwen/Qwen3.5-2B-Base` (about 1.9 billion parameters) with a rank-16 LoRA adapter. The language-model head is removed. A pointer head of about a million parameters scores each option by comparing the hidden state at the answer position with the hidden state at that option's last token. One forward pass, no decoding loop. Option labels come from the request, so a new category does not need a retrain. Reversing the option list moves the distribution by about 0.016, because earlier options are still visible under causal attention.

The base weights download from Hugging Face on first load (about 4.5 GB). They are not inside the checkpoint.

License: Apache-2.0.

## Question types

All three types are the same softmax, read back three ways. Ask every question you need in **one** request. The state is encoded once and shared. Extra questions add their own tokens. The questions cannot see each other.

| Type | What you send | What comes back |
|---|---|---|
| `noul` | A yes/no question. Optional `criteria` for what `true` and `false` mean. | `noul`: P(true), from 0 to 1. With two outcomes that probability **is** the confidence. There is no separate confidence field. |
| `choice` | Instructions plus `criteria`: option name → what that option means. Any number of options (schema limit 255). | The top option, a probability per option, and a confidence. |
| `score` | Instructions plus an ordered rubric, lowest level first, 2–10 levels. | Expected value on that scale, the distribution, and a confidence. |

Choice confidence does not depend on how many options there are:

```
confidence = (N * p_max - 1) / (N - 1)
```

Uniform is 0. Certain is 1. Score confidence is different: it uses the spread of the distribution, so mass on two neighbouring levels counts as a precise "about halfway", not as doubt.

Published routing bands, taken from the Jev confidence notes and repeated in `docs/architecture.md`:

| Confidence | Published convention |
|---|---|
| 0.9 or above | Act |
| 0.5 to 0.9 | Confirm |
| Below 0.5 | Hand to a person |

Those bands are a starting point. The model card says the calibration was fit on held-out short classification, and that a threshold has to be measured on your own traffic. On short tasks it has not seen, answers at 0.9 or above are right about 95% of the time. Marker's cutoff stays unset until the scripted utterances in [Testing](../04-Testing/README.md) have been scored.

### CLI shape

```bash
pip install strands-decider

strands-decider ask StrandsAgents/strands-decider-2B-hobson-v19 \
  --state "Help! My payouts have been failing for 3 days!" \
  --choice "Which team should handle this?=billing,sales,retail" \
  --noul "Does this convey urgency?" \
  --score "How frustrated is the writer?=calm,frustrated,depressed"
```

`--device` is `cuda`, `mps`, `cpu`, or `mlx`. Omit it and the CLI picks cuda, then mps, then cpu. MLX is only used when asked for.

A v19 recording of that payouts line (PyPI README) returned billing at confidence 0.769, urgency 0.829, and a frustration score of 1.10 at confidence 0.519. The same line on the v21 card, on CUDA, returned billing at 0.837, urgency 0.875, and frustration 1.07 at 0.603. Same answers, different digits. A retrained checkpoint can move the numbers without moving the label.

## Checkpoints

`docs/inference.md` on `main` (read 10 October 2026) names three text checkpoints. The package README and the blog still show v19 in their sample commands. The Strands example README serves a fourth id. Pin one when we install, by Hub revision tag (`YYMMDD`), and write the id into Operations.

| Hub id | Role on 10 October 2026 |
|---|---|
| `StrandsAgents/strands-decider-2B-qwen3.5-v1-2610` | Reference weights in current `docs/inference.md`. The Strands example README serves this id on port 8099. |
| `StrandsAgents/strands-decider-2B-hobson-v21` | Representative seed of six. JevBench public 176/231 at a 4096-token window, Brier 0.323. |
| `StrandsAgents/strands-decider-2B-hobson-v19` | Single published run. JevBench public 167/231 at 3072 tokens, 168/231 at 4096. Median about 115 ms per question on an RTX 3090. |

Gemma 4 sizes (E2B, E4B, 12B, 26B-A4B) serve with the same commands but need the code on `main`. PyPI 0.1.0 does not include them. This prototype stays on the Qwen 2B line.

v21 JevBench tiers are not reprinted on the card as easy/standard/hard. v19's split of the public set, from the package README, is easy 1.000, standard 0.875, hard 0.505. Easy tasks are the ones that match a short classification. Hard, multi-step documents are the weak spot. Marker should keep the state short: the conversation plus one proposed tool call.

## Implementation

### Install

```bash
pip install strands-decider
```

Python >= 3.10. The 0.1.0 dependencies include torch >= 2.7, transformers >= 5.15 and < 6, peft, accelerate, huggingface_hub, fastapi, and uvicorn. Device extras (`cuda`, `mps`, `cpu`, `mlx`) are documented for the next release and for a clone (`pip install -e ".[mlx]"`). On PyPI 0.1.0 the declared extras are `train` and `dev`.

In Python, load a checkpoint with:

```python
from strands_decider.modeling import StrandsDeciderModel
model = StrandsDeciderModel.load("StrandsAgents/strands-decider-2B-hobson-v21")
```

The usual path for an agent is not that in-process load. It is a local server plus a small HTTP client, which is what `examples/strands/` does.

### This machine

The published GPU path is Linux, or Windows via WSL2, with an NVIDIA GPU. A server started inside WSL (`strands-decider serve … --port 8099`) is reachable from Windows at `127.0.0.1:8099` while that WSL session is alive.

CPU serving is supported (`--device cpu`) and much slower than a GPU. Apple silicon uses `--device mps` or `--device mlx`. The 115 ms figure is an RTX 3090 median for v19. An M3 Pro warm median for small tasks is about 153 ms. First request at a new length can be slower (MPS shape compile). Record whatever this machine actually does.

### Serve

```bash
strands-decider serve StrandsAgents/strands-decider-2B-qwen3.5-v1-2610 --port 8099
```

The server binds to `127.0.0.1` and has no authentication. Concurrent behaviour is not verified upstream. Use it for local experiments. `GET /health` returns the checkpoint, base model, device, and window.

`POST /v1/systemone`:

```bash
curl -s localhost:8099/v1/systemone -H 'content-type: application/json' -d '{
  "state": "Help! My payouts have been failing for 3 days!",
  "questions": {
    "is_urgent": {"type": "noul", "instructions": "Does this convey urgency?"}
  }
}'
```

A recorded 0.1.0 response:

```json
{
  "model": "strands-decider-2B-hobson-v19",
  "answers": { "is_urgent": { "type": "noul", "noul": 0.8277 } },
  "usage": { "input_tokens": 86, "output_tokens": 1 },
  "latency_ms": 140.03
}
```

Choice and score use the same `answers` object. A choice entry carries the selected label and a confidence (Builder Center sample: `"choice"` and `"confidence"`). Confirm the exact keys on the first live response and write them into Operations. The `model` field on the request is ignored; the server answers as whatever checkpoint it loaded.

A prompt longer than the window is shortened by cutting the **state** and keeping the question. `--strict-window` returns HTTP 422 instead. Empty state is allowed when the question carries the whole task.

### Limits that change how we ask

From the v21 model card and `docs/architecture.md`:

- Phrase the question so the obvious reading is the one you want. With the state and the options fixed, a reworded question often gets the same answer. The `true` / `false` descriptions on a noul carry as much signal as the question sentence. The official example writes those descriptions on purpose.
- `noul` and `score` transfer poorly onto rubrics that do not resemble the training mix. Measure our utterance classes before trusting a cutoff.
- Confidence does not mean "I could not tell". On vision probes with the image removed, the model still answers at high confidence. We send text only. Do not treat a high score as proof the state was complete.
- Long, multi-step states are the weak tier. Keep Marker's state to the conversation and the proposed arguments.
- The server is local and unauthenticated. Do not expose it.

## Integration with a Strands agent

The official example is `examples/strands/tool_call_intervention.py`, with the HTTP client in `examples/strands/_client.py`. It is marked experimental. The questions, the 0.45 threshold, and the policy were picked by hand. The README says to copy the shape and fit the questions to your own traffic.

What the example does:

1. `strands-decider serve` on port 8099. `STRANDS_DECIDER_URL` overrides the endpoint (default `http://127.0.0.1:8099`).
2. A `Decider` client posts `{state, questions}` to `/v1/systemone` and returns `payload["answers"]`.
3. Questions are built with `Decider.noul`, `Decider.choice`, and `Decider.score`.
4. A subclass of `InterventionHandler` sets a `name` attribute and overrides `before_tool_call` on the class. Strands only detects methods overridden at class level.
5. The handler builds one state from `event.agent.messages` plus `event.tool_use["name"]` and `event.tool_use["input"]`, calls `decider.ask` once, and returns `Proceed` or `Guide`.
6. The agent is `Agent(model="us.anthropic.claude-haiku-4-5-20251001-v1:0", tools=[...], interventions=[gate], ...)`. Bedrock needs AWS credentials. The decider half only needs the local server, so the Bedrock model id can change without changing the handler.

`Guide(feedback="...")` cancels the tool call and gives the model another turn with that feedback. `Deny` blocks and short-circuits later handlers. `Confirm` pauses for a person. `Proceed` lets the call run. `before_tool_call` may also return `Transform`. `before_model_call` can `Proceed`, `Deny`, `Guide`, or `Transform`, which is the hook for model routing. Marker uses `before_tool_call` on `add_mark`.

The example's two noul questions are `args_grounded` and `premature`. Its policy: if P(grounded) is below 0.45, `Guide`; else if P(premature) is at least 0.45, `Guide`; else `Proceed`. The code branches on the numbers. The model does not choose the action.

### What Marker asks

One request, two questions, on the conversation plus the proposed `add_mark` arguments. Definitions live in [Architecture](README.md).

| Key | Builder | Why this type |
|---|---|---|
| `utterance_class` | `Decider.choice`, with a one-line description per option: `constraint`, `decision`, `parking`, `noise` | Policy classification. This is the declared `#policy` pattern. |
| `args_grounded` | `Decider.noul`, with `true` / `false` descriptions in the style of the official example | Stops a mark whose arguments were invented. Same shape as the weather sample's grounding check, asked about a board mark. |

Handler mapping, cutoff still unset:

| Result | Action |
|---|---|
| class is `noise` | `Proceed`, and the tool adds nothing |
| class is `constraint`, `decision`, or `parking`, and grounded, at or above the cutoff | `Proceed` |
| arguments not grounded | `Guide` — ask which of these they want on the board |
| confidence under the cutoff | `Confirm` — the person keeps the pen |

Choice confidence and noul probability are different numbers. The test sheet records both, and the cutoff says which number it applies to.

The reusable asset another builder copies is those two question builders, the handler, and a short note. It lands under [Guides](../02-Guides/README.md) when implementation starts. The weather scenario itself is not the asset.

## Use cases

The package README names the decisions Decider is aimed at. The hackathon asks a submission to declare one of five tags. The fit:

| Hackathon tag | Decider job | Question | Where it sits |
|---|---|---|---|
| `#tool-selection` | Which tool to call | `choice` over tool names | Before the model is allowed to call, or as the router in front of the tools |
| `#guardrails` | Are these arguments grounded, safe, or in policy | `noul`, often two of them in one request | `before_tool_call` |
| `#policy` | Which category is this request | `choice` over the categories you will act on | Before the agent acts. Triage and queue routing are the same shape. |
| `#model-routing` | Frontier model or a smaller one | `choice` | `before_model_call`, then pick the Bedrock model id |
| `#context-memory` | How relevant is this chunk | `score` on an ordered relevance rubric | Before the chunk is injected |

Other published uses, outside that tag list:

| Use | Question | Notes |
|---|---|---|
| Evaluation | `noul` ("is this answer adequate?") or `score` | Cheap enough to run on every draft. v21's adequacy sets are the evidence that this is in range, and also the evidence that it is not automatic: generated-adequacy accuracy on the card is 0.788. |
| Hybrid agent | The rote decision | The LLM writes, explains, and handles the case the decider is unsure about. |
| Sentiment or urgency on a ticket | `noul` plus `score` in one request | The payouts demo. Useful as a warm-up call. It is not our submission. |

Marker declares `#policy`. The grounding noul is in the same request so a class and a grounding verdict share one forward pass. The article should say that the second question uses the guardrail shape, and that the tag is policy because the action is "what kind of utterance is this, before anything is written on the board."

### Where Decider is a poor fit

Open-ended design, prose, and multi-step reasoning stay on Bedrock. The hard JevBench tier (about half right on v19) is the warning. A question whose options need a paragraph of judgement each will look confident and be wrong. Keep each option to a short description, keep the state to the words actually spoken plus the proposed arguments, and let `Confirm` catch the middle of the confidence range.
