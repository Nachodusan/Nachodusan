## Ignacio Duque Sandoval

**AI Trainer · LLM Evaluation & RAG · Native Mobile**<br>
Buenos Aires, Argentina — working remotely across Chile and Argentina

I build AI products end to end: the app, the backend, the retrieval layer, and the
evaluation that proves any of it actually works. Most of what I ship sits where an
LLM meets something that has to be *correct* — clinical records, official
coursebooks — so grounding, abstention and measurement matter more than fluency.

How I work: **I measure before I decide, and I check the instrument first.** In
`ia-rioplatense` I threw away three rounds of LoRA fine-tuning because the A/B
numbers said prompt + retrieval won. In `tutor-ia` an experiment measured the
model's ceiling on mathematics and the feature left the product. In half the
repos below, the first finding was that my own harness was wrong — those are
written up rather than quietly fixed, because a number nobody audited is not
a result.

---

### Featured work

#### [tutor-ia](https://github.com/Nachodusan/tutor-ia) — study tutor grounded in official coursebooks
Typed dual-corpus RAG: the student's textbook as the citable source of truth, the
teacher's guide as pedagogy anchored to the learning objective. Hybrid retrieval
(dense + lexical + RRF) with a reranker acting as an **abstention gate** — if it
isn't in the material, the tutor says so instead of inventing. Per-sentence
directional NLI verification and false-premise detection run in the live chat, not
in a notebook.

Own evaluation harness with a golden set anchored to real chunks: **recall@5 of
88%** over ~38,000 chunks across 35 subjects.

`TypeScript` `Next.js` `Turborepo` `PostgreSQL + pgvector` `BGE-M3` `mDeBERTa NLI` `Python`

#### [agent-evals](https://github.com/Nachodusan/agent-evals) — evaluate what an agent did, not just what it answered
Trajectory checks, repeated runs, and a verdict that refuses to round. A case
passes only if every attempt passed; anything in between is reported as flaky
rather than as a percentage. Zero dependencies in the core.

Pointed at my own sql-agent, which had a single-run harness reporting 6/8, it
found three things that one run cannot: **one of the passing cases was a coin
flip** (0/3, then 1/3), **the agent was reporting SQL it had never executed**
with the right answer attached, and **a quarter of the weaker model's
"failures" were crashes inside my agent**, not the model. Fixing that moved
six failures into the right column and left the pass rate untouched.

`Python` `agent evaluation` `trajectory` `flakiness` `pytest`

#### [vector-db-bench](https://github.com/Nachodusan/vector-db-bench) — latency at matched recall, not at whatever the defaults were
Comparing vector databases at default settings measures the defaults. Every
HNSW implementation trades accuracy for speed on a knob, so this sweeps it,
measures recall against exact brute force, and compares at fixed recall
targets. Qdrant, Chroma and pgvector over 41,896 real BGE-M3 embeddings.

**The gap widens with accuracy**: at recall 0.90 Qdrant and pgvector are within
40% of each other; at 0.99 it is 3.4×. One number per engine would have picked
one of those at random.

Chroma is excluded from the comparison with the reason stated — its
`hnsw:ef_search` is accepted, persisted, reported back, and inert: identical
results for all 500 queries across the whole sweep.

The README's longest section is **the five ways the benchmark was wrong before
it was right**, including a client bug of mine that was inflating pgvector's
latency 2.4×, and a curve where latency fell as recall rose — which HNSW
cannot do, and which is now an automatic guard.

`Python` `HNSW` `pgvector` `Qdrant` `Chroma` `benchmarking`

#### [sql-agent](https://github.com/Nachodusan/sql-agent) — an agent loop, and an eval that catches what looks right
Natural-language questions answered by querying a database: LangGraph loop,
tool calling, every step traced with latency, tokens and cost. The sample
schema is seeded with traps so guessing is punished.

