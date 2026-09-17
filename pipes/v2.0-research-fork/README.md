# v2.0 research fork

The v2.0 research fork is the instrumented version of the autonomous two-agent
pipe. It preserves the sandbox fork's core behavior while adding explicit
controls and logs for exploratory, reproducible experiments.

## Source

- [`llm_autonomous_chat_v2_research_fork.py`](llm_autonomous_chat_v2_research_fork.py)

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

The pipe captures the last visible line beginning with that prefix in each
participant reply and injects the current captured value into both future
prompts as a durable record. This condition is intended for experiments on
agreement retention and memory; it is not a claim that the note represents
ground truth.

## Version status

v2.0 is retained as the first instrumented research fork. For new controlled
follow-up experiments, use v2.1:

- [`../v2.1.0-research-fork/llm_autonomous_chat_v2_1_0_research_fork.py`](../v2.1.0-research-fork/llm_autonomous_chat_v2_1_0_research_fork.py)

v2.1 retains the v2.0 controls and adds experiment presets, run manifests,
duplicate run-tag protection, best-effort LM Studio/manual provenance capture,
client-side timing fields, preflight mode, API-key redaction in JSONL logs, and
a `stream_ended_without_output` label for blank backend-ended turns.

## Installing beside v1.9

The research-fork source uses the pipe id `two_llm_research`. If both versions
are installed in the same Open WebUI instance, ensure every installed pipe has
a unique `pipes()` id and display name.

See [`../../methods.md`](../../methods.md) for methods, limitations, and
reporting guidance.
