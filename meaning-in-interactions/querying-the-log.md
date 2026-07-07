# Querying the Log — Beating Long Context with Tools and Multi-Scale Retrieval

A method-focused companion to the other analytical docs, and the **"query" half** of the
*compact-and-query* through-line. Where
[clustering-representation-eval.md](clustering-representation-eval.md) asks how to **represent /
compact** an interaction stream so a model can read it, this doc asks the complementary question:
when the stream is too big to read at all, how do we let a model (or a pipeline) **reach into** the
full log on demand instead of shrinking it to fit?

It exists because of one under-developed bullet in
[ai-architecture-question.md](ai-architecture-question.md) §2.3 ("Why raw logs are hard for a
pretrained LLM"):

> **Long context.** A class session is thousands of events. Feeding the whole stream per inference is
> expensive and hits context limits — the LogLLM caveat. This pushes toward **summarizing /
> windowing / incremental memory** (which the spec's "detector memory" already anticipates).

That bullet only names the *compact* response (summarize / window / remember). There is a second,
largely orthogonal response the series hasn't developed: **don't fit the stream into context at
all — index it and query it.** This doc develops that response in two families:

- **Family A — Agentic querying / tool-use over the log.** Give the model *tools* (search, filter,
  count, fetch-state-at-time) and let it pull only the spans it needs. The general agentic-RAG
  pattern is well established; the open question is whether it has been done **for logs**, and what
  it buys us on *interaction* logs specifically.
- **Family B — Pre-organizing the stream so relevant spans are findable.** Window → features /
  embeddings → similarity search (the baseline the user already knows), plus the harder
  **multi-time-scale** problem: patterns that span more than one window, or that live at a different
  time scale than the window you chose.

The two families compose: Family A's tools are *queries over* Family B's index. The actionable form
of this work will live in [research-directions.md](research-directions.md) (Group 3 — see §6); this
doc is the reasoning and the literature scaffold behind it.

> **Honesty tiers (as in the other docs).** *Solid research* = canonical / repeatedly replicated.
> *Sound-but-unproven* = reasonable from one or few studies. *Gap* = barely touched, especially for
> K-12 open-ended classroom logs like CLUE.
>
> **⏳ STATUS: research-plan scaffold.** The *design reasoning* below is written now; the
> *literature findings* are **not yet gathered** — each is marked with a **`⏳ RESEARCH TO RUN`**
> block giving the exact searches, candidate papers, and verification checklist. Run those in a
> later session and fold the findings in, following the series' verify-before-cite rule. Until then,
> treat every empirical claim in this doc as **unverified** unless it cites an anchor already
> verified elsewhere in the series (LogLLM, the ensemble+RAG paper, differential sequence mining,
> GSP/PrefixSpan).

---

## The one-paragraph verdict (provisional — confirm via §6 research)

**The "query, don't stuff" response to long context is a mature, well-established pattern in general
LLM engineering (agentic RAG / tool-use) and in classical time-series/sequence mining (indexing,
motif discovery, sequential-pattern mining). What is almost certainly a gap is the *specific
composition* for our setting: an agent given query tools over a K-12 open-ended interaction stream,
backed by a multi-scale index that lets the same pattern be found whether it takes ten seconds or ten
minutes.** Family A's building blocks (retrieval, tool-use, aggregation-as-a-tool to dodge the
numeric-reasoning weakness) are canonical; whether anyone has wired them over *logs* — and how well
it works vs. fine-tuned specialists — is the thing to search (§2). Family B's building blocks
(sliding-window pattern mining with gap/window constraints, variable-length motif discovery,
hierarchical summarization indexes) are canonical; the multi-scale-on-interaction-logs application is
the thing to search (§3). *This paragraph is the hypothesis; §6 is how to test it before printing
it.*

---

## 1. Framing — two responses to long context

The long-context problem has two fundamentally different fixes, and the series has only developed one.

| Response | Move | Where it lives in the series |
|---|---|---|
| **Compact** | Shrink the stream to fit the window: summarize, sessionize, window, embed, remember. | [techniques.md](techniques.md) (sessionize/compress), [clustering-representation-eval.md](clustering-representation-eval.md) (represent), [ai-architecture-question.md](ai-architecture-question.md) ("detector memory") |
| **Query** | Leave the stream large; **index it and pull spans on demand.** | **This doc** |

They are not exclusive — a real system does both (compact each retrieved span; query to decide what to
retrieve). But they trade off differently. **Compaction is lossy and eager**: you decide up front
what to keep, before you know what the question is. **Querying is lossless and lazy**: the full
record stays available, and relevance is decided per question, at the cost of building and
maintaining an index and paying retrieval latency. For a detector or feedback agent whose "interesting
pattern" is *defined per study and changes* (the flexibility the spec banks on — see
[ai-architecture-question.md](ai-architecture-question.md) §2.3), lazy/lossless is attractive: you
don't have to have compacted for the right question in advance.

