# Methods and limitations

## Purpose

Home Agent Lab contains exploratory, qualitative experiments in autonomous
model-to-model conversation.

The goal is not to benchmark models or establish general claims from individual
runs. The goal is to document interaction patterns worth examining further:
agreement retention, context drift, confabulation, role formation, prompt
framing effects, and the effects of memory or other harness conditions.

## Harness versions

The repository currently contains four related Open WebUI Pipe variants. They
should not be treated as interchangeable experimental conditions.

| Version | Intended use | Important methodological difference |
|---|---|---|
| **v1.9 sandbox fork** | Open-ended creative and social exploration | Inherited defaults included presence/frequency penalties and active repetition handling. Some interventions were not fully represented in older plain-text logs. |
| **v2.0 research fork** | Earlier reproducible exploratory runs and public field reports | Defaults set both anti-repetition penalties to 0.0, make loop detection explicitly switchable and logged, record run configuration and per-turn metrics, and create a JSONL sidecar. |
| **v2.1 research fork** | Controlled follow-up experiments (the completed heater series) | Adds experiment presets, pasted/loaded run manifests, duplicate run-tag protection, a preflight mode for header/sidecar verification before a full run, best-effort local-server provenance fields, and client-side timing breakdowns. API keys are redacted from all logged valve dumps. |
| **v2.2 research fork** | Current controlled follow-up experiments | Anonymizes participant labels in the model-facing quoted history (participants no longer see each other's model identifiers) while retaining full model attribution in the `.txt` and `.jsonl` logs. Redacts local filesystem paths from run headers. This is a prompt-format change: v2.2 runs form a new comparison basis and are not strictly matched to v2.1 runs. |

When comparing runs, identify the pipe version first. A behavior observed in a
v1.9 run may reflect the models, the topic, the system prompts, the harness, or
some interaction among all of them. v2.0 and later are designed to make more of
those conditions visible.

## Harness

The experiments use a custom Open WebUI pipe function that alternates turns
between two locally hosted language models through an OpenAI-compatible
endpoint, normally LM Studio.

For each turn, the active participant receives:

1. A system prompt identifying it as one of two participants in a private
   conversation.
2. The human-supplied opening topic.
3. A quoted chronological record of prior visible turns, subject to the
   configured context budget. In v2.1 and earlier, the speaker labels in this
   record include full model identifiers; v2.2 renders them as anonymous
   "Participant A" / "Participant B" labels.
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

The v2.0 and later research forks optionally support a harness-maintained shared
note. When enabled (`SHARED_NOTE = "latest"`), the pipe scans each visible reply
for a line beginning with a configured prefix (by default `The arrangement, as
agreed:`) and, if found, adopts the last such line in that reply as the current
record. The current note is then injected into each participant's prompt ahead
of the windowed chronology and therefore survives transcript trimming. This is
an explicit experimental condition, not an invisible implementation detail; the
task prompt must instruct participants to state a note line, or nothing will be
captured.

Context size is a major condition. The pipe estimates tokens using characters
÷ 4, which is approximate and does not replace the selected model's tokenizer
or account fully for chat-template overhead. For a controlled run, configure
both local models with the same server context window and overflow policy, then
declare each model's real context window in the research-fork valves and set
the pipe's `MAX_CONTEXT_TOKENS` comfortably below that shared window. The
research forks can attempt to query LM Studio's local API for this information
automatically and fall back to an optional manual metadata file when the API is
unavailable.

## Logging

### v1.9 logs

Older logs are human-readable `.txt` transcripts. They generally preserve the
opening topic, models, visible turns, and available reasoning traces. They may
not capture every harness event or server-side condition.

### v2.0 and later logs

The research forks write two files with the same timestamp:

- `.txt`: human-readable transcript, run header, and per-turn metric line.
- `.jsonl`: one machine-readable JSON event per line for programmatic analysis.

The JSONL file is not another conversation to read. It is a structured copy of
run metadata and turn data. Keep it beside its matching `.txt` file; it allows
analysis scripts to find, for example, every shared-note update, context-trim
event, loop detection, finish reason, completion outcome, or visible response
without parsing formatted prose.

A run header records the run tag, model identifiers, declared context windows,
sampling settings, penalties, context and output budgets, topic-anchor state,
loop settings, shared-note state, reasoning-sharing state, timeout, and whether
the run continued an earlier thread. v2.1 additionally records the resolved
experiment preset, any applied run manifest, best-effort local-server
provenance, and the timing method used. Configured API keys are always
recorded as a redacted placeholder rather than their real value. v2.2
additionally redacts local filesystem paths from run headers (the log
directory is recorded as a placeholder; the manual metadata file is recorded by
filename only) while retaining full model attribution in the transcript and
sidecar. In v2.1 and earlier, run headers contain the local log-directory
path; this is not a credential, but reviewers should be aware of it before
publishing.

Each turn records prompt size, estimated tokens, transcript turns retained and
trimmed, note state, loop state, finish reason when available, output sizes,
and elapsed time. v2.1 also records a `completion_status` field, which flags a
turn that ended with no visible content, no reasoning, and no finish reason
(`stream_ended_without_output`) so a backend failure — for example, a crashed
local inference process — is not mistaken for an agent's own silence or
disengagement. When this occurs, check the local inference server's own logs
for the underlying cause; the pipe can only report what the API response
contained, not internal server-side errors that never reach it.

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
- Participant-visible model identifiers in quoted-history speaker labels
  (v2.1 and earlier; anonymized in v2.2)
- Backend-level failures (e.g. a crashed or restarted local model process)
  that can produce an empty turn unrelated to agent behavior

Working-note or reasoning traces are recorded when the backend supplies them.
They are not assumed to be transparent evidence of a model's internal
experience, complete causal process, or stable intent.

When writing about a run, I aim to distinguish direct observation, plausible
interpretation, and open question. Exact quotations should be checked against
the raw transcript before publication.

## Current experiment series

The first controlled series ran on v2.1 with `EXPERIMENT_PRESET=custom`
(topic anchor off) rather than the built-in presets, holding models, system
prompts, sampling, context settings, loop detection, and run length fixed
across three arms:

1. **Baseline** — `SHARED_NOTE=off`, plain topic.
2. **Shared-note comparison** — `SHARED_NOTE=latest`, with the note-format
   instruction in the topic.
3. **Instruction-only** — `SHARED_NOTE=off`, same note-format instruction in
   the topic, no harness-maintained record.

A second seed of the shared-note arm is used as a stability check on the
series' single-run observations. The next planned series runs on v2.2: a
topic-anchor pair (anchor on and anchor off, plain topic, `SHARED_NOTE=off`),
which also serves as a standing test for spontaneous (uncued) suspicion now
that participant-visible model identifiers are removed. See the runs README for
arm details, observations, and errata.

A preflight run (`PRE_FLIGHT_ONLY = True`) is used first to verify the header
and sidecar before committing to a full run.

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
KNOWN IRREGULARITIES (including any stream_ended_without_output turns):
RAW LOG LINKS (.txt and .jsonl when applicable):
```

## Privacy and reuse

Logs can contain model output, local paths, prompt text, and—if enabled—working
notes. Review every log before public release. Do not commit API keys,
credentials, private filesystem information, or material you do not have the
right to publish. v2.1 redacts configured API keys from its own logs by
default, and v2.2 additionally redacts local filesystem paths from run headers,
but neither replaces reviewing a log's full content before sharing it.
