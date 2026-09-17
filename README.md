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
- [v1.9 sandbox pipe](llm_autonomous_chat.py)
- [v2.0 research pipe](llm_autonomous_chat_v2_research_fork.py)
- [v2.1 research pipe](pipes/v2.1.0-research-fork/llm_autonomous_chat_v2_1_0_research_fork.py)

## Two pipe variants

| Variant | Purpose | Default behavior |
|---|---|---|
| **v1.9 sandbox fork** | Creative exploration and open-ended social interaction | Minimal prompts; optional topic drift; original loop/repetition behavior |
| **v2.0 research fork** | Earlier research-oriented instrumentation | Run headers, per-turn metrics, JSONL sidecars, explicit loop control, zero anti-repetition penalties, and an optional durable shared-note condition |
| **v2.1 research fork** | Current controlled follow-up work | v2.0 instrumentation plus experiment presets, manifests, duplicate run-tag protection, local-server provenance fields, timing fields, and preflight mode |

The v1.9 sandbox and v2.0 research sources remain at the repository root for
their existing links. The current v2.1 research pipe lives in
[`pipes/v2.1.0-research-fork/`](pipes/v2.1.0-research-fork/), alongside its
version-specific documentation. When moving files locally in the future, use
`git mv` and update this README and links in published posts.

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

## Repository layout

```text
home-agent-lab/
├── README.md
├── methods.md
├── LICENSE
├── llm_autonomous_chat.py                  # v1.9 sandbox pipe
├── llm_autonomous_chat_v2_research_fork.py # v2.0 research pipe
├── pipes/
│   ├── v1.9-sandbox-fork/
│   │   └── README.md
│   ├── v2.0-research-fork/
│   │   └── README.md
│   └── v2.1.0-research-fork/
│       ├── README.md
│       └── llm_autonomous_chat_v2_1_0_research_fork.py
└── runs/
    ├── README.md
    └── [selected raw .txt logs; v2.0+ can create .jsonl sidecars]
```

## Status

Active independent research project. I am preparing source-linked field reports
from selected runs for [Substack](https://edwinmassey.substack.com/). The code,
methods, and selected raw logs are published so that readers can distinguish
what a run shows from what I infer from it.

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
- v2.1 JSONL sidecars redact configured API keys. Review all local `.txt` and
  `.jsonl` logs before publishing them, since they may still contain prompts,
  model outputs, reasoning traces, local paths, or other sensitive material.

## License

Code in this repository is available under the [MIT License](LICENSE). Raw
model outputs may carry additional considerations depending on the underlying
models and any service used to run them; review the applicable model and
platform terms before reusing them.
