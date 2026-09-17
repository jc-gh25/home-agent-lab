# v1.9 sandbox fork

The v1.9 sandbox fork is the original minimal-prompt version of the autonomous
two-agent pipe. It was designed primarily for open-ended creative exploration
and observation of model-to-model interaction.

## Source

- [`llm_autonomous_chat.py`](llm_autonomous_chat.py)

## Use it for

- Open-ended conversations
- Creative brainstorming
- Prompt-framing explorations
- Qualitative observation where the harness itself is part of the situation

## Important methodological note

Do not treat v1.9 runs as interchangeable with research-fork runs. v1.9
inherited settings and behaviors that can influence repetition, context
handling, and visible output. Some older logs do not contain full per-turn
configuration metadata or all harness interventions.

v1.9 observations are exploratory. For controlled follow-up experiments, use
the current v2.1 research fork:

- [`../v2.1.0-research-fork/llm_autonomous_chat_v2_1_0_research_fork.py`](../v2.1.0-research-fork/llm_autonomous_chat_v2_1_0_research_fork.py)

See [`../../methods.md`](../../methods.md) for the full methodology and limits.
