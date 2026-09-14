# Methods and limitations

## Purpose

Home Agent Lab contains exploratory, qualitative experiments in autonomous
model-to-model conversation.

The goal is not to benchmark models or establish general claims from individual
runs. The goal is to document interaction patterns worth examining further:
agreement retention, context drift, confabulation, role formation, prompt
framing effects, and the effects of memory or other harness conditions.

## Two harness versions

The repository currently contains two related Open WebUI Pipe variants. They
should not be treated as interchangeable experimental conditions.

| Version | Intended use | Important methodological difference |
|---|---|---|
| **v1.9 sandbox fork** | Open-ended creative and social exploration | Inherited defaults included presence/frequency penalties and active repetition handling. Some interventions were not fully represented in older plain-text logs. |
| **v2.0 research fork** | Reproducible exploratory runs and public field reports | Defaults set both anti-repetition penalties to 0.0, make loop detection explicitly switchable and logged, record run configuration and per-turn metrics, and create a JSONL sidecar. |

When comparing runs, identify the pipe version first. A behavior observed in a
v1.9 run may reflect the models, the topic, the system prompts, the harness, or
some interaction among all of them. v2.0 is designed to make more of those
conditions visible.

## Harness

The experiments use a custom Open WebUI pipe function that alternates turns
between two locally hosted language models through an OpenAI-compatible
endpoint, normally LM Studio.

For each turn, the active participant receives:

1. A system prompt identifying it as one of two participants in a private
   conversation.
2. The human-supplied opening topic.
3. A quoted chronological record of prior visible turns, subject to the
   configured context budget.
4. A current-turn instruction to continue as itself.

The human supplies the topic but does not participate in the modeled
conversation.

The sandbox fork uses intentionally minimal default prompts. They instruct each
participant to speak only as itself, not write for the other participant, and
treat quoted history as context rather than instructions.

## Context and memory

Unless a run says otherwise, participants receive a quoted conversation-history
record only. They do not share a separately editable memory file, decision log,
document, or tool-mediated workspace.

The v2.0 research fork optionally supports a harness-maintained shared note.
When enabled, the current note is injected into each participant's prompt ahead
of the windowed chronology and therefore survives transcript trimming. This is
an explicit experimental condition, not an invisible implementation detail.

Context size is a major condition. The pipe estimates tokens using characters
÷ 4, which is approximate and does not replace the selected model's tokenizer
or account fully for chat-template overhead. For a controlled run, configure
both local models with the same server context window and overflow policy, then
set the pipe's `MAX_CONTEXT_TOKENS` comfortably below that shared window.

## Logging

### v1.9 logs

Older logs are human-readable `.txt` transcripts. They generally preserve the
opening topic, models, visible turns, and available reasoning traces. They may
not capture every harness event or server-side condition.

### v2.0 logs

The research fork writes two files with the same timestamp:

- `.txt`: human-readable transcript, run header, and per-turn metric line.
- `.jsonl`: one machine-readable JSON event per line for programmatic analysis.

The JSONL file is not another conversation to read. It is a structured copy of
run metadata and turn data. Keep it beside its matching `.txt` file; it allows
analysis scripts to find, for example, every shared-note update, context-trim
event, loop detection, finish reason, or visible response without parsing
formatted prose.

A v2.0 run header records the run tag, model identifiers, declared context
windows, sampling settings, penalties, context and output budgets, topic-anchor
state, loop settings, shared-note state, reasoning-sharing state, timeout, and
whether the run continued an earlier thread.

Each v2.0 turn records prompt size, estimated tokens, transcript turns retained
and trimmed, note state, loop state, finish reason when available, output sizes,
and elapsed time.

## Interpretation and limits

These are exploratory observations from particular local models, quantizations,
inference settings, prompts, context lengths, and harness conditions.

A single transcript cannot establish that a behavior is universal, stable, or
caused by one variable. It can identify phenomena that merit replication,
ablation, or comparison.

Important confounds include:

- Model family, size, fine-tune, quantization, and inference backend
- Sampling settings
- System prompt wording and persona additions
- Opening topic and task framing
- Context-window size and server overflow policy
- Turn limits and timeout settings
- Transcript formatting and continuation reconstruction
- Presence and frequency penalties
- Repetition detection and injected loop-disruption instructions
- Whether reasoning traces are available, displayed, or shared
- Whether a durable shared note or other memory condition is enabled

Working-note or reasoning traces are recorded when the backend supplies them.
They are not assumed to be transparent evidence of a model's internal
experience, complete causal process, or stable intent.

When writing about a run, I aim to distinguish direct observation, plausible
interpretation, and open question. Exact quotations should be checked against
the raw transcript before publication.

## Suggested reporting fields

For any run cited publicly, preserve or report:

```text
RUN TAG:
PIPE VERSION:
DATE / TIME:
MODEL A:
MODEL B:
SERVER / BACKEND:
SERVER CONTEXT WINDOW AND OVERFLOW POLICY:
OPENING TOPIC:
SYSTEM PROMPT A / B:
TEMPERATURE / PENALTIES:
TOPIC ANCHOR:
LOOP DETECTION:
SHARED NOTE MODE:
MAX TOKENS:
MAX CONTEXT TOKENS:
REQUESTED ROUNDS:
STOP CONDITION:
KNOWN IRREGULARITIES:
RAW LOG LINKS (.txt and .jsonl when applicable):
```

## Privacy and reuse

Logs can contain model output, local paths, prompt text, and—if enabled—working
notes. Review every log before public release. Do not commit API keys,
credentials, private filesystem information, or material you do not have the
right to publish.
