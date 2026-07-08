# Working Notes — Context, Decisions, and Open Work

*This file is **meta** — not part of this compendium. It captures context for anyone (human or a
new AI session) picking this work up: the decisions behind the docs, the conventions they follow,
what is verified vs. scan-only, and what's still open. The actual content is in the four companion
docs and `papers/`.*

---

## What's here and its status

A set of documents on **finding meaning in user interactions**, for the Concord-style ed-tech
research group (same audience as [../grounding-llm-help/](../grounding-llm-help/) and
[../audiences/ed-tech-research-group.md](../audiences/ed-tech-research-group.md)).

- **[finding-meaning-problem.md](finding-meaning-problem.md)** — the problem, written as a **straw
  man** (built from discussion + the prior PR, not from literature). Has explicit **[OTHER USE
  CASE]** placeholders.
- **[techniques.md](techniques.md)** — domain-agnostic methods (web analytics / marketing / UX).
- **[edtech-landscape.md](edtech-landscape.md)** — what education actually built.
- **[ai-architecture-question.md](ai-architecture-question.md)** — transformers-vs-RNN and
  pretrained-vs-trained.
- **[when-to-intervene.md](when-to-intervene.md)** — the intervention-timing literature upstream of
  detection (impasses / contingency / assistance dilemma / expert-tutor cues / productive confusion /
  application-tutor struggle detection — Lumière, rage-clicks, GUIDE / the UI-affordance↔learning-goal
  continuum vocabulary — semantic-vs-articulatory distance, instrumental genesis, cognitive load /
  well-vs-ill-structured & design tutoring — Jonassen, Schön's reflective studio, ITS-for-ill-defined /
  §9 give-vs-ask-vs-invite initiative — Razzaq & Heffernan, help avoidance, mixed-initiative).
  Synthesis only, no new empirical claim; checks the "intervene on affect change" hypothesis. **Central
  claim (per the user): our materials span a spectrum — gated/well-structured (wildfire sim with per-page
  goals) to open-ended/ill-structured (open document-building) — and the two need *different* intervention
  machinery; a solution for one likely won't transfer.** **Second reframing (per the user): on-request help
  (the wildfire default) shifts the meaning-detection target from "detect when to intervene" to "advise
  well when asked"; only detecting *help-avoidance* (stuck-and-not-asking) loops back to impasse detection.**
  Note: the Wood/Bruner/Ross block study is *well*-structured (fixed pyramid, 3–5 yr olds), not open-ended.
- **[clustering-representation-eval.md](clustering-representation-eval.md)** — **sixth core
  (analytical) doc**, and the method deep-dive behind research-direction **3.8**: rank serializations
  by embedding→cluster→score-vs-known-groups (external cluster validation / MTEB Clustering task /
  TabLLM serialization-sensitivity / GCD-NCD discovery). Also §7 checks whether the core proxy
  (embedding separability ≈ LLM-readability) holds — intrinsic≠extrinsic (Schnabel 2015) and
  decodable≠used (Hewitt & Liang 2019) are canonical; geometry-predicts-embedding-task-performance has
  fresh positive evidence (Myntti et al. 2026, arXiv:2605.22202); the *generative*-LLM bridge is
  untested. Verdict: components all canonical, the serialization-ranking + interaction-log application
  is a gap. All load-bearing citations verified (GCD, NCD, Hewitt & Liang fetched directly this
  session; metrics/MTEB/TabLLM/Sclar/ClusterLLM verified 3–0 by the workflow; Schnabel &
  Myntti/2602.04212 abstract-level, flagged). Added at the user's request July 7 2026.
