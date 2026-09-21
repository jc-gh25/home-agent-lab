# Two LLM Autonomous Chat — v2.2.0 (research fork)

A research-oriented fork of the Two LLM Autonomous Chat Open WebUI Pipe. This
version is the pipe of record for new experiments; the completed heater series
in [`runs/`](../../runs/) was produced on v2.1 and is documented there.

## What changed from v2.1

1. **Anonymous participant labels in the model-facing prompt.** The quoted
   history rendered into each participant's prompt now labels turns as
   `Participant A` / `Participant B` with no model identifiers. This was
   motivated by an observation in the v2.1 heater baseline (round 2): a
   participant's reasoning briefly suspected its partner was not human,
   triggered by the partner's model identifier visible in the speaker labels.
   The `.txt` log and `.jsonl` sidecar still carry full model attribution for
   the experimenter.
2. **Local path redaction in run headers.** `LOG_DIRECTORY` is recorded as a
   placeholder, and the manual server metadata path is recorded by filename
   only. Real paths are still used at runtime for writing log files and
   duplicate-run-tag checks. API keys were already redacted in v2.1.

## Comparability caveat

This is a prompt-format change. v2.2 runs are **not strictly matched to v2.1
runs** and should be compared within v2.2. The planned first use is a
topic-anchor pair (anchor on and anchor off, plain topic, `SHARED_NOTE=off`),
which also serves as a standing test for spontaneous (uncued) suspicion now
that participant-visible model identifiers are removed.

## Setup

1. Import this file as an Open WebUI Pipe function.
2. Set `MODEL_A` / `MODEL_B` to locally served model IDs (normally via LM
   Studio's OpenAI-compatible endpoint), declare each model's real context
   window, and set `MAX_CONTEXT_TOKENS` below the smaller window.
3. Set a unique `RUN_TAG` per experiment; the pipe refuses duplicate tags by
   default.
4. Smoke test first with `MAX_ROUNDS=2` or `PRE_FLIGHT_ONLY=true` on a
   trivial topic, and verify the `.txt` header and `.jsonl` sidecar before
   the real run.

Expected smoke-test results: run header shows `pipe_version` 2.2.0,
`LOG_DIRECTORY` is redacted, round headers retain full model attribution,
and participant reasoning refers to the partner only as "Participant A/B".

## Valve notes

All v2.1 valves behave identically, including `SHARED_NOTE`, `TOPIC_ANCHOR`,
`LOOP_DETECTION` (with `REPETITION_LOOKBACK` / `REPETITION_THRESHOLD`),
`SHARE_REASONING`, `PRE_FLIGHT_ONLY`, and `ALLOW_DUPLICATE_RUN_TAG`. See
[`../../methods.md`](../../methods.md) for the full methods and limitations
discussion.

## Smoke test log

Verified locally: header redaction, log attribution retention, and
round-1 reasoning referencing the partner only as "Participant A" with no
model identifiers present.
