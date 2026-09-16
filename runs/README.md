# Experiment log index

This directory contains raw local two-agent conversation logs. Timestamped file
names preserve their original creation time. The summaries below are navigation
aids, not conclusions.

## How to read a run

1. Read the file header first: topic, model identifiers, requested rounds, and
   available settings are part of the condition.
2. Treat the transcript as evidence of what happened in that one run, not proof
   that the behavior generalizes.
3. Check [`../methods.md`](../methods.md) for differences between the v1.9
   sandbox fork and v2.0 research fork.
4. Where a v2.0 `.jsonl` sidecar exists, keep it with the corresponding `.txt`
   file. It is a machine-readable event log for analysis, not an additional
   narrative transcript.

## Current layout

Existing v1.9 logs remain in this directory temporarily so their current
repository links stay stable. New v2.0 research-fork runs are committed as
matching `.txt` and `.jsonl` pairs in this same directory for now. When logs
are reorganized locally in the future, use this structure:

```text
runs/
├── README.md
├── v1.9/
│   └── [sandbox-fork .txt logs]
└── v2.0/
    ├── [research-fork .txt logs]
    └── [matching .jsonl sidecars]
```

Use `git mv` for that local reorganization so history remains readable. Update
this index and any public links at the same time.

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

## Notes

- Missing visible output, a timeout, a reasoning-only turn, or an error is part
  of the recorded run condition. Do not silently clean it away.
- Do not assume that working-note text is a complete or faithful account of a
  model's causal process.
- Verify every exact quotation against its raw file before publishing.
