# Two LLM Autonomous Chat v2.4.0 Research Fork

This is a research-oriented fork of the Two LLM Autonomous Chat Open WebUI pipe.

Version 2.4.0 extends `RUN_MANIFEST` with optional per-run controls for selected existing valves. It preserves the v2.3.2 required manifest schema and its precedence rule:

```text
manifest > preset > valves
```

A manifest value overrides a preset value; a preset value overrides the value configured in the Open WebUI pipe valves.

## What is new in v2.4.0

v2.4.0 adds 11 optional `RUN_MANIFEST` fields:

| Manifest key | Pipe valve | Type | Allowed values |
|---|---|---:|---|
| `max_tokens` | `MAX_TOKENS` | integer | 64–16000 |
| `max_original_prompt_tokens` | `MAX_ORIGINAL_PROMPT_TOKENS` | integer | 1000–256000 |
| `timeout_seconds` | `TIMEOUT_SECONDS` | integer | 10–3600 |
| `topic_anchor` | `TOPIC_ANCHOR` | Boolean | `true` or `false` |
| `loop_detection` | `LOOP_DETECTION` | Boolean | `true` or `false` |
| `repetition_lookback` | `REPETITION_LOOKBACK` | integer | 1–50 |
| `repetition_threshold` | `REPETITION_THRESHOLD` | number | 0.1–1.0 |
| `share_reasoning` | `SHARE_REASONING` | Boolean | `true` or `false` |
| `model_preflight` | `MODEL_PREFLIGHT` | Boolean | `true` or `false` |
| `fail_fast_on_invalid_visible_turn` | `FAIL_FAST_ON_INVALID_VISIBLE_TURN` | Boolean | `true` or `false` |
| `pre_flight_only` | `PRE_FLIGHT_ONLY` | Boolean | `true` or `false` |

An omitted optional field does not override a preset or a pipe valve.

## Required manifest fields

The v2.3.2 required fields are unchanged:

```text
run_tag
model_a
model_b
model_a_context
model_b_context
max_context_tokens
max_rounds
temperature
penalties
shared_note
topic
```

The `penalties` object must contain:

```text
presence_penalty
frequency_penalty
```

Old manifests containing only the required fields remain valid.

## Manifest validation

v2.4.0 applies the following rules before any model request is made:

- Unknown manifest keys are rejected and named in the validation error.
- Optional Boolean fields accept only actual JSON Boolean values: `true` or `false`.
- Optional numeric fields are checked against the existing valve ranges shown above.
- An absent optional field leaves the value inherited from the preset or the Open WebUI valve unchanged.
- The raw manifest is retained as `manifest_verbatim` in the JSONL run-start event.
- The resolved configuration records every v2.4 optional setting in `resolved_values`.

For example, these are invalid Boolean values:

```json
"topic_anchor": "true"
```

```json
"topic_anchor": 1
```

```json
"topic_anchor": null
```

This is valid:

```json
"topic_anchor": true
```

## Fields intentionally excluded

The following settings remain local/UI-only and must not be placed in a `RUN_MANIFEST`:

```text
OPENAI_BASE_URL
API_KEY
LM_STUDIO_BASE_URL
LOG_DIRECTORY
LOG_TO_FILE
SHOW_LOG_PATH
DISPLAY_FLUSH_CHARS
DISPLAY_FLUSH_SECONDS
MANUAL_SERVER_METADATA_FILENAME

ALLOW_DUPLICATE_RUN_TAG
EXPERIMENT_PRESET

SYSTEM_PROMPT_A
SYSTEM_PROMPT_B
MODERATOR_SYSTEM_PROMPT
PERSONA_A
PERSONA_B
MODERATOR_MODEL
INCLUDE_REASONING_IN_MODERATOR
SEND_TOOL_CHOICE_NONE
SHARED_NOTE_CAPTURE_PREFIX
```

This keeps endpoint credentials, local filesystem settings, UI display settings, and local prompt/persona configuration out of portable experimental manifests.

## API key behavior

`API_KEY` is a pipe valve, not a manifest field.

If the configured OpenAI-compatible endpoint requires a key, enter the same key in the pipe’s `API_KEY` valve. If the valve is blank, the pipe attempts to use `OPENAI_API_KEY` from the environment in which the Open WebUI backend is running.

