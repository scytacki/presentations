# Techniques for Finding Meaning in Interaction Streams

This is the domain-agnostic companion to [edtech-landscape.md](edtech-landscape.md) (what education
specifically did) and [ai-architecture-question.md](ai-architecture-question.md) (the modern-AI
argument). It surveys the **methods** for pulling meaning out of a stream of user-interaction
events, independent of field — leaning on the older and much richer literatures of **web analytics,
marketing, and UX/HCI**, which have been mining behavior logs for "why did the user do that" since
the late 1990s. The problem framing is in
[finding-meaning-problem.md](finding-meaning-problem.md).

The organizing idea: these fields converged on a **pipeline** — clean the stream into sessions,
discover patterns, then decide which patterns are *interesting* — and on a **succession of pattern
models** running from association rules through Markov chains, sequential-pattern mining, process
models, and finally neural sequence models. Each rung is still in use; newer does not mean the older
ones were abandoned.

> **Honesty tiers.** *Solid* = foundational/replicated. *Sound-but-unproven* = one or few studies.
> *Gap* = thin evidence. Accessibility is marked; paywalled-but-important works are in
> [bibliography.md](bibliography.md).

---

## 1. The canonical pipeline (web-usage mining)

The reference frame comes from **web-usage mining**, whose standard survey (Srivastava, Cooley,
Deshpande & Tan, 2000) defines three phases that every later system echoes:

1. **Preprocessing** — turn raw log lines into clean user sessions. The field-standard
   **sessionization** heuristic is a **30-minute inactivity timeout**: a gap longer than that ends
   the session. *(Solid — this is a genuine convention, and a useful default for us.)*
2. **Pattern discovery** — the analytic core (next sections).
3. **Pattern analysis** — filter the discovered patterns down to the *interesting* ones (see §6).

The survey enumerates the pattern-discovery toolbox that everything else refines: **statistical
analysis, association rules (Apriori), clustering (of users and of pages), classification,
sequential-pattern mining, and dependency modeling.** *(Solid — the canonical taxonomy.)*

For us, the value is the shape: **sessionize → discover → rank by interestingness.** Our detector
problem is really the "discover + rank in real time" end of this pipeline.

## 2. Clickstream & sequence-pattern mining

The heart of "meaning in order-of-actions" is **sequential pattern mining** — finding frequent
ordered subsequences across many users' sessions:

- **GSP (Generalized Sequential Patterns)** — Srikant & Agrawal (1996). An Apriori-style algorithm
  that adds *time constraints* (min/max gap between elements) and a *sliding window*. Foundational.
  **Paywalled** (Springer LNCS). *(Solid.)*
- **PrefixSpan** — Pei, Han et al. (2001/2004). Prefix-projected pattern growth; mines the full set
  of sequential patterns while avoiding GSP's candidate-generation blowup, and outperformed GSP and
  FreeSpan on large sequence databases. **Paywalled** (IEEE TKDE). *(Solid — still a standard.)*

Alongside frequent-sequence mining sits **probabilistic navigation modeling**:

- **Markov models of navigation** — model the next action as a function of recent actions. Plain
  first-order Markov underfits; high-order Markov explodes in state space. **Selective Markov
  Models** (Deshpande & Karypis, SDM 2001) prune intelligently (support / confidence / error
  pruning), reportedly cutting up to ~90% of states while *improving* accuracy. **Open access**
  (SIAM PDF). *(Sound — well-regarded, one primary source here.)*
- **Alignment + clustering + Markov pipelines** — e.g. Significant Usage Patterns (2006): align
  sessions (Needleman-Wunsch), cluster them, fit a Markov model per cluster, extract
  high-probability paths. A template for "find the typical trajectories." **Paywalled.**
  *(Sound-but-unproven.)*

**Relevance to our detectors:** GSP/PrefixSpan and Markov models are essentially the *rule-based /
statistical* detector's toolkit — the way you'd formalize "3+ deletes then a restart" or "the
typical path through this activity, and who deviates."

## 3. Purchase intent and conversion — the marketing lineage

The commercial motivation the problem doc anticipated is real and long-running: predict, from
browsing behavior, whether a session will **convert** (buy) or **abandon**, and act on it.

