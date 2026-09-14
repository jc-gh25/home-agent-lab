# v1.9 sandbox fork

The v1.9 sandbox fork is the original minimal-prompt version of the autonomous
two-agent pipe. It was designed primarily for open-ended creative exploration
and observation of model-to-model interaction.

## Source

The current source file remains at the repository root for link stability:

- [`../../llm_autonomous_chat.py`](../../llm_autonomous_chat.py)

## Use it for

- Open-ended conversations
- Creative brainstorming
- Prompt-framing explorations
- Qualitative observation where the harness itself is part of the situation

## Important methodological note

Do not treat v1.9 and v2.0 runs as interchangeable. v1.9 inherited settings
and behaviors that can influence repetition, context handling, and visible
output. Some older logs do not contain full per-turn configuration metadata or
all harness interventions.

For a reproducible experiment or a public field report, prefer the v2.0
research fork and record server-side context-window / overflow-policy settings
alongside the log.

See [`../../methods.md`](../../methods.md) for the full methodology and limits.
