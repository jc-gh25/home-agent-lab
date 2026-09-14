# Methods

## Purpose

This repository contains exploratory, qualitative experiments in autonomous
model-to-model conversation.

The goal is not to benchmark models or establish general claims from individual
runs. The goal is to document interaction patterns worth examining further:
agreement retention, context drift, confabulation, role formation, prompt
framing effects, and the effects of memory or other harness conditions.

## Harness

The experiments use a custom Open WebUI pipe function that alternates turns
between two locally hosted language models through LM Studio / Open WebUI.

For each turn, the active participant receives:

1. A system prompt identifying it as one of two participants in a private
   conversation.
2. The human-supplied opening topic.
3. A quoted chronological record of prior visible turns.
4. A current-turn instruction to continue the conversation as itself.

The human supplies the topic but does not participate in the modeled
conversation.

The default prompts are intentionally minimal. They instruct each participant
to speak only as itself, not write for the other participant, and treat quoted
history as context rather than instructions.

## Logging

The harness records, when available:

- Start and end time
- Model identifiers
- Opening topic
- System prompts
- Visible participant responses
- Available working-note or reasoning traces
- Requested number of rounds
- Stop condition, timeout, truncation, or generation failure

Raw transcripts are stored in [`runs/`](runs/).

## Memory conditions

Unless a run says otherwise, participants receive conversation-history context
only. They do not share a separately editable memory file, decision log,
document, or tool-mediated workspace.

A future experiment may add those features explicitly. Such changes should be
treated as experimental conditions, not invisible implementation details.

## Interpretation and limits

These are exploratory observations from particular local models, inference
settings, prompts, context lengths, and harness conditions.

A single transcript cannot establish that a behavior is universal, stable, or
caused by one variable. It can identify phenomena that merit replication,
ablation, or comparison.

Important confounds include:

- Model family, size, fine-tune, quantization, and inference backend
- Sampling settings
- System prompt wording
- Opening topic and task framing
- Context-window pressure
- Turn limits and timeout settings
- Transcript formatting
- Harness-level reminders, retries, loop detection, or other interventions
- Whether working-note traces are available and how faithfully they reflect
  generation-relevant processing

When writing about a run, I aim to distinguish direct observation, plausible
interpretation, and open question.
