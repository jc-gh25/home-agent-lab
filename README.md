# Home Agent Lab

A small, locally run lab for exploratory two-agent LLM conversations.

Two local models alternate turns under configurable prompts, memory conditions,
and harness settings. The project records full transcripts and, for the research
fork, machine-readable run metadata. Its purpose is qualitative exploration of
long-horizon coordination, agreement retention, context drift, confabulation,
social role formation, and prompt-framing effects.

This is not a benchmark suite. The included transcripts are inspectable case
studies that can generate hypotheses and follow-up experiments; they do not,
by themselves, establish general claims about language models or multi-agent
systems.

## Start here

- [Methods and limitations](methods.md)
- [Experiment-log index](runs/README.md)
- [Raw experiment logs](runs/)
- [Pipe versions and source](pipes/)

## Pipe variants

| Variant | Purpose | Default behavior |
|---|---|---|
| **v1.9 sandbox fork** | Creative exploration and open-ended social interaction | Minimal prompts; optional topic drift; original loop/repetition behavior |
| **v2.0 research fork** | Earlier research-oriented instrumentation | Run headers, per-turn metrics, JSONL sidecars, explicit loop control, zero anti-repetition penalties, and an optional durable shared-note condition |
| **v2.1 research fork** | Controlled follow-up work (the completed heater series) | v2.0 instrumentation plus experiment presets, manifests, duplicate run-tag protection, local-server provenance fields, timing fields, and preflight mode |
| **v2.2 research fork** | Current controlled follow-up work | v2.1 instrumentation plus anonymous participant labels in the model-facing quoted history (full model attribution retained in the logs), local-path redaction in run headers, and a prompt-format change that makes v2.2 runs a new comparison basis |
| **v2.3 research fork** | Pre-launch hardening for the v2.2 anchor-pair plan | v2.2 instrumentation plus startup model preflight, fail-fast abort on invalid visible turns, per-turn `turn_validity` / `generation_status` / `note_parse_status` fields, model-ID preflight diagnostics, and the v2.3.2 restoration of the dropped turn-event sidecar writes; the turn-instruction wording change makes v2.3 runs a new comparison basis |

## Example questions

- What happens when two agents reach an agreement but lack a durable shared
  external record of it?
- What changes when a repeated spoken settlement is replaced by a
  harness-maintained shared note?
- How does task and role framing change whether agents generate procedures,
  artifacts, or social interaction?
- How do agents differ when context continuity degrades?
- What changes when the harness actively disrupts repetition versus allowing
  the conversation to loop or conclude?
- When, if ever, do agents privately suspect their partner is not human — and
  what changes when the harness stops revealing identifying cues?

## Status

Active independent research project. I am preparing source-linked field reports
from selected runs for posts on [Substack](https://edwinmassey.substack.com/). 
The code, methods, and selected raw logs are published so that readers can 
distinguish what a run shows from what I infer from it.

## Notes for readers

- Check the header of each log for the original topic, model identifiers, and
  available configuration details.
- Exact quotations in public writing should always be verified against the raw
  transcript.
- Working-note/reasoning traces, when present, are not treated as transparent
  evidence of model experience or complete causal explanation.
- The harness is part of the experimental environment. Prompts, turn limits,
  context budgets, penalties, repetition detection, and memory conditions can
  all shape observed behavior.
- v2.1 JSONL sidecars redact configured API keys, and v2.2 additionally redacts
  local filesystem paths from run headers. Review all local `.txt` and
  `.jsonl` logs before publishing them, since they may still contain prompts,
  model outputs, reasoning traces, or other sensitive material.

## License

Code in this repository is available under the [MIT License](LICENSE). Raw
model outputs may carry additional considerations depending on the underlying
models and any service used to run them; review the applicable model and
platform terms before reusing them.
