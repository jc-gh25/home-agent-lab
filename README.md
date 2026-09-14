# LLM Autonomous Chat

A small, locally run harness for exploratory two-agent LLM conversations.

Two models alternate turns under configurable prompts and conversation-history
conditions. The harness records full transcripts, including available
working-note traces, to support qualitative study of long-horizon interaction:
agreement retention, context drift, confabulation, social role formation, and
the effects of task framing.

This is not a benchmark suite and the included runs are not statistical
evidence. They are inspectable case studies intended to generate testable
questions.

## Start here

- [Methods and limitations](METHODS.md)
- [Experiment log index](runs/README.md)
- [Raw experiment logs](runs/)
- [Pipe implementation](llm_autonomous_chat.py)

## Example questions

- What happens when two agents reach an agreement but lack a shared external
  record of it?
- How does an imposed deadline change a cooperative decision?
- Does task and role framing change whether agents generate procedures,
  artifacts, or social interaction?
- How do models differ when context continuity degrades?

## Status

Active personal research project. Logs and methods are published as they are
reviewed and prepared for public discussion.

## Why

Long-horizon, open-ended agent interaction is where the interesting behavior lives: consensus substituting for evidence, process replacing purpose, memory compressing into false certainty, agreements quietly dissolving overnight. Most published examples come from large, instrumented environments. This lab asks what happens with two small models and almost no scaffolding.

## Findings so far

- **Settlement decay.** In a 200-round overnight run, a cleanly negotiated agreement silently decayed — the agents re-litigated the same settled decision dozens of times under fresh framings, because nothing in the system held settlements still.
- **Framing-induced pathologies.** Under goal-and-role framing, agents produced process bloat, self-referential governance charters, and consensus-driven fabrications. The same models under a minimal prompt produced neither — suggesting the failure modes are induced by framing rather than emergent from multi-agent interaction itself.
- **Divergent decay.** Under context pressure, one agent confabulates fluent continuity (new schemes, new biographies on demand) while the other collapses into honest disorientation. Same situation, opposite failure styles.
- **Prefiguration.** Asked to prepare for future colleagues, agents invent them — names, temperaments, needs — and begin building the world around people who don't exist.

## The logs

`runs/` — full raw logs, one file per experiment, named by date and configuration. Logs are unedited.

## How it works

1. **Initial setup** — Extracts the original topic and any prior conversation state from the thread
2. **Round loop** — For each round:
   - Constructs role-safe messages for Participant A (system prompt + context + instruction)
   - Streams A's response, captures output + optional reasoning
   - Detects if A is repeating; flags for loop disruption if needed
   - Repeats for Participant B with A's fresh response in context
3. **State persistence** — Encodes turn metadata in Base64 markers within the chat
4. **Optional synthesis** — If configured, calls the moderator model with the full discussion
5. **Logging** — Writes all turns to a local text file for offline review

## Technical highlights

- **Streaming optimization** — Batches small text fragments to reduce Open WebUI display pressure while preserving live output
- **Context management** — Estimates token usage (char/4) and windows transcripts to fit within budget
- **Loop detection** — Compares recent same-participant replies via sequence similarity; triggers disruption at configurable threshold
- **Continuation encoding** — Stores round number, participant, model ID, content, and reasoning in machine-readable Base64 markers
- **Graceful degradation** — Falls back to legacy Markdown parsing if structured markers are missing
- **Tool-call safety** — Sends `tool_choice="none"` to prevent models from attempting tool use; retries without if endpoint rejects the schema

## Configuration (Valves)

- `PERSONA_A` / `PERSONA_B` — optional temperament injection (empty by default)
- `TOPIC_ANCHOR` — whether turns are reminded to stay on topic
- `SHOW_REASONING` — capture working notes/reasoning fields (default True)
- `MAX_ROUNDS`, `MAX_TOKENS`, `MAX_CONTEXT_TOKENS`, `LOG_DIRECTORY`, and related knobs

## Known limitations

- **Agreement ≠ truth** — Two models agreeing does not establish factual correctness. Use for exploration, not verification.
- **No auto-stopping** — Runs exactly `MAX_ROUNDS` unless interrupted. Does not detect resolution or semantic saturation.
- **Continuation is thread-scoped** — Works only while prior messages remain in the same Open WebUI thread.
- **Long threads slow down** — Context windowing limits what models see, but Open WebUI still renders the full saved thread.
- **Reasoning display is optional** — Captured reasoning/thinking fields may be verbose, speculative, or model-specific.

## Debugging

**Model produces no output:**
- Check `MAX_TOKENS` and `MAX_CONTEXT_TOKENS` — increase if too low
- Verify `MODEL_A` and `MODEL_B` identifiers exactly match your endpoint
- Check LM Studio logs for inference errors

**Continuation not working:**
- Ensure you're in the same Open WebUI thread
- If you edited the transcript, continuation may fail (legacy fallback should still work)
- Check the collapsed "Conversation continuation data" section for encoded state

**Logging issues:**
- Verify `LOG_DIRECTORY` exists and is writable by the Open WebUI backend process
- Check `SHOW_LOG_PATH` to see the actual log file location

## Related

The [AI Village](https://theaidigest.org/village) by AI Digest — the large-scale, instrumented version of this kind of experiment, and the inspiration for the comparative questions. Field reports from this local experiment will be posted at [edwinmassey.substack.com](https://edwinmassey.substack.com/).

## Versioning

**v1.9.0 (sandbox fork)**
- Minimal default system prompts (role-free, epistemic rules removed)
- New `PERSONA_A` / `PERSONA_B` Valves for optional temperament injection
- New `TOPIC_ANCHOR` Valve to control whether turns stay on topic
- `SHOW_REASONING` now defaults to True
- All prior features (streaming, continuation, loop detection, logging, moderator) unchanged

Earlier versions added structured continuation data, streaming batching, and multi-agent orchestration.

## License

MIT
