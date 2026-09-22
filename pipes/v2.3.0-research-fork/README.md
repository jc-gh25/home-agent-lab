# Two LLM Autonomous Chat — v2.3.0 (research fork)

A research-oriented fork of the Two LLM Autonomous Chat Open WebUI Pipe. This
version is the pipe of record for new experiments. It forks v2.2.0 directly
and is a strictly additive change: v2.2's anonymous participant labels and
local-path redaction are unchanged, and no existing valve's default behavior
was altered except where noted below.

## Why this version exists

On 2026-09-21, a v2.1 run (`heater-v2.1-sharednote-002`) failed at the first
turn: Participant A's stream ended with zero visible characters and zero
reasoning characters in 0.016 seconds. The harness recorded this as a normal
turn, injected a `[No visible final answer was produced.]` placeholder into
Participant B's next prompt, and the run continued for several more rounds
before the experimenter noticed. A later turn in the same run was logged as
`completion_status: "output_received"` despite producing zero visible
characters (986 characters of reasoning, no answer) — a second, distinct
failure mode the v2.2 schema could not distinguish from a successful turn.

Neither failure was a bug in v2.2's logging; both were fully captured in the
JSONL sidecar. The gap was **interpretive**: v2.2 had no way to say "this
turn should not count," so the harness treated a transport/model failure as
if it were conversational silence and passed it forward into the next
participant's social context.

## What changed from v2.2

1. **`MODEL_PREFLIGHT` (new valve, default `True`).** Before Round 1 of a
   fresh run (not a continuation), the pipe sends one isolated, non-
   experimental probe request to each of `MODEL_A` and `MODEL_B` and
   requires non-empty **visible** content back. If either model fails —
   empty stream, reasoning-only output, or a transport/HTTP error — the run
   aborts before any experimental turn is generated. Each probe result is
   logged as a `model_preflight` sidecar event. This is distinct from the
   existing `PRE_FLIGHT_ONLY` valve (unchanged), which is a manual, one-
   round *experimental* smoke test that still exercises the full message-
   assembly and logging path.

2. **Turn-level output validity classification.** Every turn now logs two
   new fields in addition to the existing `completion_status` and
   `finish_reason`:
   - `generation_status`: `visible_and_reasoning`, `visible_only`,
     `reasoning_only`, or `empty`.
   - `turn_validity`: `valid_visible_output` or `invalid_no_visible_output`.

3. **`FAIL_FAST_ON_INVALID_VISIBLE_TURN` (new valve, default `True`).** If a
   turn is `invalid_no_visible_output`, the run aborts immediately with an
   explicit `run_aborted` sidecar event (`reason`, `round`, `participant`,
   `generation_status`, raw content/reasoning character counts). The failing
   turn is **not** appended to the transcript and is **not** shown to the
   other participant as a placeholder. Setting this to `False` restores
   exact v2.2 behavior: the invalid turn is recorded with the placeholder
   `[No visible final answer was produced.]` and the run continues.

4. **Shared-note parse status.** When `SHARED_NOTE="latest"`, every turn now
   logs `note_parse_status`: `captured`, `missing_required_prefix`,
   `malformed_note_line`, or `not_applicable`. Previously, `note_captured`
   being `null` was ambiguous between "the note feature is off" and "the
   participant forgot the required line."

5. **Turn-instruction wording.** Changed from *"Write only your next
   visible contribution for Participant {other_id}"* to *"Write Participant
   {self_id}'s next visible message, addressed to Participant {other_id}"*,
   to reduce the "for" = "on behalf of" pronoun-binding ambiguity observed
   in v2.1/v2.2 heater logs (a participant's private reasoning drifting into
   writing as if it were the other participant).

6. **`clean_visible_reply()` gained one additional pattern.** It now also
   strips a leading, verbatim echo of the harness's own quoted-chronology
   guardrail sentence, matching an artifact observed once in a v2.1
   baseline log (round 3, `two_llm_chat_2026-09-17_02-39-20`), where a model
   echoed the harness's own prompt-injection warning as its visible reply.

All other v2.2 behavior — anonymous participant labels in the model-facing
prompt, local path redaction in run headers, `SHARED_NOTE`, `TOPIC_ANCHOR`,
`LOOP_DETECTION`, experiment presets, manifests, `PRE_FLIGHT_ONLY`,
`ALLOW_DUPLICATE_RUN_TAG` — is unchanged.