- A representative system (Sakar et al., *Neural Computing & Applications*, 2018) runs **two
  real-time modules**: an MLP predicting purchasing intent, and an **LSTM** over the sequential
  clickstream predicting likelihood of abandonment. Notable as an early "sequence model on
  clickstream, in real time" design. **Paywalled** (Springer); the associated *Online Shoppers
  Purchasing Intention* dataset is open on UCI. *(Sound-but-unproven — one primary source.)*

The lesson for us is less the specific model than the **framing**: marketing treats the interaction
stream as a live signal of *latent intent* and optimizes latency-to-action — structurally the same
as flagging an "interesting" student moment for a timely interview.

## 4. UX/HCI — judging a UI and reading frustration

This is the branch aimed at *"is this feature helping, and where are users struggling?"* — closest
to the product/UX use case in the problem doc.

- **Frustration from interaction logs.** "Predicting Searcher Frustration" (Feild, Allan & Jones,
  SIGIR 2010) is the landmark: infer user frustration during hard search tasks from behavioral
  signals (plus, in their study, physical sensors). Establishes that **frustration is detectable
  from interaction behavior**, not just surveys. **Paywalled** (ACM). *(Solid for the concept.)*
- **Rage-clicks / heuristic negative-experience signals.** Modern web analytics operationalize
  frustration with simple behavioral heuristics — *rage clicks* (rapid repeated clicks on one spot),
  *U-turns* (immediate back-navigation), *cart churn*, *search struggle*, *dead clicks*. A recent
  preprint (Joseph, 2025, arXiv:2512.20438) uses exactly five such rules to label sessions and then
  trains an **LSTM** to predict frustration from the first ~20–30 events of a real e-commerce
  clickstream (~305k sessions), reporting ~91% accuracy / .97 AUC. Useful as a **labeling-scheme**
  template; treat the metrics cautiously (single-author preprint, dataset provenance unconfirmed).
  **Open access (arXiv).** *(Sound-but-unproven.)*
- **Funnel / drop-off and task-success inference.** The everyday industrial methods — conversion
  funnels, cohort retention, A/B testing on interaction metrics — are the bluntest form of "is this
  UI helping." They are pattern-*counting*, not pattern-*discovery*, and mostly live in tooling
  rather than papers; worth naming so we don't reinvent them. *(Solid as practice; light on
  citations.)*

## 5. Process mining and anomaly detection — the "does this match the intended flow?" branch

Two more general families worth knowing:

- **Process mining** — reconstruct a *process model* (e.g. a workflow/Petri net) purely from the
  ordering in event logs, then check how well real traces *conform* to it. The origin is van der
  Aalst, Weijters & Maruster's workflow-mining / **α-algorithm** (IEEE TKDE, 2004). This is the
  formal version of "what is the intended path through the activity, and where do students deviate?"
  — directly relevant to detecting *unsupported* things a user is trying to do. **Paywalled**
  (IEEE). *(Solid — founds a whole field.)*
- **Anomaly / novelty detection on event streams** — treat "interesting" as "statistically unusual
  relative to a learned normal." This is the operational backbone of log-anomaly detection (see
  [ai-architecture-question.md](ai-architecture-question.md) for the neural versions like LogBERT /
  LogLLM). *(Solid as a paradigm.)*

## 6. Operationalizing "interestingness" — the part everyone underestimates

The hardest conceptual problem — and the one most relevant to a detector that must decide *what to
surface* — is defining **interestingness** itself. The standard reference is **Geng & Hamilton's
survey (ACM Computing Surveys, 2006)**, which partitions interestingness measures into three kinds:

- **Objective** — from the data/statistics alone (support, confidence, unusualness).
- **Subjective** — relative to the *user's prior beliefs* (surprising = contradicts what I expected).
- **Semantic** — based on *utility / domain meaning* (interesting = actionable for my goal).

**Paywalled** (ACM). *(Solid — the canonical framing.)*

This maps almost one-to-one onto our situation: a *rule/ML* detector encodes **objective**
interestingness; a *researcher's evolving intent* is **subjective**; and "worth interrupting class
to interview" is **semantic/utility**. It's a useful vocabulary for arguing about *which kind* of
interestingness each detector type can actually capture — and a clue that the LLM detector's real
advantage may be reaching the **subjective/semantic** kinds that statistics alone miss.