The rest of the doc is the two query families and how they combine.

---

## 2. Family A — Agentic querying / tool-use over the log

**The idea.** Instead of serializing thousands of events into one prompt, give the model a small set
of **tools** and let it issue queries: *"how many `select` events in the last 5 minutes?", "show me
the document state at 10:42", "find the last time this student changed the graph axis", "diff the
response block between t1 and t2."* The model reasons over *results*, not raw stream. This is the
ordinary agentic-RAG / tool-use loop, and it directly attacks **all three** of the §2.3
raw-log weaknesses at once:

- **Long context** — only retrieved spans enter the prompt.
- **Numeric reasoning & hallucination** — counts, gaps, durations, and aggregates are computed by a
  **tool** (exact arithmetic), not guessed by the model. This is the single strongest argument for
  Family A over pure serialization: it *removes* the numeric-reasoning failure mode rather than
  hoping a better encoding mitigates it. Ties to the gap-duration-marker idea (offload gap arithmetic
  to a deterministic pre-processor) — a query tool generalizes that from "gaps" to "any aggregate."
- **Serialization** — the model sees small, purpose-fetched slices, which are far easier to serialize
  well than the whole stream (fewer tokens per slice → the serialization choice from
  [clustering-representation-eval.md](clustering-representation-eval.md) matters less per call).

### 2.1 What the tools would be over a CLUE / Activity-Player stream

A concrete, minimal tool surface (design, not literature):

- **`search(predicate, time_range)`** — events matching a type / target / actor filter within a
  window. The workhorse.
- **`aggregate(metric, group_by, time_range)`** — counts, rates, gap statistics, dwell times.
  *This is the numeric-reasoning offload.*
- **`state_at(t)`** — reconstruct the document / tile / response state at time `t` from the event
  history (CLUE logs are document-mutating events, so state is a fold over the stream).
- **`diff(t1, t2)`** — what changed in the artifact between two times (the "document-history diff"
  serialization from [ai-architecture-question.md](ai-architecture-question.md) §4, exposed as a
  query rather than baked into the prompt).
- **`timeline(granularity)`** — a coarse index the agent reads first to decide where to zoom (the
  bridge to Family B / §4).

The design claim: with `aggregate` + `state_at` + `diff`, the model rarely needs raw event lines at
all — it works at the level of *artifacts and statistics*, which is where "meaning" actually lives
and where LLMs are strongest.

### 2.2 Is there log-specific research on giving an LLM query tools?

This is the user's central literature question: agentic RAG is established in general — but has
anyone done it **for logs**, e.g. in log-anomaly research?

