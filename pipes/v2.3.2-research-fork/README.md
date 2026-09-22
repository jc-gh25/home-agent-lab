# Two LLM Autonomous Chat — v2.3.2 (research fork)

A sidecar-repair patch release of the v2.3.1 research fork. v2.3.2 does not change prompts, sampling settings, turn ordering, preflight behavior, or fail-fast policy. It fixes two logging defects: one major, one minor.

## What changed from v2.3.1

### Restored per-turn sidecar events (major)

The v2.3.0 rewrite of `append_to_log` added the new v2.3 metrics (`note_parse_status`, `generation_status`, `turn_validity`) to the human-readable `.txt` log but **accidentally dropped the `_sidecar_write` block that mirrors each turn into the `.jsonl` sidecar**. v2.3.0 and v2.3.1 therefore produced runs with complete `.txt` logs but sidecars containing **no `turn` events at all** — only `run_start`, `config_check`, `model_preflight`, `run_aborted`, `loop_detected`, and `run_end`.

**Discovery:** a v2.3.1 2-round smoke test on 2026-09-21 (run tag `topic-anchor-v2.3.1-on-001-preflight-a`) ended with `run_end` arriving 116 seconds after preflight with zero turn events, while the `.txt` contained both complete rounds. The timing gap matched four turns at observed speeds, proving turns executed and were logged to `.txt` but never reached the sidecar. Direct comparison against v2.2.0 located the dropped block; the repo contains no v2.3.x run with turn events, so the defect had never been exercised before this smoke test.

v2.3.2 restores the turn sidecar write, extended with the v2.3 fields. The restored `turn` event now carries `note_parse_status`, `generation_status`, and `turn_validity` alongside the existing content, reasoning, prompt/windowing metrics, timing fields, and note state.

### Raw output preserved in fail-fast aborts (minor)

The v2.3.x fail-fast `run_aborted` event stored only `raw_content_chars` and `raw_reasoning_chars` — the raw output itself was discarded, even though the abort status message shown to the user states that raw content/reasoning are preserved in the sidecar. v2.3.2 adds `raw_content` and `raw_reasoning` to the `run_aborted` event so the code matches its own promise.

## Smoke test log

Verified locally on 2026-09-22 (run tag `smoke-topic-anchor-v2.3.2-001`, `PRE_FLIGHT_ONLY=true`): sidecar contains two passing `model_preflight` events, followed by two complete `turn` events (Round 1, Participants A and B), each with `turn_validity: valid_visible_output`, `generation_status: visible_and_reasoning`, full content and reasoning, and all metric fields, followed by `run_end`. All 40K/50K context, temperature, penalty, anchor, and preflight valves resolved as configured.

## Version policy

Published pipe versions are frozen. Fixes land in the newest version only.

- v2.3.1 remains the model-ID-diagnostic release, but its sidecar defect means **any v2.3.0/v2.3.1 run's `.jsonl` lacks turn events** — analyze those runs from the `.txt` log, which is unaffected.
- v2.3.2 is the pipe of record for all new experiments, including the topic-anchor pair and any later shared-note or consciousness-topic series.
- Historical raw logs are never rewritten to appear as though they were produced by a later pipe version.
