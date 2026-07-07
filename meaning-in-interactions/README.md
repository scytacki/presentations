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
   clickstreams).

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

Start with doc 1 for the problem, doc 2 for the method space, doc 3 for what education has tried, doc
4 for the build decision, doc 5 for *when* acting is warranted, doc 6 for the clustering-based
serialization eval.

- **[research-directions.md](research-directions.md)** — *the actionable companion.* What we could
  build or test, in two curated groups (well-defined-and-feasible vs. proposals to explore), each
  linking back to the analytical docs. The canonical home for "so what do we do next."

- **[papers/](papers/)** — per-paper deep-dives of the studies closest to our work
  ([DDCI](papers/ddci-baker-2024.md), [ClickSight](papers/clicksight-radmehr-2025.md),
  [pyKT](papers/pykt-liu-2022.md), the [differential-sequence-mining
  method](papers/differential-sequence-mining-kinnebrew-2013.md) behind DDCI's behavioral detectors,
  [text replays + whether GPT can code from them](papers/text-replays-and-llm-coding.md), and
  [Physics Playground affect detectors](papers/physics-playground-kai-2015.md) — the open-ended
  environment closest to CLUE), same
  faithful/caveated format as the grounding folder.
- **[papers-to-obtain.md](papers-to-obtain.md)** — paywalled or not-fully-verified papers worth
  getting, by priority. (DDCI's full text has since been obtained — it confirms the ~30s target.)
- **[working-notes.md](working-notes.md)** — meta: decisions, conventions, what's verified vs.
  scan-only, and open work for the next session.

*Conventions (matching [../grounding-llm-help/](../grounding-llm-help/)): verify citations before
asserting them; three honesty tiers (solid research / sound-but-unproven / genuine gap); flag domain
mismatches with our K-12 open-ended content plainly.*
