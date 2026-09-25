# Finding Meaning in User Interactions

A set of research documents on **how to find meaning in a stream of user-interaction events** — the
interesting, noteworthy, or intent-revealing moments buried in interaction logs. Same
audience/context as the other presentations in this repo — people building and studying classroom
software (see [../audiences/ed-tech-research-group.md](../audiences/ed-tech-research-group.md)).

This is the **research/literature companion** to a piece of engineering already specified: the
[detector-driven notifications
spec](https://github.com/concord-consortium/collaborative-learning/pull/2742) (CLUE-330), which
builds a system to alert a researcher in real time when a student does something "interesting," so
they can interview the student while the moment is fresh. These docs ask: **what does the existing
research — in education and well beyond it — already know about this problem, and is education behind
on modern AI?**

The motivating thesis worth arguing with: *most educational work here predates transformers/LLMs and
relies on hand-built features or RNN-style sequence models; a modern pretrained LLM may detect
interaction patterns better — but it's an open question whether prompting a frontier model is enough
or we'd need to train our own.* The research both **supports and complicates** this (see the AI doc).

> **Read first: [Status & Limitations](status-and-limitations.md).** This is a fast first pass, not a
> finished report — parts are under-explained, the research directions need curation, the problem doc
> is stale, there's no single woven story yet, and some paywalled papers went unread. That note lays
> out what's provisional and what to do next.

1. **[finding-meaning-problem.md](finding-meaning-problem.md)** — *the problem, as a **straw man**.*
   What "meaning in an interaction" is and why it's hard. Two use cases now specified — real-time
   detection for researcher interviews, and real-time feedback to students (rule-based today, GenAI
   tomorrow) — plus a through-line that ties them together: the *compact-and-query* loop over the
   interaction log. *Still meant to be rewritten around the general problem as more use cases land.*

2. **[techniques.md](techniques.md)** — *the domain-agnostic methods.* The pipeline (sessionize →
   discover → rank by interestingness) and the succession of pattern models — association rules,
   sequential-pattern mining, Markov/process models, and the neural progression — drawn from the
   older, richer web-analytics, marketing, and UX/HCI literatures.

3. **[edtech-landscape.md](edtech-landscape.md)** — *what education actually did.* The constructs the
   field detects (struggle, gaming, affect, wheel-spinning), the fact that its built detectors are
   mostly **feature-engineering + classical ML**, knowledge tracing as the one neural stronghold,
   the **DDCI** paper our spec builds on, and the leading-edge **ClickSight** (LLM on raw
   clickstreams). **§2d–2e (Aug 2026)** add the **strategy/process** literature the first pass missed —
   log-based detectors of *systematic experimentation* that ran in real time and reportedly
   transferred across simulations, and **programming-process analytics** over code/block snapshots —
   with the distinction that organizes both: a **latent state the log hints at** (affect: modest
   ceiling, no transfer) is a much harder target than a **behavior the log contains** (strategy).

4. **[ai-architecture-question.md](ai-architecture-question.md)** — *the forward-looking argument.*
   Transformers vs RNNs (they win on big data, barely on education-scale data), and pretrained-LLM
   prompting vs training your own (prototype by prompting, harden by training) — with the evidence
   that supports the spec's "LLM first, ML/rules as optimization" instinct.

5. **[when-to-intervene.md](when-to-intervene.md)** — *the decision the detectors serve.* The
   intervention-timing literature that sits upstream of detection: impasse-driven learning (VanLehn),
   contingency/fading (Wood/Bruner/Ross), the assistance dilemma (Koedinger & Aleven), what expert
   tutors actually do, the affect twist (confusion is often the state *not* to interrupt), the adjacent
   *application-tutor* literature (Lumière/Clippy, product-analytics struggle signals), the
   design/ill-structured tutoring tradition (Jonassen, Schön), and the give-vs-ask-vs-invite *initiative*
   question (Razzaq & Heffernan; help avoidance; mixed-initiative). Two central claims: (1) our materials
   span gated/well-structured (wildfire sim with per-page goals) to open-ended/ill-structured, which need
   *different* intervention machinery; (2) on-request help shifts the meaning-detection target from
   "when to intervene" to "advise well when asked." Also checks the "intervene when affect changes"
   instinct and finds it legitimate but not primary.

6. **[clustering-representation-eval.md](clustering-representation-eval.md)** — *a method deep-dive.*
   Whether we can rank serializations by embedding each, clustering the embeddings, and scoring the
   clusters against known groups (human labels or detector/DDCI groups) — a label-light way to test if
   an embedding can *match* a classification ML model (and, as an extension, *discover* what it
   missed). Verdict: every component is canonical (external cluster validation; the MTEB Clustering
   task; serialization-sensitivity; Generalized/Novel Category Discovery), but applying it to *rank
   serializations* and to *interaction logs vs. human codes* is a genuine gap. Also asks whether the
   method's core assumption — embedding separability as a proxy for LLM-readability — actually holds
   (partly: intrinsic≠extrinsic is canonical; geometry-predicts-performance has fresh positive
   evidence; the generative-LLM bridge is untested). Backs research-direction 3.8.

7. **[querying-the-log.md](querying-the-log.md)** — *the "query" half of compact-and-query.* When the
   stream is too big to read, don't shrink it to fit — **index it and query it.** Two families:
   agentic **tool-use over the log** (search/aggregate/state-at/diff — which also offloads the
   numeric-reasoning weakness to exact tools), and **pre-organizing the stream** into a multi-scale
   index that finds patterns spanning more than one window or living at a different time scale than
   the window you chose. Provisional verdict: the building blocks are canonical (agentic RAG; GSP/
   PrefixSpan gap-tolerant mining; motif discovery; hierarchical-summary indexes) but the composition
   over K-12 open-ended interaction logs is a gap. **Researched (July 2026):** a domain asymmetry —
   Family A's evidence is all system/AIOps logs (RCAgent, Microsoft ReAct RCA, FlexLog, surveys),
   Family B's multi-scale motif work is all sensor time series (Matrix Profile / VALMOD / MAD); the
   interaction-log application is a *"did not find," not a proven void*, with a few education
   adjacencies (MOOC clickstream mining, multiscale-window dropout, multi-scale user-behavior
   embeddings, log-contextualized RAG).

Start with doc 1 for the problem, doc 2 for the method space, doc 3 for what education has tried, doc
4 for the build decision, doc 5 for *when* acting is warranted, doc 6 for the clustering-based
serialization eval, doc 7 for querying the log instead of stuffing it into context.

- **[research-directions.md](research-directions.md)** — *the actionable companion.* What we could
  build or test, in three groups — two curated by *value* (high vs. questionable, both feasible) plus
  an uncurated staging area for proposals — each linking back to the analytical docs. The canonical home for "so what do we do next."

- **[papers/](papers/)** — per-paper deep-dives of the studies closest to our work
  ([DDCI](papers/ddci-baker-2024.md), [ClickSight](papers/clicksight-radmehr-2025.md),
  [pyKT](papers/pykt-liu-2022.md), the [differential-sequence-mining
  method](papers/differential-sequence-mining-kinnebrew-2013.md) behind DDCI's behavioral detectors,
  [text replays + whether GPT can code from them](papers/text-replays-and-llm-coding.md), and
  [Physics Playground affect detectors](papers/physics-playground-kai-2015.md) — the open-ended
  environment closest to CLUE), same
  faithful/caveated format as the grounding folder.
- **[bibliography.md](bibliography.md)** — single source of truth for every reference in this compendium
  (master list with anchors + `Used by:` back-links), plus a ranked "to obtain" worklist for the
  paywalled/unverified subset and datasets. Each doc's References section links into it.
- **[working-notes.md](working-notes.md)** — meta: decisions, conventions, what's verified vs.
  scan-only, and open work for the next session.

*Conventions (matching [../grounding-llm-help/](../grounding-llm-help/)): verify citations before
asserting them; three honesty tiers (solid research / sound-but-unproven / genuine gap); flag domain
mismatches with our K-12 open-ended content plainly.*
