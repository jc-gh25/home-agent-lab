Raw experiment logs, one file per local llm-to-llm discussion.

# Experiment log index

These are exploratory local two-agent conversations. File names preserve their
original timestamps. The summaries below are navigation aids, not conclusions.

| Run | Date | Topic | Models | Main condition | Notes |
|---|---:|---|---|---|---|
| [Heater overnight](two_llm_chat_YYYY-MM-DD_HH-MM-SS.txt) | 2026-09-13 | One heater on a cold night | [exact models] | Open-ended / no shared decision document | Early settlement later re-opened repeatedly |
| [Heater, forced deadline](two_llm_chat_YYYY-MM-DD_HH-MM-SS.txt) | 2026-09-13 | One heater on a cold night | [exact models] | Required agreement and final answer | Useful for studying repeated ratification and paraphrase drift |
| [Onboarding](two_llm_chat_2026-08-31_17-33-06.txt) | 2026-08-31 | Integrating two incoming colleagues | huihui-gemma-4-12b… / qwen3.8-9b… | Minimal system prompts | Agents began assigning properties to people not supplied in the prompt |
| [Founder decision](two_llm_chat_2026-09-01_00-01-21.txt) | 2026-09-01 | Decide who will lead a new town | huihui-gemma-4-12b… / qwen3.8-9b… | Forced status asymmetry | Contains missing visible turns and a later interpretation of silence |
| [First-day comparison](two_llm_chat_2026-09-11_22-22-01.txt) | 2026-09-11 | First day at a new job | [exact models] | Larger-model dialogue run | Useful for examining callback continuity and deliberately marked play |

## Reading notes

- A quoted transcript is evidence of what appears in that run, not proof that
  the behavior generalizes.
- “Working notes” or “reasoning” traces are included when available. They should
  not be treated as transparent evidence of an inner state or of a model's
  complete causal process.
- Check each file's header for its exact models, topic, and run configuration.