**6/8 on the golden set; both failures are valid SQL returning confidently
wrong numbers** — a join fan-out that silently over-counts refunds, and a
missing `GROUP BY`. Grading compares result sets against hand-written reference
queries, because the prose reads perfectly in both cases. The first version of
the harness reported 3/8 and was itself wrong, so the grader has its own tests.

Measured recovery rate across two models: 0%. Reported as found rather than
engineered away.

`Python` `LangGraph` `tool calling` `SQLite` `evals` `pytest`

#### [tinyllm-ios](https://github.com/Nachodusan/tinyllm-ios) — the tokens/sec figure a phone can actually hold
MLX Swift running Qwen2.5-0.5B-4bit on an iPhone 16. Every on-device benchmark
publishes one throughput number, and it is almost always the first generation on
a cool device: **108.85 tok/s burst, 66.32 tok/s sustained** over 150 runs back
to back. Both correct; only one describes using the thing.

**`ProcessInfo.thermalState` is a lagging indicator** — by the time iOS first
reported pressure above `nominal`, a quarter of the throughput was already gone.
An app that only degrades on a state change has shipped that quarter to the user.

Declining throughput has a second explanation that looks identical from inside
one batch — accumulated state in the process — so the suite idles three minutes
and runs the identical batch again. It recovered **99%** of the burst figure with
memory flat across all 300 generations, which is what makes "thermal" a finding
instead of a guess.

The first measurement taken here was 12 runs and reported `retained 86%,
throttled: never`. Arithmetically correct, wrong by 39%. That report is kept in
`evidence/`, and `BenchmarkReport` now carries `reachedEquilibrium` and
`warnings` so it says so itself.

`Swift` `SwiftUI` `MLX` `on-device inference` `iOS` `benchmarking`

#### [bitacora-app](https://github.com/Nachodusan/bitacora-app) — voice journal for people in therapy
Native iOS and Android apps with on-device transcription — the audio never leaves
the phone. Long-term memory with pgvector: each day's summary is embedded,
retrieved and reranked on every turn. The psychologist receives a monthly report
where **every data point is backed by a literal quote** from the patient, who
never sees the report, to avoid pushing them toward self-diagnosis.

Risk detection on both client and server, offline-first sync queue that doesn't
duplicate, and a monthly AI spend ceiling with per-call cost logged.
**268 automated tests** (151 backend, 96 iOS, 21 Android) plus a simulated month
of real usage.

`Swift / SwiftUI` `Kotlin / Jetpack Compose` `Node.js + Fastify` `PostgreSQL + pgvector` `TypeScript`

#### [ia-rioplatense](https://github.com/Nachodusan/ia-rioplatense) — local voice assistant with a Buenos Aires persona
Llama 3.1 8B via MLX + BM25 retrieval over 319 curated examples + Piper TTS,
running entirely on Apple Silicon with no cloud dependency. A 150+ rule lexical
post-processor guarantees *voseo* and regional vocabulary. Ships the A/B evidence
for dropping LoRA fine-tuning in favour of prompt engineering + retrieval on small
stylistic datasets.

`Python` `MLX` `Llama 3.1 8B` `BM25` `Piper TTS` `FastAPI` `React`

---

### What I work with

**LLM applications** — RAG, structured outputs, prompt engineering, guardrails and abstention
**Evaluation** — golden sets, retrieval metrics, hallucination measurement, documented negative results
**Mobile** — Swift (SwiftUI, SwiftData), Kotlin (Jetpack Compose), Flutter
**Backend** — Node.js, TypeScript, Fastify, Python (FastAPI), PostgreSQL, pgvector
**Infrastructure** — Linux, systemd, Tailscale, Docker, per-client isolated instances

---

### Elsewhere

**Portfolio** → [dusan.codes](https://dusan.codes) · **LinkedIn** → [in/ignaciodusan](https://www.linkedin.com/in/ignaciodusan/) · **Email** → nachodusan@gmail.com

*Open to AI engineering roles — remote, Chile and Argentina.*