Leave `API_KEY` blank only when:

- The endpoint accepts unauthenticated requests, or
- The Open WebUI backend environment defines `OPENAI_API_KEY`.

Never put API keys in `RUN_MANIFEST`, chat prompts, logs intended for sharing, or the repository.

## Example manifest

This example supplies all required fields and all v2.4 optional fields:

````text
```RUN_MANIFEST
{
  "run_tag": "v2.4-manifest-smoke-001",
  "model_a": "your-exact-model-a-id",
  "model_b": "your-exact-model-b-id",
  "model_a_context": 32768,
  "model_b_context": 32768,
  "max_context_tokens": 16000,
  "max_rounds": 1,
  "temperature": 0.7,
  "penalties": {
    "presence_penalty": 0.0,
    "frequency_penalty": 0.0
  },
  "shared_note": "off",
  "topic": "Give one brief observation about why explicit configuration provenance matters in research software.",

  "max_tokens": 512,
  "max_original_prompt_tokens": 4096,
  "timeout_seconds": 600,
  "topic_anchor": true,
  "loop_detection": false,
  "repetition_lookback": 7,
  "repetition_threshold": 0.8,
  "share_reasoning": false,
  "model_preflight": false,
  "fail_fast_on_invalid_visible_turn": true,
  "pre_flight_only": true
}
```
````

Replace `your-exact-model-a-id` and `your-exact-model-b-id` with the exact identifiers accepted by your OpenAI-compatible server.

## LM Studio on-demand loading

In some LM Studio setups, models are loaded on demand instead of being ready before the first request.

`MODEL_PREFLIGHT` sends isolated probe requests before the conversation begins. If the server rejects those probes while a requested model is still loading, set:

```json
"model_preflight": false
```

Then use:

```json
"pre_flight_only": true
```

to run one real A/B round as a live connectivity, header, and logging check.

This behavior does not mean manifest model selection failed. Confirm model selection by checking the resolved configuration and the displayed Participant A/B model identifiers.

## Logging and verification

When `LOG_TO_FILE` is enabled, set a writable `LOG_DIRECTORY` in the Open WebUI pipe valves. An empty log directory is not valid.

For test runs, set:

```text
SHOW_LOG_PATH = true
```

The pipe writes:

```text
two_llm_chat_<timestamp>.txt
two_llm_chat_<timestamp>.jsonl
```

The human-readable `.txt` log includes the pipe version, resolved configuration, manifest-applied status, and run header.

The `.jsonl` sidecar includes a `run_start` event containing:

- `pipe_version`
- `resolved_configuration`
- `manifest_verbatim`
- `all_valves_resolved`
- the selected model identifiers
- run-control values and provenance fields

## Recommended smoke test

Before a research run:

1. Configure `OPENAI_BASE_URL`, `API_KEY` if required, `LOG_DIRECTORY`, and the remaining local valves in Open WebUI.
2. Use exact model identifiers accepted by the endpoint.
3. Use a new, unique `run_tag`.
4. Set `pre_flight_only` to `true`.
5. If models load on demand, set `model_preflight` to `false`.
6. Run one A/B round on a trivial topic.
7. Check the `.txt` header and `.jsonl` `run_start` event.
8. Confirm that `pipe_version` is `2.4.0 (research fork)` and that the resolved values match the manifest.
9. Run a negative manifest test with an unknown key and confirm it fails before participant generation.

## Changelog

### v2.4.0

- Added 11 optional `RUN_MANIFEST` fields for selected existing run-control valves.
- Added unknown-key rejection for manifests.
- Added strict JSON Boolean validation for optional Boolean fields.
- Added optional numeric range validation using the corresponding valve bounds.
- Preserved compatibility with the v2.3.2 required-only manifest format.
- Preserved `manifest > preset > valves` precedence.
- Added the expanded resolved configuration to the human-readable header and JSONL run-start evidence.
- Kept credentials, endpoint configuration, local paths, display settings, prompts, personas, and other UI/local controls out of the manifest.

### v2.3.2

See the Python pipe docstring and the v2.3.2 research-fork directory for inherited behavior, limitations, and prior changelog entries.
