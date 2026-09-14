# v2.0 research fork

The v2.0 research fork is the instrumented version of the autonomous two-agent
pipe. It preserves the sandbox fork's core behavior while adding explicit
controls and logs for exploratory, reproducible experiments.

## Source

The current source file remains at the repository root for link stability:

- [`../../llm_autonomous_chat_v2_research_fork.py`](../../llm_autonomous_chat_v2_research_fork.py)

## What v2.0 adds

- Run tag and full run header
- Per-turn prompt/context metrics
- JSONL machine-readable sidecar
- Explicit `LOOP_DETECTION` on/off valve
- Logged loop-disruption events
- Research defaults: `PRESENCE_PENALTY=0.0` and `FREQUENCY_PENALTY=0.0`
- Optional durable `SHARED_NOTE` condition
- Declared model context-window fields and a budget sanity warning
- Logged finish reasons when supplied by the backend

## Recommended first configuration

For a two-model comparison:

```text
MODEL_A_CONTEXT = [actual LM Studio window]
MODEL_B_CONTEXT = [actual LM Studio window]
MAX_CONTEXT_TOKENS = comfortably below the smaller window
TEMPERATURE = [explicit experimental value]
PRESENCE_PENALTY = 0.0
FREQUENCY_PENALTY = 0.0
LOOP_DETECTION = False
SHARE_REASONING = False
RUN_TAG = [short unique identifier]
```

Configure both local models with the same LM Studio context-window size and
same overflow policy when you want a controlled comparison. The pipe cannot
read those server settings automatically.

## Shared-note condition

Set:

```text
SHARED_NOTE = latest
SHARED_NOTE_CAPTURE_PREFIX = The arrangement, as agreed:
```

The pipe will capture the last visible line beginning with that prefix in each
participant reply and inject the current captured value into both future prompts
as a durable record. This condition is intended for experiments on agreement
retention and memory; it is not a claim that the note represents ground truth.

## Installing beside v1.9

The research-fork source currently uses the same pipe id as the sandbox source:
`two_llm_debate`. If both versions are installed in the same Open WebUI
instance, change one `pipes()` entry to a unique id and display name before
installing, for example:

```python
def pipes(self):
    return [{"id": "two_llm_research", "name": "Two LLM Research"}]
```

See [`../../methods.md`](../../methods.md) for methods, limitations, and
reporting guidance.