## 7. The neural progression for behavior sequences

Outside education, the move from RNNs to transformers on **user-behavior sequences** is more
decisively in the transformer's favor than it is in knowledge tracing (contrast
[edtech-landscape.md](edtech-landscape.md) §3):

- **RNN/LSTM/GRU** — the 2015–2018 default for session/behavior sequences (e.g. the LSTM abandonment
  model in §3).
- **Self-attention / transformers** —
  - **SASRec** (Kang & McAuley, ICDM 2018, arXiv:1808.09781): a self-attention sequential
    recommender that "outperforms various state-of-the-art sequential models (MC/CNN/RNN-based)."
    **Open access (arXiv).** *(Solid.)*
  - **Behavior Sequence Transformer (BST)** (Chen et al., Alibaba, 2019, arXiv:1905.06874): applies
    the Transformer to a user's clicked-item sequence for click-through-rate prediction; beat
    Wide&Deep and Deep Interest Network on a Taobao production dataset (offline AUC ~0.789 vs
    ~0.773/0.787). **Open access (arXiv).** *(Solid — production-scale.)*
- **LLMs + behavior** — the newest branch, e.g. "Forecasting Live Chat Intent from Browsing History"
  (Yoon et al., CIKM 2024, arXiv:2408.04668): a **two-stage** design that fine-tunes a transformer
  to classify the browsing sequence into a coarse intent, then hands off to an **LLM** to generate a
  fine-grained natural-language intent. A concrete pattern for "structured detector + LLM
  interpreter." **Open access (arXiv).** *(Sound-but-unproven — recent.)*

Note the small honest caveat: several "transformer beats X%" figures are qualitative in the
abstracts; specific margins should be read from the papers before quoting. The direction, though, is
consistent: **on large-scale behavior data, attention/transformers reliably beat RNNs** — the
opposite of the muddier knowledge-tracing picture, and the tension the AI doc has to resolve.

## 8. Mapping techniques to our detector types

| Detector type (from the spec) | Techniques that implement it |
|---|---|
| **Rule-based** | sessionization; association rules; GSP/PrefixSpan sequential patterns; Markov navigation models; process-mining conformance; heuristic negative-experience rules (rage-clicks, U-turns) |
| **ML model** | feature-engineering + classical classifiers (trees/forests); LSTM/GRU sequence models; SASRec/BST-style behavior transformers; anomaly detection |
| **LLM** | prompted frontier models over serialized event streams (ClickSight-style); structured-detector-plus-LLM-interpreter (live-chat-intent style) |

And the **interestingness** vocabulary (§6) cuts across all three: it's the question of *what each
detector is even trying to surface* — objective anomaly, subjective surprise, or semantic utility.

---

## References

*Full details + accessibility in [the series bibliography](bibliography.md).*

- [Chen et al. (2019) — BST](bibliography.md#chen-2019) 🟢
- [Deshpande & Karypis (2001) — Selective Markov Models](bibliography.md#deshpande-2001) 🟢
- [Feild, Allan & Jones (2010) — Predicting Searcher Frustration](bibliography.md#feild-2010) 🔒
- [Geng & Hamilton (2006) — Interestingness Measures survey](bibliography.md#geng-2006) 🔒
- [Joseph (2025) — Digital Frustration from Clickstream](bibliography.md#joseph-2025) 🟢
- [Kang & McAuley (2018) — SASRec](bibliography.md#kang-2018) 🟢
- [Pei, Han et al. (2001/2004) — PrefixSpan](bibliography.md#pei-2001) 🔒
- [Sakar et al. (2018) — purchasing intention + LSTM](bibliography.md#sakar-2018) 🔒
- [Srikant & Agrawal (1996) — GSP](bibliography.md#srikant-1996) 🔒
- [Srivastava et al. (2000) — Web Usage Mining](bibliography.md#srivastava-2000) 🟢
- [van der Aalst et al. (2004) — Workflow Mining / α-algorithm](bibliography.md#vanderaalst-2004) 🔒
- [Yoon et al. (2024) — Forecasting Live Chat Intent](bibliography.md#yoon-2024) 🟢
