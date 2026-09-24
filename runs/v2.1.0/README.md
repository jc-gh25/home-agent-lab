# v2.1.0 runs

Pipe: `pipes/v2.1.0-research-fork/`. Heater series -- baseline, shared-note
comparison, and instruction-only arms, Sep 17-19. Full exploratory
observations and errata (including the baseline-003 tag mislabeling below)
are documented in `../README.md` under "v2.1 research-fork status"; this
folder holds the raw logs only.

| File | Run tag | Notes |
|---|---|---|
| `two_llm_chat_2026-09-17_02-39-20.*` | `heater-v2.1-baseline-002` | Baseline arm, `SHARED_NOTE=off`. |
| `two_llm_chat_2026-09-18_06-08-45.*` | `heater-v2.1-baseline-003` (mislabeled -- this is the shared-note comparison arm, `SHARED_NOTE=latest`; see errata in `../README.md`) | Paired with the 09-17 baseline. |
| `two_llm_chat_2026-09-19_22-54-34.*` | not independently verified in this README | Instruction-only arm, `SHARED_NOTE=off`, same note-format topic instruction as 09-18. Includes a round-156 timeout (600.05s) coinciding with heavy context trimming. |

This series is complete -- do not re-run the heater baseline.
