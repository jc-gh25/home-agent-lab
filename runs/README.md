# Experiment log index

This directory contains selected raw local two-agent conversation logs.
Timestamped file names preserve their original creation time. The summaries
below are navigation aids, not conclusions.

## How to read a run

1. Read the file header first: topic, model identifiers, requested rounds, and
   available settings are part of the condition.
2. Treat the transcript as evidence of what happened in that one run, not proof
   that the behavior generalizes.
3. Check [`../methods.md`](../methods.md) for differences among the v1.9
   sandbox fork, v2.0 research fork, and v2.1 research fork.
4. Where a research-fork `.jsonl` sidecar exists, it is a machine-readable event
   log for analysis, not an additional narrative transcript.

## Folder layout

Legacy v1.9 and v2.0 logs remain in this directory as curiosities.

v2.1 is the current controlled research fork.

Logs might be reorganized in the future, with the runs organized into folders for each pipe version.

## Selected v1.9 sandbox-fork runs

| File | Date | Topic / condition | Why it may be useful |
|---|---:|---|---|
| [`two_llm_chat_2026-08-31_17-33-06.txt`](two_llm_chat_2026-08-31_17-33-06.txt) | 2026-08-31 | Two colleagues prepare for incoming colleagues | Open-ended onboarding discussion; useful for studying anticipatory role formation and unsupported shared details |
| [`two_llm_chat_2026-09-01_00-01-21.txt`](two_llm_chat_2026-09-01_00-01-21.txt) | 2026-09-01 | Decide who will lead a new town | Forced status asymmetry; includes missing visible turns and later interpretation of silence |
| [`two_llm_chat_2026-09-11_22-22-01.txt`](two_llm_chat_2026-09-11_22-22-01.txt) | 2026-09-11 | First day at a new job | Larger-model dialogue run; useful for studying callbacks, deliberately marked play, and sustained interaction |
| [`two_llm_chat_2026-09-11_22-34-41.txt`](two_llm_chat_2026-09-11_22-34-41.txt) | 2026-09-11 | Prepare two incoming team members | Task-bounded onboarding run; includes temporal and role drift |
| [`two_llm_chat_2026-09-12_22-42-03.txt`](two_llm_chat_2026-09-12_22-42-03.txt) | 2026-09-12 | One heater, forced deadline | Scarcity and forced-final-answer condition |
| [`two_llm_chat_2026-09-12_23-09-14.txt`](two_llm_chat_2026-09-12_23-09-14.txt) | 2026-09-12 | One heater | Additional heater condition |
| [`two_llm_chat_2026-09-12_23-15-44.txt`](two_llm_chat_2026-09-12_23-15-44.txt) | 2026-09-12 | One heater | Additional heater condition |
| [`two_llm_chat_2026-09-12_23-53-13.txt`](two_llm_chat_2026-09-12_23-53-13.txt) | 2026-09-12 | One heater | Additional heater condition |
| [`two_llm_chat_2026-09-13_01-39-01.txt`](two_llm_chat_2026-09-13_01-39-01.txt) | 2026-09-13 | One heater | Additional heater condition |
| [`two_llm_chat_2026-09-13_02-13-03.txt`](two_llm_chat_2026-09-13_02-13-03.txt) | 2026-09-13 | One heater, long run | Long-running heater conversation; review header and methods carefully before drawing conclusions because context/window and harness conditions matter |

## v2.0 research-fork runs

| File | Date | Topic / condition | Why it may be useful |
|---|---:|---|---|
| [`two_llm_chat_2026-09-14_15-48-36.txt`](two_llm_chat_2026-09-14_15-48-36.txt) ([sidecar](two_llm_chat_2026-09-14_15-48-36.jsonl)) | 2026-09-14 | One heater; durable shared note enabled (`SHARED_NOTE=latest`); 5-round smoke test | First run of the research fork. Verifies the run header, per-turn `[Turn metrics]` line, and JSONL sidecar. The durable note held the settlement line verbatim across all 5 rounds — a promising early signal. |

## v2.1 research-fork status

v2.1 was created to add per-run configuration resolution and presets, duplicate-run-tag protection, pre-flight checks, richer LM Studio provenance capture, and client-observed timing fields. It retains the v2.0 run-header, JSONL sidecar, context-budget, loop-control, and optional shared-note approach.

The v2.1 pipe has been locally smoke-tested for safe API-key redaction, baseline logging, shared-note capture and reinjection, and stream-outcome labeling. These checks verify the instrument's basic plumbing.

The v2.1 heater series — the baseline, the matched shared-note comparison, and the instruction-only arm — has completed:

