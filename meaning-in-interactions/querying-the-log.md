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
largely orthogonal response this compendium hasn't developed: **don't fit the stream into context at
all — index it and query it.** This doc develops that response in two families:

- **Family A — Agentic querying / tool-use over the log.** Give the model *tools* (search, filter,
  count, fetch-state-at-time) and let it pull only the spans it needs. The general agentic-RAG
  pattern is well established; the open question is whether it has been done **for logs**, and what
  it buys us on *interaction* logs specifically.
- **Family B — Pre-organizing the stream so relevant spans are findable.** Window → features /
  embeddings → similarity search (the standard baseline), plus the harder
  **multi-time-scale** problem: patterns that span more than one window, or that live at a different
  time scale than the window you chose.

The two families compose: Family A's tools are *queries over* Family B's index. The actionable form
of this work will live in [research-directions.md](research-directions.md) (Group 3 — see §6); this
doc is the reasoning and the literature scaffold behind it.

> **Honesty tiers (as in the other docs).** *Solid research* = canonical / repeatedly replicated.
> *Sound-but-unproven* = reasonable from one or few studies. *Gap* = barely touched, especially for
> K-12 open-ended classroom logs like CLUE.
>
> **STATUS: researched (July 2026).** The literature for both families has been gathered and
> verified (a fan-out deep-research pass plus direct fetches; see the
> [Verification note](#verification-note)). The headline result is a **domain asymmetry**: both
> families are real, active research areas, but Family A's evidence is almost entirely **system /
> AIOps / security logs** and Family B's multi-scale-motif evidence is almost entirely **sensor time
> series**. The **K-12 open-ended interaction-log** application of either is a **"did not find,"
> reported honestly as such — not a proven void** (a few education/user-interaction adjacencies exist
> and are cited below).

---

## The one-paragraph verdict

**The "query, don't stuff" response to long context is a mature, well-established pattern — and the
research confirms it on both sides, with one sharp caveat.** Family A (give an LLM query/tool access
over logs instead of stuffing the full log in the prompt) is a real, active area: tool-augmented
log-analysis agents exist and are peer-reviewed — [RCAgent](bibliography.md#wang-2024) (CIKM 2024)
even adds an *Observation Key-Value Store* so the agent "reference[s] lengthy data without expanding
prompt context," the single cleanest instance of the idea — and multiple 2025 surveys catalogue
LLMs-for-logs. Family B's multi-scale side is equally solid: the [Matrix
Profile](bibliography.md#yeh-2016) family ([VALMOD](bibliography.md#linardi-2018),
[MAD](bibliography.md#linardi-2020)) discovers repeated subsequences over a **range of lengths with
no preset window** — a direct answer to the multi-time-scale problem. **The caveat is domain:** every
verified Family A system is a system/AIOps log, and every verified Family B motif method is sensor
time series. The **K-12 open-ended interaction-log composition** — an agent with query tools over a
multi-scale index of a student's event stream — is a genuine gap, reported as **"did not find," not
"does not exist"** (the education/user-interaction adjacencies that *do* exist —
[MOOC clickstream mining](bibliography.md#brinton-2015),
[multiscale-window dropout prediction](bibliography.md#cheng-2025),
[multi-scale user-behavior embeddings](bibliography.md#fu-2023),
[log-contextualized RAG](bibliography.md#cohn-2025) — are cited in §§2–3). So the building blocks are
canonical; wiring them together for our domain is the open, and now well-motivated, work.

---

## 1. Framing — two responses to long context

The long-context problem has two fundamentally different fixes, and this compendium has developed only one.

| Response | Move | Where it lives in this compendium |
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

The central literature question: agentic RAG is established in general — but has anyone done it
**for logs**, e.g. in log-anomaly research? **Yes, and it is an active area — almost
entirely in the system / AIOps / security-log domain.** *(Solid research; domain-mismatched to ours —
see the flag at the end.)*

- **Tool-augmented / agentic log analysis exists in peer-reviewed work.**
  [RCAgent](bibliography.md#wang-2024) (Wang et al., Alibaba, *CIKM 2024*) is an autonomous
  root-cause-analysis agent that "employ[s] data querying functions as information-gathering tools and
  LLM-based expert agents as analytical tools," and — the detail most on-point for us — avoids
  full-log-in-prompt with an **Observation Key-Value Store** "enabling the agent to reference lengthy
  data without expanding prompt context." Microsoft's [ReAct RCA agent](bibliography.md#roy-2024)
  (Roy et al., *FSE 2024 Companion*) makes the motivation explicit: prior LLM approaches "put a fixed
  set of information in context and cannot dynamically collect additional diagnostic information such
  as … logs, metrics or databases," so they equip a **ReAct agent with retrieval tools** instead.
  Both are exactly Family A: the model *queries* the logs rather than ingesting them.
- **The problem is documented, not hypothetical.** [LogLLM](bibliography.md#guan-2024) (Guan et al.,
  2024) notes that integrating the log sequence directly into a prompt "becomes impractical when using
  a large window size" — the published statement of exactly the long-context wall querying is meant to
  get around. (LogLLM's own fix is fine-tuning, not querying, so it's a motivation here, not a Family A
  technique.)
- **FlexLog — where the LLM's "retrieval" is thinner than it sounds.**
  [FlexLog](bibliography.md#flex-log-2024) (*"LLM meets ML: Data-efficient Anomaly Detection on
  Unstable Logs"*, Hadadi, Xu, Bianculli & Briand, 2024; ACM TOSEM 2025) — which
  [ai-architecture-question.md](ai-architecture-question.md) cites as the log-anomaly approach that
  needed an ensemble + RAG hybrid rather than a standalone LLM — combines an ML ensemble (decision
  tree, kNN, feed-forward net) with a Mistral LLM plus a **cache + RAG**. Its RAG retrieves
  *descriptive metadata about log templates / system calls* (e.g. what `setxattr` means) to gloss the
  input in the prompt — **not** retrieval of similar historical log windows or incidents (the cache is
  the separate exact-match store). So FlexLog is a *glossary-enrichment* RAG, a **weaker** "query the
  log" instance than RCAgent's tool-calling.
- **Surveys confirm this is a recognized field, not a one-off.** [Akhtar, Khan & Parkinson
  (2025)](bibliography.md#akhtar-2025), *"LLM-based event log analysis techniques: A survey"*
  (arXiv preprint), organizes the space into **fine-tuning, RAG, and in-context learning** — so
  RAG-over-logs is a *named* technique (though the survey instantiates it thinly, mostly as
  future work). [*"A Survey of AIOps in the Era of LLMs"*](bibliography.md#aiops-survey-2025) (*ACM
  Computing Surveys* 2025) spans **183 papers** and calls log parsing "the most actively studied
  task"; [*"LLM/Agent-as-Data-Analyst"*](bibliography.md#llm-data-analyst-2025) lists
  **tool-augmented workflows** among four core design goals for data-analysis agents. On the
  engineering side, natural-language-query-over-logs is shipping (e.g. LLMs that translate a question
  into PromQL/LogQL executed against the store, per an [industry
  write-up](bibliography.md#algomox-graylit)) — *practice, not peer-reviewed evidence.*
- **Aggregation-as-a-tool (Family A's numeric-reasoning offload) is implicit, not isolated.** The
  agentic systems above *do* call query/aggregation functions, but we **did not find** a study that
  isolates "wire exact count/aggregate/timing tools to an LLM over event data" as its own measured
  contribution. Worth a targeted search before claiming novelty either way.

**Education / user-interaction domain — did not find the pattern; found adjacencies.** The strongest
ed-tech LLM-on-student-logs example we found does the *opposite* of Family A: [Ober et al.
(JEDM)](bibliography.md#ober-2025) feeds student dialogue **directly** into ChatGPT-4o prompts to code
persistence — no indexing, retrieval, or tools (a clean anti-example). The closest thing to
retrieval-over-interaction-logs in education is [LC-RAG](bibliography.md#cohn-2025) (Cohn et al.,
2025, the C2STEM modeling environment), which uses environment logs to **contextualize** discourse so
RAG can fetch curated *domain* knowledge — i.e. logs as retrieval *context*, not the object being
queried. **We did not find agentic query-tools operating over a K-12 interaction stream** — but the
learning-analytics/EDM venues were not searched exhaustively, so this is a *"did not find,"* not a
proven absence ([[verify-absence-claims]]).

### 2.3 What the evidence says

**The pattern is mature for system/observability logs and general data-analysis agents, genuinely
thin for user-interaction logs, and not found for K-12 open-ended education logs.** That makes
Family A *low technical risk* (the machinery is proven and productized elsewhere) and *high
novelty-for-our-domain* (nobody we found has pointed it at student interaction streams) — the sweet
spot for a Group 3 proposal (§6). The load-bearing unknown is not "can you build it" but "does it beat
a full-context prompt *and* a fine-tuned specialist on **our** logs, at usable precision/latency."

---

## 3. Family B — Pre-organizing the stream: clustering & multi-scale retrieval

Family A needs something to query *against*. A naive index is: chop the stream into windows, describe
each window by features or an embedding, and do similarity search over windows. This is the retrieval-flavored sibling of
[clustering-representation-eval.md](clustering-representation-eval.md) (same embed step, different
downstream task — retrieval instead of cluster-vs-label; note the doc's own caveat that *clustering ≠
retrieval*, so the serialization winner there is a **prior, not a verdict** here).

This baseline approach doesn't handle some important cases:

### 3.1 The multi-time-scale problem

**Fixed windows have a fixed time scale, but meaningful patterns don't.** A "rage-click" burst lives
in ~10 seconds; a "wheel-spinning on one goal" pattern lives across ~10 minutes; a "gave up and
wandered" pattern might span a whole session. Any single window size:

- **too small** → a long pattern is shredded across many windows, and no single window's
  embedding/feature-vector contains it (it's invisible to per-window similarity search);
- **too large** → a short pattern is diluted to noise inside a window dominated by other activity.

And patterns **span window boundaries**: the same event sequence split differently by an arbitrary
grid produces different (and non-matching) window vectors. This is the crux: *how do you find a
pattern that spans more than one window, or that lives at a different scale than your window?*

### 3.2 Techniques for multi-scale pattern search

The design menu, now with verified citations. **The pattern to notice: the strongest, most exact
techniques are all *sensor time-series* work; the education/user-interaction instances are fewer,
newer, and less battle-tested.** *(Motif/matrix-profile line: solid research, domain-mismatched.
Education line: sound-but-unproven for our exact use.)*

- **Motif discovery / matrix profile — the best answer to "no preset window."** The [Matrix
  Profile](bibliography.md#yeh-2016) (Yeh et al., *IEEE ICDM 2016*) is a unifying primitive for
  motifs, discords, and shapelets; [**VALMOD**](bibliography.md#linardi-2018) (Linardi et al.,
  *SIGMOD 2018*) and its journal extension [**"Matrix Profile goes MAD"**](bibliography.md#linardi-2020)
  (*DMKD* 2020) explicitly solve the fixed-length limitation — they "efficiently find all motifs [and
  discords] in a given range of lengths," which is exactly *scale-robust without committing to a
  window*. **Domain flag:** every evaluation is sensor/physiological/scientific series (ECG, EEG,
  seismology, industrial) — **none verified on event/clickstream/education logs.** The range is also
  user-specified `[l_min, l_max]`, so it's "variable within a range," not unbounded.
- **Variable-length / gap-tolerant sequential-pattern mining.** This compendium already carries the anchor:
  [**GSP**](bibliography.md#srikant-1996) (Srikant & Agrawal 1996) adds *min/max gap constraints* and
  a *sliding window* to Apriori-style sequence mining, and [**PrefixSpan**](bibliography.md#pei-2001)
  (Pei, Han et al.) mines the *full* set of sequential patterns without a fixed length (both in
  [techniques.md](techniques.md) §"sequential pattern mining"; gap/window support and
  arbitrary-length patterns both re-confirmed this pass). Gap constraints + no fixed length is
  *precisely* a partial answer to multi-scale: the pattern is defined by its *order and gaps*, not by
  which fixed window it fell in.
  [papers/differential-sequence-mining-kinnebrew-2013.md](papers/differential-sequence-mining-kinnebrew-2013.md)
  is the education instance (a configurable `-REL/-IRR` window over the stream), and — a real
  interaction-log example — [Brinton et al. (2015)](bibliography.md#brinton-2015) mine recurring
  subsequences ("reflecting," "revising") from **MOOC video clickstreams** and relate them to
  performance.
- **Hierarchical / multi-resolution windowing.** Index the stream at several window sizes at once
  (10 s, 1 min, 10 min, session) and search all levels; a pattern registers at whatever level matches
  its natural scale. Cheap, obvious, effective — the pragmatic first thing to build. **And it has a
  fresh education instance:** [DMSW](bibliography.md#cheng-2025) (Cheng et al., 2025) runs a
  **Dual-Modal Multiscale Sliding Window** over student behavioral+academic data — time windows of
  varying sizes to catch abrupt behavioral change — for dropout prediction. *(2025 preprint; treat
  metrics cautiously.)*
- **Hierarchical summarization tree (RAPTOR-style).** [RAPTOR](bibliography.md#sarthi-2024) (Sarthi
  et al., *ICLR 2024*) recursively embeds→clusters→summarizes spans into a tree, so a query can match
  a fine leaf or a coarse internal node. Ties Family B directly to Family A: `timeline(granularity)`
  is a read over this tree. **Domain flag:** RAPTOR is general text-RAG; we **did not find** a
  logs/time-series analogue that builds such a tree over an event stream — an open build.
- **Multi-resolution symbolic representations** (SAX, and symbolic-sequence + linear-model classifiers
  like [MrSEQL](bibliography.md#lenguyen-2019), Le Nguyen et al., *DMKD 2019*) — the time-series
  lineage that discretizes at multiple resolutions. Again sensor-domain; kept here as the "represent
  each scale symbolically, then mine" option.

**The Q6 sub-question — can a *multi-scale embedding* let plain similarity search subsume the mining?**
The closest verified answer is [**MSDP**](bibliography.md#fu-2023) (Fu et al., *CIKM 2023*), a
**Multi-scale Stochastic Distribution Prediction** self-supervised pretraining that learns robust
**user-behavior-sequence** representations (predicting the *distribution* of behaviors over a future
window rather than the noisy next event). It is the right shape — multi-scale, embedding-based, over
user-behavior streams — but it is industrial recommender data, **not education**, and it learns a
representation rather than demonstrating "one index, query returns nearest at any scale." So Q6 is
*partially* answered in the user-behavior domain and **open for interaction/education logs.**

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

- **Solid research:** agentic tool-use / RAG over logs as an engineering pattern (RCAgent, Microsoft
  ReAct RCA, FlexLog, the AIOps/log surveys); variable-length, no-preset-window motif discovery
  (Matrix Profile, VALMOD, MAD); sequential-pattern mining with gap/window constraints (GSP,
  PrefixSpan); RAPTOR hierarchical-summary retrieval. **All verified — but Family A's are all
  *system/AIOps logs* and Family B's motif work is all *sensor time series.***
- **Sound-but-unproven for our use:** the education/user-interaction adjacencies — MOOC clickstream
  subsequence mining (Brinton 2015), multiscale-window dropout prediction (DMSW 2025), multi-scale
  user-behavior embeddings (MSDP, CIKM 2023), log-contextualized RAG (LC-RAG 2025). Right shape,
  wrong-or-adjacent task; several are recent single-paper preprints.
- **Gap (did not find, not proven void):** the whole composition — an agent with query tools over a
  **multi-scale index of a K-12 open-ended interaction stream**, evaluated for whether it finds
  "interesting" moments with usable precision/latency. Not found in this pass; learning-analytics/EDM
  venues were not searched exhaustively, so treat as an opening, not a certainty ([[verify-absence-claims]]).

The recurring domain-mismatch flag applies hard here: system logs are templated, high-volume, and
semantically shallow; CLUE/AP logs are low-volume-per-student, semantically rich, and open-ended.
Techniques proven on the former may not transfer — say so wherever this doc leans on system-log work.

---

## 6. Where this becomes actionable (Group 3 pointer)

Groups 1 and 2 of [research-directions.md](research-directions.md) are hand-curated, so this doc
proposes only in **Group 3**. With the literature now in, two candidate items are ready to add (final
wording left to that curation step):

- **A "query-tools over interaction logs" direction** — likely an *extension of §3.4* (which already
  lists "log compaction + query tools" as a need) rather than a wholly new item: specify the tool
  surface (§2.1), and pose the eval — does an agent with these tools beat a full-context prompt and a
  fine-tuned specialist on finding interesting moments in CLUE logs?
- **A "multi-scale index" direction** — likely a *note under §3.1 (serialization study)* or §3.8
  (clustering eval): the retrieval sibling of the clustering eval, testing whether a multi-scale
  index finds cross-window / variable-duration patterns single-window similarity misses.

Whether each lands as a new item or an extension is a call for that curation step.

---

## Verification note

**Literature gathered July 2026** via a fan-out deep-research pass (6 search angles → 26 sources
fetched → 114 claims extracted → 25 adversarially verified by 3-vote, 23 confirmed) plus direct
follow-up fetches for the education/user-interaction papers, which the automated pass surfaced but did
not itself 3-vote.

- **Verified (3-vote or directly fetched this pass):** RCAgent (CIKM 2024), Microsoft ReAct RCA (FSE
  2024), FlexLog (2406.07467), LogLLM (2411.08561), the three surveys (2502.00677, 2507.12472,
  2509.23988), the JEDM persistence anti-example; Matrix Profile (Yeh 2016), VALMOD (2018), MAD
  (2020); Brinton MOOC clickstream (1503.06489), DMSW (2505.11119), LC-RAG (2505.17238) — the last
  three fetched directly for citation details.
- **Two claims scoped precisely.** (1) FlexLog's RAG (arXiv:2406.07467) retrieves
  log-template/system-call metadata to gloss the input, **not** similar historical log spans — a
  glossary-enrichment RAG, confirmed from the full text. (2) LogLLM's large-window claim is only that
  direct prompting **"becomes impractical … [at] large window size"**; it does not state the stronger
  "whole sequence must go in the prompt" reason, so this doc does not assert it either.
- **Scan-only / abstract-level (⚠️, not independently deep-read):** MSDP (CIKM 2023, search-snippet
  level), MrSEQL (2019), RAPTOR (identity high-confidence, full text not re-read), the Algomox
  industry write-up (gray literature, not peer-reviewed).
- **Absence-claim guard honored.** The "no agentic query-tools over K-12 interaction logs" finding is
  a **"did not find,"** not a proven void: the adversarial pass explicitly **refuted (0–3)** a
  stronger "education gap confirmed" claim because learning-analytics/EDM venues were not searched
  exhaustively ([[verify-absence-claims]]).
- **Domain-mismatch flag (the central caveat):** every verified Family A system is a system/AIOps
  log; every verified Family B motif method is sensor time series. These support the doc's *methods by
  analogy*; transfer to K-12 open-ended interaction logs is the proposed extension, not something
  these papers demonstrate.

---

## References

*Full details + accessibility in [the shared bibliography](bibliography.md). Verification tiers are in
the [Verification note](#verification-note) above.*

- [Akhtar, Khan & Parkinson (2025) — LLM event-log-analysis survey](bibliography.md#akhtar-2025) 🟢⚠️
- [Brinton et al. (2015) — Mining MOOC Clickstreams](bibliography.md#brinton-2015) 🟢
- [Cheng et al. (2025) — DMSW multiscale-window dropout](bibliography.md#cheng-2025) 🟢⚠️
- [Cohn et al. (2025) — LC-RAG (log-contextualized RAG)](bibliography.md#cohn-2025) 🟢
- [FlexLog — Hadadi et al. (2024/2025), arXiv:2406.07467](bibliography.md#flex-log-2024) 🟢
- [Fu et al. (2023) — MSDP multi-scale user-behavior embedding](bibliography.md#fu-2023) 🔒⚠️
- [Guan et al. (2024) — LogLLM](bibliography.md#guan-2024) 🟢
- [Kinnebrew et al. (2013) — differential sequence mining](bibliography.md#kinnebrew-2013) ✅
- [Le Nguyen et al. (2019) — MrSEQL symbolic TS classification](bibliography.md#lenguyen-2019) 🟢⚠️
- [Linardi et al. (2018) — VALMOD](bibliography.md#linardi-2018) 🟢 · [Linardi et al. (2020) — Matrix Profile goes MAD](bibliography.md#linardi-2020) 🟢
- [LLM/Agent-as-Data-Analyst survey (2025)](bibliography.md#llm-data-analyst-2025) 🟢⚠️
- [Ober et al. (JEDM) — LLM persistence coding (anti-example)](bibliography.md#ober-2025) 🟢
- [Pei, Han et al. (2001/2004) — PrefixSpan](bibliography.md#pei-2001) 🔒
- [Roy et al. (2024) — Microsoft ReAct RCA agent](bibliography.md#roy-2024) 🟢
- [Sarthi et al. (2024) — RAPTOR](bibliography.md#sarthi-2024) 🟢⚠️
- [Srikant & Agrawal (1996) — GSP](bibliography.md#srikant-1996) 🔒
- [Survey of AIOps in the Era of LLMs (2025)](bibliography.md#aiops-survey-2025) 🟢
- [Wang et al. (2024) — RCAgent](bibliography.md#wang-2024) 🟢
- [Yeh et al. (2016) — Matrix Profile I](bibliography.md#yeh-2016) 🟢
- [Algomox — NL-query-over-observability (industry)](bibliography.md#algomox-graylit) ⚠️
