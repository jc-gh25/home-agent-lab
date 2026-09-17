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

## Current layout

Legacy v1.9 and v2.0 logs remain in this directory as curiosities.

v2.1 is the current controlled research fork.

Logs might be reorganized in the future. A future layout could be:

```text
runs/
├── README.md
├── v1.9/
│   └── [sandbox-fork .txt logs]
├── v2.0/
│   ├── [research-fork .txt logs]
│   └── [matching .jsonl sidecars]
└── v2.1/
    ├── [reviewed, selected .txt logs]
    └── [reviewed, selected .jsonl sidecars]
```

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
| [`two_llm_chat_2026-09-14_15-48-36.txt`](two_llm_chat_2026-09-14_15-48-36.txt) ([sidecar](two_llm_chat_2026-09-14_15-48-36.jsonl)) | 2026-09-14 | One heater; durable shared note enabled (`SHARED_NOTE=latest`); 5-round smoke test | First run of the research fork. Verifies the run header, per-turn `[Turn metrics]` line, and JSONL sidecar. The durable note held the settlement line verbatim across all 5 rounds — a promising early signal, not a long-horizon result. A longer run under the same settings is the natural follow-up. |

## v2.1 research-fork status

The v2.1 pipe has been locally smoke-tested for safe API-key redaction, baseline logging, shared-note capture and
reinjection, and stream-outcome labeling. These checks verify the instrument's basic plumbing.

The first controlled v2.1 series will compare matched heater conditions:

- **Baseline:** `SHARED_NOTE=off`
- **Shared note:** `SHARED_NOTE=latest`

The same models, system prompts, topic wording, sampling settings, context
configuration, loop-detection setting, and run length should be used in both
conditions. The task must request a standardized agreement line beginning with
the configured note prefix, normally `The arrangement, as agreed:`. In the
shared-note condition, the harness retains the latest matching line and injects
it before the windowed quoted transcript on later turns.

## Notes

- Missing visible output, a timeout, a reasoning-only turn, or an error is part
  of the recorded run condition.
- In v2.1, a turn with no visible output, no reasoning, and no server finish
  reason is labeled `stream_ended_without_output`. Treat it as a backend or
  transport irregularity until local server logs identify a cause.
- Do not assume that working-note text is a complete or faithful account of a
  model's causal process.