> **⏳ RESEARCH TO RUN — Family A literature.**
>
> **Anchors already in the series (start here, re-read before searching):**
> - **LogLLM** (Guan et al., 2024, arXiv:2411.08561) — *verified in-series.* Explicitly notes
>   zero-shot prompting becomes **impractical for large window sizes** because the whole log sequence
>   must enter the prompt. This is the exact problem Family A dodges — cite it as the motivation.
> - **Ensemble + RAG log paper** (arXiv:2406.07467) — *scan-only in-series, re-verify.* Already
>   flagged as needing a standalone hybrid rather than a lone LLM; it is the closest existing
>   "retrieval over logs" anchor. **Verify what the RAG actually retrieves over** (log templates?
>   historical incidents? similar windows?) — that detail decides how relevant it is.
>
> **Searches to run** (arXiv, ACL Anthology, Google Scholar; 2023–2026 skew):
> - `LLM agent log analysis tools` · `retrieval augmented generation log anomaly detection` ·
>   `LLM tool use system logs` · `agentic log analysis` · `LLM query interface observability logs`
> - `RAG log parsing` · `in-context learning log anomaly` · `LLM function calling telemetry`
> - Industry/observability angle (may only exist as engineering write-ups, not papers):
>   `LLM copilot for logs`, `natural language query over logs`, Honeycomb/Datadog/Elastic
>   "AI query assistant" — **label these clearly as engineering practice, not peer-reviewed**, per the
>   series honesty convention.
> - Education angle (expect little/none — that absence is itself a finding): `LLM agent student log`,
>   `tool use learning analytics`, `retrieval interaction log tutor`.
>
> **What to extract per source:** (a) what the tools/queries are; (b) what the retrieval indexes
> over; (c) whether it beats a fine-tuned specialist or just a naive full-context prompt;
> (d) latency/cost; (e) domain (system logs vs. user-interaction logs — flag the mismatch loudly, per
> the series rule that almost none of this was built for K-12 open-ended software).
>
> **Verification checklist:** fetch each load-bearing paper (don't cite from abstract alone); mark
> open/paywalled; assign an honesty tier; if you find the agentic-query-over-logs pattern is common
> in *system-log* research but absent for *interaction/education* logs, **say exactly that** — it is
> the most likely and most useful conclusion, and it sets up the Group 3 proposal.
>
> **Absence-claim guard** (see [../grounding-llm-help](../grounding-llm-help/) lesson recorded in
> working-notes): do **not** write "no one has done X" without an actual search behind it. "Did not
> find" ≠ "does not exist."

### 2.3 Provisional take (to confirm)

Expected shape of the answer: **the pattern is mature for system/observability logs and general
agents, thinly explored for user-interaction logs, and essentially untouched for K-12 open-ended
education logs.** If that holds, Family A is *low technical risk, high novelty-for-our-domain* — the
sweet spot for a Group 3 proposal. Confirm before asserting.

---

## 3. Family B — Pre-organizing the stream: clustering & multi-scale retrieval

Family A needs something to query *against*. The naive index is the one the user described: chop the
stream into windows, describe each window by features or an embedding, and do similarity search over
windows. This is the retrieval-flavored sibling of
[clustering-representation-eval.md](clustering-representation-eval.md) (same embed step, different
downstream task — retrieval instead of cluster-vs-label; note the doc's own caveat that *clustering ≠
retrieval*, so the serialization winner there is a **prior, not a verdict** here).

The baseline works until you hit the user's real question:

### 3.1 The multi-time-scale problem

**Fixed windows have a fixed time scale, but meaningful patterns don't.** A "rage-click" burst lives
in ~10 seconds; a "wheel-spinning on one goal" pattern lives across ~10 minutes; a "gave up and
wandered" pattern might span a whole session. Any single window size:

- **too small** → a long pattern is shredded across many windows, and no single window's
  embedding/feature-vector contains it (it's invisible to per-window similarity search);
- **too large** → a short pattern is diluted to noise inside a window dominated by other activity.

And patterns **span window boundaries**: the same event sequence split differently by an arbitrary
grid produces different (and non-matching) window vectors. This is the crux the user named: *how do
you find a pattern that spans more than one window, or that lives at a different scale than your
window?*

### 3.2 Techniques for multi-scale pattern search

The design menu (families of known techniques; §6 gathers the citations):

- **Variable-length / gap-tolerant sequential-pattern mining.** The series already carries the
  anchor: **GSP** (Srikant & Agrawal 1996) adds *min/max gap constraints* and a *sliding window* to
  Apriori-style sequence mining, and **PrefixSpan** (Pei, Han et al.) mines the *full* set of
  sequential patterns without a fixed length (both in [techniques.md](techniques.md) §"sequential
  pattern mining"). Gap constraints + no fixed length is *precisely* a partial answer to
  multi-scale: the pattern is defined by its *order and gaps*, not by which fixed window it fell in.
  [papers/differential-sequence-mining-kinnebrew-2013.md](papers/differential-sequence-mining-kinnebrew-2013.md)
  is the education instance (a configurable `-REL/-IRR` window over the stream).
- **Motif discovery / matrix profile.** Find repeated subsequences of *arbitrary length and offset*
  without pre-set windows (matrix profile / MASS family). Scale-robust by construction. **Verify and
  cite in §6.**
- **Hierarchical / multi-resolution windowing.** Index the stream at several window sizes at once
  (10 s, 1 min, 10 min, session) and search all levels; a pattern registers at whatever level matches
  its natural scale. Cheap, obvious, effective — the pragmatic first thing to build.
- **Hierarchical summarization tree (RAPTOR-style).** Recursively summarize/embed spans into a tree,
  so a query can match a leaf (fine) or an internal node (coarse). Ties Family B directly to
  Family A: `timeline(granularity)` is a read over this tree. **Verify RAPTOR and any log/time-series
  analogues in §6.**
- **Shapelets / learned subsequence features**, and **multi-resolution time-series representations**
  (wavelets, SAX at multiple scales) — the time-series-analysis lineage. **Verify in §6; keep only
  what's load-bearing.**

> **⏳ RESEARCH TO RUN — Family B literature.**
>
> **Searches:** `multi-scale motif discovery time series` · `matrix profile variable length` ·
> `multi-resolution sequential pattern mining` · `hierarchical windowing event log` ·
> `cross-window pattern detection clickstream` · `RAPTOR recursive summarization retrieval` ·
> `multi-scale event sequence embedding` · `variable length pattern mining user behavior` ·
> (education) `sequential pattern mining learning analytics multi-scale` · `wheel spinning detection window size`.
>
> **What to extract:** how each handles *scale* and *boundaries*; whether it's unsupervised (no
> labels — matches our labeling bottleneck); computational cost at class-session scale; whether it's
> ever been applied to *user-interaction* streams vs. only sensor/system time series.
>
> **Verification:** fetch primary sources for matrix profile and RAPTOR (don't cite from blog posts);
> GSP/PrefixSpan/differential-sequence-mining are already in-series (GSP & PrefixSpan paywalled →
> keep abstract-level, differential-sequence-mining fetched). Flag domain mismatch: most time-series
> motif work is sensor data, not semantic UI events.
>
> **Open sub-question worth its own note:** does a good *multi-scale embedding* let plain
> similarity-search subsume the mining approaches (embed spans at several scales, one vector index,
> query returns nearest at any scale)? That would be the cleanest bridge to Family A — check whether
> anyone has shown it.

---

## 4. Synthesis — the two families are one system

They compose into a **coarse-to-fine agentic loop**:

1. **Index once (Family B):** build a multi-scale index — hierarchical windows or a summarization
   tree — over the full session stream. Unsupervised, so no labels needed.
2. **Orient (Family A):** the agent reads the *coarse* level first (`timeline`) — a cheap, small
   overview of the whole session.
3. **Zoom (A over B):** it issues `search` / `aggregate` / `state_at` / `diff` against the levels
   that look interesting, pulling only those spans into context.
4. **Decide:** it reasons over retrieved artifacts + exact aggregates — never the raw full stream,
   never guessed numbers.

This is the concrete realization of the *compact-and-query loop* the working notes call the intended
center of gravity: **Family B is the compaction/index, Family A is the query.** It's also the machinery
[research-directions.md](research-directions.md) §3.4 (the GenAI feedback agent) says it needs
("log compaction + query tools") — this doc is the design behind that one-line requirement.

**What this does *not* solve:** whether the retrieved-and-aggregated view actually preserves the
*meaning* (a multi-scale index can still miss a pattern no level captures cleanly, and aggregates can
launder away the very texture that made a moment interesting). That's the same
representation-adequacy worry as [clustering-representation-eval.md](clustering-representation-eval.md)
§7 (decodable ≠ used; separable ≠ readable), now in retrieval form: *retrievable ≠ meaningful.* Name
it; don't hand-wave it.

---

## 5. Honesty tiers & gaps for our setting

- **Solid research:** agentic RAG / tool-use as an engineering pattern; sequential-pattern mining
  with gap/window constraints (GSP, PrefixSpan); the general retrieval-index toolkit. *(General —
  little of it built for interaction logs.)*
- **Sound-but-unproven (pending §6):** RAG/tool-use applied to *logs* (system-log evidence likely
  exists; interaction-log evidence likely thin); multi-scale motif/summary indexes over *event*
  streams.
- **Gap:** the whole composition — an agent with query tools over a **multi-scale index of a K-12
  open-ended interaction stream**, evaluated for whether it finds "interesting" moments with usable
  precision/latency. Almost certainly unbuilt for our domain. *(Confirm the absence via §6, don't
  assume it.)*

The recurring domain-mismatch flag applies hard here: system logs are templated, high-volume, and
semantically shallow; CLUE/AP logs are low-volume-per-student, semantically rich, and open-ended.
Techniques proven on the former may not transfer — say so wherever this doc leans on system-log work.

---

## 6. Where this becomes actionable (Group 3 pointer)

Per the **hard rule** (working-notes: only the user promotes into Group 1/2), this doc proposes only
in **Group 3** of [research-directions.md](research-directions.md). Two candidate items to add there
after the §2/§3 research is folded in (leaving the exact wording to a follow-up so it's user-approved):

- **A "query-tools over interaction logs" direction** — likely an *extension of §3.4* (which already
  lists "log compaction + query tools" as a need) rather than a wholly new item: specify the tool
  surface (§2.1), and pose the eval — does an agent with these tools beat a full-context prompt and a
  fine-tuned specialist on finding interesting moments in CLUE logs?
- **A "multi-scale index" direction** — likely a *note under §3.1 (serialization study)* or §3.8
  (clustering eval): the retrieval sibling of the clustering eval, testing whether a multi-scale
  index finds cross-window / variable-duration patterns single-window similarity misses.

Decide new-item-vs-extension with the user once the literature is in.

---

## Verification note

**Status at time of writing: scaffold only.** No new literature was gathered for this doc. Claims
rest on anchors already verified elsewhere in the series (LogLLM 2411.08561; ensemble+RAG 2406.07467
— scan-only, re-verify; GSP/PrefixSpan — paywalled, abstract-level; differential sequence mining
Kinnebrew 2013 — fetched). Everything in a **`⏳ RESEARCH TO RUN`** block is **unverified and not yet
searched**. When folding in findings, follow the series conventions: fetch primary sources, mark
open/paywalled, assign honesty tiers, flag domain mismatch, and honor the absence-claim guard (search
before writing "no one has done X"). Update this note with the confidence tiers of what you add.

---

## References

*Full details + accessibility in [the series bibliography](bibliography.md). Anchors reused here (not
re-verified in this doc); see the in-doc research-to-run notes for confidence tiers.*

- [Guan et al. (2024) — LogLLM](bibliography.md#guan-2024) 🟢
- [Ensemble + RAG for log anomaly detection](bibliography.md#ensemble-rag-2024) 🟢⚠️
- [Srikant & Agrawal (1996) — GSP](bibliography.md#srikant-1996) 🔒
- [Pei, Han et al. (2001/2004) — PrefixSpan](bibliography.md#pei-2001) 🔒
- [Kinnebrew, Loretz & Biswas (2013) — differential sequence mining](bibliography.md#kinnebrew-2013) ✅
