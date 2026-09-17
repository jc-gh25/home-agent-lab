# v2.1.0 research fork

The v2.1.0 research fork is the current controlled-experiment version of the
autonomous two-agent Open WebUI pipe.

## Source

- [`llm_autonomous_chat_v2_1_0_research_fork.py`](llm_autonomous_chat_v2_1_0_research_fork.py)

## What v2.1 adds

v2.1 retains the v2.0 research instrumentation and adds:

- Experiment presets for matched conditions
- Per-run manifests
- Duplicate `RUN_TAG` protection
- `PRE_FLIGHT_ONLY` mode for short logging checks
- Best-effort LM Studio provenance capture, with optional manual metadata
- Client-observed prompt-prefill and generation timing fields
- API-key redaction in logged valve settings
- `stream_ended_without_output` labeling for a turn that ends with no visible
  output, reasoning, or backend finish reason

## Current experiment

The current controlled comparison is a shared heater scenario:

| Condition | `SHARED_NOTE` setting |
|---|---|
| Heater baseline | `off` |
| Heater shared-note condition | `latest` |

Keep the models, system prompts, topic wording, sampling settings, context
settings, loop-detection setting, and run length the same in both conditions.
The intended difference is whether the harness retains and reinjects a durable
shared record.

## Shared-note condition

When `SHARED_NOTE = latest`, the pipe scans each visible reply for a line
beginning with the configured prefix:

```text
The arrangement, as agreed:
```

The last matching line in a reply becomes the current shared record. On later
turns, that record is injected before the windowed quoted conversation history.

The task prompt must ask participants to state this agreement line; otherwise,
there is no note for the pipe to capture. A shared note is a condition for
studying coordination and memory persistence. It is not a claim that the note
is true or authoritative outside the conversation.

## Before a full run

Use `PRE_FLIGHT_ONLY = True` first to verify that the text log and JSONL
sidecar are being written correctly.

For controlled comparisons, record the actual context-window size and overflow
policy of both loaded local models. Set `MAX_CONTEXT_TOKENS` below the smaller
server context window so the pipe's logged windowing behavior—not hidden
server-side truncation—determines which transcript turns remain visible.

See [`../../methods.md`](../../methods.md) for methods, limitations, and
reporting guidance.