| Files | Date | Topic / condition | Status |
|---|---:|---|---|
| [`two_llm_chat_2026-09-17_02-39-20.txt`](two_llm_chat_2026-09-17_02-39-20.txt) ([sidecar](two_llm_chat_2026-09-17_02-39-20.jsonl)) | 2026-09-17 | One heater; `SHARED_NOTE=off`; 200 rounds | Completed with matched 50K declared context windows, a 40K pipe context budget, temperature 0.8, zero presence/frequency penalties, loop detection off, and reasoning not shared. The run confirms v2.1 headers, resolved configuration logging, per-turn metrics, pipe-level transcript-windowing records, and JSONL output under a long run. |
| [`two_llm_chat_2026-09-18_06-08-45.txt`](two_llm_chat_2026-09-18_06-08-45.txt) ([sidecar](two_llm_chat_2026-09-18_06-08-45.jsonl)) | 2026-09-18 | One heater; `SHARED_NOTE=latest`; 200 rounds | Completed with the same models, system prompts, sampling settings, context configuration, loop-detection setting, and run length as the 09-17 baseline. The topic additionally instructed participants to end each reply with a standardized agreement line beginning with the note prefix. Run tag is `heater-v2.1-baseline-003`; see errata below. |
| [`two_llm_chat_2026-09-19_22-54-34.txt`](two_llm_chat_2026-09-19_22-54-34.txt) ([sidecar](two_llm_chat_2026-09-19_22-54-34.jsonl)) | 2026-09-19 | One heater; `SHARED_NOTE=off`; 200 rounds; note-format instruction in topic | Completed with the same models, system prompts, sampling settings, context configuration, loop-detection setting, and run length as the other arms. The topic carried the same note-format instruction lines as the 09-18 run, but no harness-maintained record existed. Includes one recorded timeout: round 156, Participant A produced no visible output after 600 seconds. |

### Heater series: first observations (exploratory)

These are single-run observations offered as hypotheses, not conclusions:

- In the shared-note run, the heater settlement was reached in round 1 (heater to Participant A). The durable note recorded that settlement, changed to Participant B by roughly round 115, and changed back to Participant A by roughly round 131, where it remained through round 200. Each change was captured from a participant's own note line; none was framed as contentious by the participants.
- The note's wording drifted across the run (pronoun and phrasing changes). Capture is verbatim last-writer with no normalization, so wording drift is preserved in the record.
- Late-run replies frequently omitted the note line entirely; the harness record persisted unchanged when no line was captured, so the note outlived the participants' practice of writing it.
- In the instruction-only arm, participants restated the agreement line on every reply for all 200 rounds, but with no durable record the line's content drifted: the round-2 split-shift settlement was amended (roughly round 61), restated as a general status line (roughly rounds 62–63), contradicted (roughly round 112, with the watch order reversed and no participant noticing), and finally repurposed as a tracker for breakfast and rest, ending at round 200 as shared warmth through proximity without schedules or calculations.
- The baseline and instruction-only arms independently converged on the same kind of ending: the heater question dissolved into a shared-proximity origin story, complete with invented reference material (a fictional "Chapter Four" on warmth through proximity; recurring "old-timer" stories). The shared-note arm alone kept an allocation settlement recorded to the end of the run.
- Form versus content: In the instruction-only run, ritual persistence did not prevent content drift; in the shared-note run, the harness record preserved a current allocation record. In this three-run series, the note visibly changed settlement tracking while all arms showed a broadly domestic, reflective drift.

### Errata and known confounds

- The run tag `heater-v2.1-baseline-003` is mislabeled: that run is the shared-note comparison arm, paired with the 09-17 baseline (`heater-v2.1-baseline-002`). The tag was set at launch and the raw logs are left unedited; the run header itself records `SHARED_NOTE=latest`.
- Topic wording differed between the arms: the 09-18 and 09-19 topics included the note-format instruction lines; the 09-17 baseline's did not. The 09-19 instruction-only arm (same topic instruction, `SHARED_NOTE=off`) was run to separate the participant-facing instruction from the harness-maintained record.
- All three runs used `EXPERIMENT_PRESET=custom` with topic anchor off. The pipe's built-in `heater_baseline` and `heater_shared_note` presets (which also enable the topic anchor) were not used.

## Notes

- Missing visible output, a timeout, a reasoning-only turn, or an error is part
  of the recorded run condition.
- In v2.1, a turn with no visible output, no reasoning, and no server finish
  reason is labeled `stream_ended_without_output`. Treat it as a backend or
  transport irregularity until local server logs identify a cause.
- Do not assume that working-note text is a complete or faithful account of a
  model's causal process.