- **[querying-the-log.md](querying-the-log.md)** — **seventh core doc**, the **"query" half** of
  compact-and-query (companion to the "compact/represent" clustering doc). Two families: agentic
  **tool-use over the log** (search/aggregate/state-at/diff; the `aggregate` tool is the
  numeric-reasoning offload) and **pre-organizing the stream** into a **multi-scale index** (the
  user's cross-window / different-time-scale problem). Synthesis: A's tools query B's index — a
  coarse-to-fine agentic loop = the concrete compact-and-query machinery §3.4 needs. **STATUS:
  researched July 7 2026** (deep-research workflow: 6 angles → 26 sources → 25 claims 3-vote-verified,
  23 confirmed; plus direct fetches for the education papers). **Headline = a domain asymmetry:**
  Family A (agentic tool-use / RAG over logs) is real and active but almost all **system/AIOps/security
  logs** (RCAgent CIKM 2024 — its Observation KV-Store is the cleanest "reference lengthy data without
  expanding prompt context"; Microsoft ReAct RCA FSE 2024; FlexLog; 3 surveys). Family B multi-scale
  motif (Matrix Profile / VALMOD / MAD) is all **sensor time series**. The **K-12 interaction-log**
  composition is a **"did not find," NOT a proven void** (the adversarial pass refuted 0–3 a stronger
  "education gap confirmed" claim). Education/user-interaction adjacencies that DO exist: Brinton 2015
  (MOOC clickstream SPM), DMSW 2505.11119 (multiscale windows), MSDP CIKM 2023 (multi-scale
  user-behavior embedding — answers the Q6 sub-question), LC-RAG 2505.17238 (logs as retrieval
  context). **Two corrections made:** (1) arXiv:2406.07467 is **FlexLog / "LLM meets ML…"**, not an
  "ensemble+RAG" title — bibliography entry corrected and its anchor renamed to `flex-log-2024`
  (all citing docs updated); (2) LogLLM says "impractical … large window size" but does NOT
  verbatim give the "whole sequence must go in the prompt" reason — softened here **and in
  [ai-architecture-question.md](ai-architecture-question.md) §2** (its "ensemble + RAG hybrid"
  phrasing for 2406.07467 was already accurate — now links the corrected FlexLog entry). 16 new
  bibliography entries added (all `Used by: querying-the-log`).
- **[research-directions.md](research-directions.md)** — actionable directions (Group 1
  curated/feasible/**high value**; Group 2 curated/feasible/**questionable value**; Group 3
  proposals; Rejected placeholder). The canonical "what next."
- **[papers/](papers/)** — deep-dives: DDCI, ClickSight, pyKT, differential sequence mining, text
  replays + LLM coding, Physics Playground affect detectors (Kai 2015).
- **[bibliography.md](bibliography.md)** — **single source of truth for every reference in this
  compendium** (renamed from the old `papers-to-obtain.md`). A **Master list** (alphabetical; each entry
  has a stable `<a id="firstauthor-year">` anchor + a `Used by:` back-link list) followed by a
  **To obtain — ranked** acquisition worklist (paywalled/unverified-but-wanted subset + Datasets),
  which links into the master rather than repeating citations. Every doc's `## References` section is
  now a compact list of `[label](bibliography.md#<key>)` pointers into this file — details live here,
  not in the docs. When adding a reference: add the anchored master entry (with `Used by:`) first,
  then the pointer in each citing doc.

**Committed to branch `meaning-in-interactions`** (not pushed). Earlier convention was
uncommitted-until-asked; the user has since been committing incrementally on this branch.

---

## Hard rules (do not violate)

*Standing instructions from the user. These override default behavior.*

1. **Do NOT add items to Group 1 *or* Group 2 of [research-directions.md](research-directions.md)
   without the user's explicit approval.** Both are the user's **curated** lists — Group 1 is
   curated/feasible/**high value**, Group 2 is curated/feasible/**questionable value** — and reflect
   *their* judgment, not ours. You may freely propose directions in **Group 3** (and argue for
   promotion), but only the user moves something into Group 1 or 2. When in doubt, put it in Group 3
   and flag it. *(The three-group structure — high-value vs. questionable-value curated, plus the
   uncurated staging area — was set by the user; earlier the doc had only two groups.)*
2. **`scotts-notes.md` is the user's personal notes — NEVER edit it.** You may **read** it for
   context and **refer to it in conversation**, but: (a) never modify it, and (b) **no other document
   in this folder may link to or reference it** (it is not part of this compendium). This
   working-notes mention exists only to record the rule.

---

## Key decisions (don't relitigate without reason)

- **Four core docs: problem (straw man) / techniques / edtech / AI-question.** Chosen with the user:
  techniques-first *and* edtech-centered were both wanted, so they became two separate docs; the
  RNN→transformer / pretrained-vs-trained analysis earned its own (fourth) doc rather than being a
  section. **A fifth analytical doc, [when-to-intervene.md](when-to-intervene.md), was later added at
  the user's request** — the intervention-timing literature (impasses, contingency, assistance
  dilemma, expert-tutor cues, productive confusion) that is *upstream* of the detection question. **A
  sixth core doc, [clustering-representation-eval.md](clustering-representation-eval.md), was added
  July 2026 at the user's request** — the method deep-dive behind research-direction 3.8 (evaluate
  serializations by clustering their embeddings against known groups). Promoted from a
  method-bullet to a numbered core doc by the user.
- **The problem doc is deliberately a straw man.** The motivation is broader than the detector case;
  the user is naming use cases incrementally. **Now specified: (1)** real-time detection for
  researcher interviews, **(2)** real-time feedback to students — Variant A rule-system+states
  (current; = the detector approach but with a much larger detection/coverage burden), Variant B a
  GenAI feedback agent (not yet built; less annoying, easier to extend, but non-deterministic and
  not auditable-by-rules). Still-open candidates: post-hoc corpus analysis, teacher-facing formative
  signal (DDCI-backed), product/UX evaluation.
- **The unifying through-line the user surfaced:** the **compact-and-query loop** over interaction
  logs (summarize vs. read-in-full, alternative representations, a query tool over the raw stream)
  is the *same* work whether a researcher is building a detector or a GenAI agent is reading a
  student. This is now the intended center of gravity for the eventual rewrite, and it ties to
  `techniques.md` (sessionization/compression), `ai-architecture-question.md`
  (serialization/long-context/memory), and grounding-llm-help's `grounding-in-practice.md`. **The
  feedback use case also bridges to the [grounding-llm-help](../grounding-llm-help/) series** (its
  GenAI variant *is* that series' LLM helper, seen from the interaction-reading side).
- **Folder name `meaning-in-interactions`** chosen by the user over alternatives.
- **No formal spec file.** The user chose to skip the brainstorming skill's `docs/superpowers/specs`
  step and go straight to research + drafting (the grounding-llm-help set was produced the same
  way; README/working-notes act as the living spec).
- **Framing anchor = the prior detector PR** (CLUE-330,
  https://github.com/concord-consortium/collaborative-learning/pull/2742). The three detector types
  in that spec (rule-based / ML / LLM) are used as a spine that maps onto the technique space.

---

## The single most important finding

**The paper the detector PR "builds on" is identified:** Baker et al. (2024), *Detector-Driven
Classroom Interviewing* (DDCI), ETR&D 72(5):2841–2863, DOI 10.1007/s11423-023-10324-y. See
[papers/ddci-baker-2024.md](papers/ddci-baker-2024.md).

**RESOLVED (full text obtained):** the user had a local copy
(`~/Downloads/DetectorDrivenClassInterviewing.pdf`); it has been read in full. The spec's
**~30-second latency target IS stated in DDCI** — "detect, almost in real-time (with a delay almost
under 30 s)" (p.2843). Other confirmed facts: demonstrated on the **open-ended Betty's Brain**
(concept-mapping, middle-school science); detectors are **logistic/step-regression** affect
detectors (boredom/frustration/confusion/engaged/delight, 20-s grain) + **sequential-pattern-mining**
behavior detectors; interviewer app is **Quick Red Fox (QRF)**; evaluation is **four illustrative
vignettes**, not a benchmark (no detector precision/recall reported); and the paper **explicitly
proposes a teacher-facing use** of the same infrastructure. See
[papers/ddci-baker-2024.md](papers/ddci-baker-2024.md).

## The honest through-line of the research

1. **Education's built meaning-detectors are mostly feature-engineering + classical ML** (decision
   trees / random forests) for affect, gaming, and struggle. The hypothesis ("predates modern AI")
   is **correct for these.**
2. **Knowledge tracing is the neural exception** — but it *undercuts* the naive "transformers win"
   inference: SAKT failed to beat the RNN-based DKT; AKT/SAINT win only with domain-specific design;
   **pyKT (verified) shows gains over the 2015 LSTM are "minimal" under leak-free evaluation.**
3. **Transformers do win on big non-education behavior data** (SASRec, BST).
4. **Pretrained-LLM-on-raw-logs is live in education now** (ClickSight, verified) but is
   *interpretation*, not yet reliable *detection*; fine-tuned specialists still beat zero-shot LLMs
   on well-defined tasks (LogLLM etc.).
5. Net: the evidence backs the spec's **"LLM first (flexible, zero-label), train/fine-tune as an
   optimization"** progression — more than it backs "just switch to transformers."

---

## Verification status — READ BEFORE EXTENDING ANY CLAIM

The deep-research workflow hit **heavy API rate-limiting during its verify/synthesize phases**, so
its automatic verification is incomplete. Many claims it bucketed as "refuted" actually have `0-0`
votes = **never verified (abstained), not disproven.** Do not treat that workflow's "refuted" list
as false.

To compensate, I **directly fetched and verified** the load-bearing citations. Confidence tiers:

- **Directly verified by me (high confidence):** DDCI *(full 23-page text read from local copy —
  30s target, Betty's Brain, detectors, QRF, vignettes all confirmed)*, ClickSight (2505.15410),
  pyKT (2206.11460, key quotes from abstract), DKT
  (1506.05908), SAKT (1907.06837), SAINT (2002.07033), SASRec (1808.09781), LogLLM (2411.08561),
  Forecasting Live Chat Intent (2408.04668), Joseph digital-frustration preprint (2512.20438),
  Physics Playground affect detectors *(Kai et al. 2015, EDM — full 8-page text read; 137 students,
  BROMP labels, interaction A′≈0.63 vs video 0.70; confirmed as primary study **P4** in the de Morais
  et al. 2023 review's Table 1)*.
- **Corrected during verification:** the "intent from browsing" paper is **"Forecasting Live Chat
  Intent from Browsing History"** (Yoon et al., CIKM 2024) and uses an **unnamed LLM, not GPT-3.5**
  (the scan overstated it). The frustration preprint is **single-author (Jibin Joseph)**; its Coveo
  dataset attribution was **not** confirmed — docs say "a real e-commerce dataset (~305k sessions)."
  **Also (July 2026):** research-directions item 2.1 first asserted "nobody has measured how well a
  human re-coding video+screen matches a live BROMP observer" — that was **unverified and overstated**;
  a web search found the *method* is standard (D'Mello & Graesser **retrospective affect judgment
  protocol**), ground-truth-source comparison is active (**"Says Who?"**, Zambrano/Ocumpaugh/Hutt,
  EDM 2024, added to edtech refs), and BROMP is live *by design* to avoid video's context loss. The
  claim was softened to "did not find that specific remote-vs-live *human* A′ comparison." Lesson:
  don't ship "nobody has done X" without a search.
- **Scan-only, NOT independently re-verified (flagged in-doc and in bibliography):** AKT (KDD
  2020) quotes, BST (1905.06874), Gruver time-series (2310.07820), the classification comparison
  (2406.08660), the ensemble+RAG log paper (2406.07467). Verify before leaning on these hard.
- **Paywalled, abstract-level only:** DDCI, Beck & Gong 2013, D'Mello & Graesser 2012, GSP,
  PrefixSpan, Geng & Hamilton 2006, Feild et al. 2010, van der Aalst 2004, Sakar et al. 2018.

**Raw research outputs** (verified-claim lists, per-source extractions) lived in this session's temp
files at `/private/tmp/claude-501/.../tasks/{ws76difu5,w1l58zpoo,wd6kcxk8n}.output` — **session-scoped,
not in the repo, and gone in a new session.** The docs carry the conclusions; re-run targeted
verification if you reopen a claim.

---

## Conventions these docs follow (match them)

- **Verify before cite;** never assert a paper from memory. Mark accessibility (open / paywalled) and
  note when a citation is scan-only.
- **Three honesty tiers:** *solid research* / *sound-but-unproven* / *genuine gap*.
- **Flag domain mismatch plainly:** almost none of this literature was built for **K-12 open-ended
  classroom software like CLUE** (drawing, tiles, open response). Most is intelligent tutors /
  skill-builders with clean correctness signals. Say so whenever leaning on those results.
- **Per-paper deep-dive structure:** problem → what it does → what it ran on → how evaluated → key
  caveat → why it matters for us → limitations → a *Verification* note.

---

## Open work / where to go next

- **User to fill in the [OTHER USE CASE] slots** in doc 1, then doc 1 gets rewritten around the
  general problem. This was the user's stated plan.
- ~~Obtain DDCI full text and settle the ~30s latency question~~ **Done** — read in full; 30s
  confirmed, deep-dive updated.
- **Re-verify the scan-only citations** (AKT, BST, 2310.07820, 2406.08660, 2406.07467) — quick arXiv
  fetches — and remove the "not re-verified" hedges.
- **Possible deeper dives not yet done:** process mining for "unsupported things users try to do"
  (the UX use case); a foundation-model-on-unlabeled-logs angle (self-supervised pretraining) as the
  way around the labeling bottleneck; the "insight/aha detection" gap.
- **Consider a per-paper deep-dive for the wheel-spinning / affect classics** if the edtech doc gets
  presented on its own.
- **Actionable directions now live in [research-directions.md](research-directions.md)** (three
  groups: curated/high-value, curated/questionable-value, and uncurated proposals). **Group 1**
  (high value) currently holds one item — the **cross-application GenAI affect detector** — which the
  user considers well-scoped and now data-feasible (they have relationships with **both ASSISTments
  and the Betty's Brain researchers**, so datasets are likely obtainable if the IRBs permit).
  **Group 2** (questionable value) holds one item — measuring the **human upper-bound for "remote"
  affect labeling** by re-coding the Physics Playground video (does a human with video+screen match
  in-person BROMP? decides whether an AI remote-labeler is worth building at all). **Group 3**
  (proposals) holds the serialization study, an LLM CLUE detector, text-replay labeling of CLUE data,
  the GenAI feedback agent, a foundation-model-on-logs idea, a **which-intervention-trigger-is-detectable**
  scoping study (3.6), and the latent other-use-cases. A future
  **Rejected ideas** section will record what we drop and why. Don't re-scatter actionable directions
  back into the analytical docs — add them there.
