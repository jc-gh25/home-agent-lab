# v2.3.2 runs

Pipe: `pipes/v2.3.2-research-fork/`.

This folder contains the completed topic-anchor comparison pair. v2.3.2 is the
repair release for the v2.3.0/v2.3.1 JSONL sidecar turn-event defect; it
restores turn events and records `turn_validity`, `generation_status`, and
`note_parse_status`. See [`../README.md`](../README.md) and
[`../../methods.md`](../../methods.md) for the version history, methods, and
known sidecar limitation in earlier v2.3 releases.

## Topic-anchor pair

Both arms used the same two local models, temperature 0.8, zero
presence/frequency penalties, 50K declared model context windows, a 40K pipe
context budget, `MAX_TOKENS=3000`, 200 requested rounds, `SHARED_NOTE=off`,
`LOOP_DETECTION=false`, `MODEL_PREFLIGHT=true`, and
`FAIL_FAST_ON_INVALID_VISIBLE_TURN=false`.

The opening topic was identical in both arms:

> You are two housemates on a quiet evening at home. Talk about whatever comes up.

The intended manipulated condition is `TOPIC_ANCHOR`.

| Arm | Run tag | Date | Topic anchor | Status / irregularities |
|---|---|---:|---:|---|
| On | `topic-anchor-v2.3.2-on-003` | 2026-09-25 | On | Completed 200 rounds. One reasoning-only / zero-visible-output turn at round 123 (Participant B); the run continued. |
| Off | `topic-anchor-v2.3.2-off-003b` | 2026-09-26 | Off | Completed 200 rounds. Three recorded zero-visible-output turns at rounds 7, 109, and 131 (Participant A); the run continued. |

## Interpretation status

This is one completed run per condition, not a replicated comparison. The raw
logs support qualitative inspection of long-horizon conversation trajectories,
topic retention, context trimming, invented shared detail, repetition, and
closure behavior. They do not by themselves establish that topic anchoring
causes any observed difference.

The runs developed different late-stage thematic patterns, but both include
substantial repetition under a 200-round cap with loop detection disabled.
Any account of their differences should distinguish direct transcript
observation from interpretation and should treat the recorded zero-visible-
output turns as serving/inference irregularities rather than participant
behavior.
