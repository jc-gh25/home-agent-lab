# Two LLM Autonomous Chat — v2.3.1 (research fork)

A diagnostic and provenance-preserving patch release of the v2.3.0 research fork.

**v2.3.1 does not change the experiment's participant prompts, sampling settings, turn ordering, shared-note behavior, context handling, or fail-fast policy.** It adds clearer reporting when a reachable inference server rejects the configured model identifier.

## What changed from v2.3.0

### Configured-model-ID diagnostics

v2.3.0 introduced automatic `MODEL_PREFLIGHT`: before a fresh experimental run, the pipe sends an isolated probe to each configured model and requires non-empty visible output. This prevents an unavailable backend, empty stream, or reasoning-only response from becoming apparent participant silence in the experimental transcript.

During real local testing, LM Studio rejected an old configured Qwen served-model alias with the HTTP 400 message:

```text
No models loaded. Please load a model in the developer page or use the 'lms load' command.
```

The server was reachable, and the second configured model could still load and answer. The useful diagnosis was therefore not a generic transport failure; it was that the server did not make a model available for the configured identifier.

v2.3.1 adds these preflight failure classes:

| Failure class | Meaning |
|---|---|
| `no_model_available_for_configured_id` | The reachable server reports that no model is available for the configured request. In LM Studio, this can occur when a previously used served-model alias changes after an update. |
| `configured_model_id_not_found` | The server explicitly reports an unknown, missing, or nonexistent model identifier. |
| `endpoint_timeout` | The request timed out. |
| `endpoint_unreachable` | The endpoint could not be reached. |
| `server_rejected_model_request` | The reachable server rejected the request for another reason. |

For model-ID-related failures, the Open WebUI display now emits a **Model-ID diagnostic** explaining the next action:

1. In LM Studio, right-click the intended model.
2. Choose **Copy Default Identifier**.
3. Compare the copied identifier exactly with `MODEL_A` or `MODEL_B`.
4. Deliberately update the valve if needed.
5. If using `lmstudio_manual_metadata.json`, update the corresponding metadata key too.

The pipe logs `configured_model_id` and `remediation` in the `model_preflight` JSONL event.

## Important design rule

The pipe **does not guess, search for, or silently substitute model IDs**.

A near-match can represent a different served alias, model revision, quantization, or server configuration. Silent substitution would compromise experimental provenance. The experimenter must intentionally select the current server identifier and preserve that selection in the run header.

## Successful identifier-migration smoke test

This release follows a real smoke-test sequence in which an older Qwen alias failed and the current LM Studio Default Identifier succeeded:

```text
Previously configured ID:
qwen3.8-27b-turbo-fable-coldfusion-735-882-heretic-uncensored-neo-coder-max-mtp-nvfp4-q8

Current LM Studio Default Identifier:
qwen3.8-27b-turbo-fable-coldfusion-735-882-heretic-uncensored-neo-coder-max-mtp-q8
```

Using the current identifier, a v2.3 validation run successfully produced:

```text
run_start
config_check
model_preflight — A: pass
model_preflight — B: pass
turn — Round 1, A: valid_visible_output
turn — Round 1, B: valid_visible_output
run_end
```

This is a serving-configuration finding, not evidence that either historical model identity was incorrect. Historical runs remain frozen and retain the identifiers recorded in their original headers.

## Inherited v2.3 behavior

All v2.3.0 behavior remains unchanged:

- `MODEL_PREFLIGHT=True` by default for fresh runs.
- `FAIL_FAST_ON_INVALID_VISIBLE_TURN=True` by default.
- Invalid visible-output turns are not forwarded to the other participant when fail-fast is enabled.
- JSONL records `generation_status`, `turn_validity`, `note_parse_status`, and `run_aborted` events.
- The model-facing transcript uses anonymous `Participant A` / `Participant B` labels.
- Local paths are redacted from public run headers.
- The quoted-chronology guardrail echo is removed from cleaned visible output while raw evidence remains available in the sidecar.
- The turn instruction uses “Write Participant A/B's next visible message, addressed to Participant B/A” rather than the ambiguous “for Participant” wording.

See the v2.3.0 README for the full validity policy, experimental rationale, and smoke-test procedure.

## Setup

1. Import `llm_autonomous_chat_v2_3_1_research_fork.py` as an Open WebUI Pipe function.
2. In LM Studio, use the exact current **Copy Default Identifier** value for each model.
3. Set `MODEL_A` and `MODEL_B` to those exact identifiers.
4. Update `lmstudio_manual_metadata.json` keys if a served-model identifier changes.
5. Set a unique `RUN_TAG`.
6. Leave the following defaults enabled for controlled research runs:

```text
MODEL_PREFLIGHT = True
FAIL_FAST_ON_INVALID_VISIBLE_TURN = True
LOOP_DETECTION = False
SHARE_REASONING = False
```

7. Run a fresh trivial smoke test before a real experiment:

```text
PRE_FLIGHT_ONLY = True
MAX_ROUNDS = 2
SHARED_NOTE = off
```

A successful smoke-test JSONL should contain two passing `model_preflight` events before the first ordinary `turn` event.

## Version policy

Published pipe versions are frozen. Fixes land in the newest version only.

- v2.3.0 remains the first automatic-preflight and fail-fast release.
- v2.3.1 adds diagnostic clarity for server-rejected configured model IDs.
- Existing raw logs are never rewritten to appear as though they were produced by a later pipe version.