## Validity and exclusion policy

For any analysis built on v2.3 logs:

- A run containing a `run_aborted` event ended there. Rounds before the
  abort are valid and analyzable; nothing after the abort exists.
- A run's `model_preflight` events are diagnostic, not experimental data. Do
  not include them in outcome analysis.
- With default settings, every turn in a completed run has
  `turn_validity: "valid_visible_output"` by construction — an invalid turn
  would have ended the run. This makes v2.3 completed runs strictly cleaner
  than v2.1/v2.2 runs for this reason alone.
- If you deliberately set `FAIL_FAST_ON_INVALID_VISIBLE_TURN=False` for an
  exploratory run, filter on `turn_validity` before computing any statistic
  that assumes every turn was a genuine visible reply.

## Setup

1. Import this file as an Open WebUI Pipe function.
2. Set `MODEL_A` / `MODEL_B` to locally served model IDs, declare each
   model's real context window, and set `MAX_CONTEXT_TOKENS` below the
   smaller window.
3. Set a unique `RUN_TAG` per experiment; the pipe refuses duplicate tags by
   default.
4. Leave `MODEL_PREFLIGHT=True` for controlled experiments. Run a fresh chat
   with a trivial topic first and confirm both preflight probes show `PASS`
   before starting a real run.
5. Smoke test with `MAX_ROUNDS=2` or `PRE_FLIGHT_ONLY=true` on a trivial
   topic, and verify the `.txt` header and `.jsonl` sidecar before the real
   run, as in prior versions.

Expected smoke-test results: run header shows `pipe_version` `2.3.0 (research
fork)`; two `model_preflight` events with `result: "pass"` appear in the
sidecar before the first `turn` event; round headers retain full model
attribution in `.txt`/`.jsonl` logs; participant reasoning refers to the
partner only as "Participant A/B" (unchanged from v2.2); `LOG_DIRECTORY` is
redacted in the printed header (unchanged from v2.2).

## Comparability caveat

This version changes turn-instruction wording (item 5 above) and adds a
scaffold-echo cleanup pattern (item 6). Both are prompt-format changes.
**v2.3 runs are not strictly matched to v2.1 or v2.2 runs** on these two
specific dimensions and should primarily be compared within v2.3, in the
same way v2.2 documented for its own anonymization change.

## Known limitations (carried over, plus new)

- All v2.2 known limitations apply unchanged (token estimation is chars/4;
  shared-note capture is a plain-text prefix scan; two models agreeing
  establishes nothing about truth).
- `MODEL_PREFLIGHT` only runs before a *fresh* run (`last_round == 0`), not
  before a continuation. A continuation implicitly assumes both models were
  already reachable in the run being continued.
- The preflight probe uses a fixed, trivial prompt ("Say hello in one short
  sentence"). It is a connectivity/format check, not a proxy for how the
  model will behave under the actual experimental prompt and context load.
- `FAIL_FAST_ON_INVALID_VISIBLE_TURN=True` means a single bad turn ends the
  whole run. For long unattended runs, this trades completed-round-count for
  interpretability. That trade is deliberate for this project's goals.

## Verification performed before this release

Before this file was proposed for commit, the following were checked
programmatically against the actual modified source (not just described):

- The file parses as valid Python (`ast.parse`).
- All functions present in v2.2 remain present in v2.3, unchanged in name
  and signature, plus three new ones.
- A scripted mock of the actual `sharednote-002` failure pattern (Participant
  A: empty stream; Participant B: reasoning-only) was run through the new
  `pipe()` logic end-to-end. Confirmed: `MODEL_PREFLIGHT` would have caught
  this exact scenario before Round 1; and with preflight passing but a later
  turn invalid, `FAIL_FAST_ON_INVALID_VISIBLE_TURN` stops the run at exactly
  the failing turn, writes the correct `run_aborted` sidecar event, and never
  issues the next model request.
- Confirmed `FAIL_FAST_ON_INVALID_VISIBLE_TURN=False` reproduces the exact
  v2.2 placeholder-and-continue behavior, unchanged.
- Confirmed `clean_visible_reply()` strips the scaffold-echo artifact
  observed in the v2.1 baseline log.
- Confirmed `shared_note_parse_status()` correctly classifies all four
  states against synthetic inputs.

This is a code review record, not a substitute for running the documented
smoke test against real local models before the next real experiment.
