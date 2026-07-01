# Working Notes — Context, Decisions, and Open Work

*This file is **meta** — not part of the document series. It captures context for anyone (human or a
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
- **[papers/](papers/)** — deep-dives: DDCI, ClickSight, pyKT.
- **[papers-to-obtain.md](papers-to-obtain.md)** — retrieval list.

**Uncommitted as of this session** — convention (matching grounding-llm-help): work stays
uncommitted until the user explicitly asks to commit.

---

## Key decisions (don't relitigate without reason)

- **Four docs: problem (straw man) / techniques / edtech / AI-question.** Chosen with the user:
  techniques-first *and* edtech-centered were both wanted, so they became two separate docs; the
  RNN→transformer / pretrained-vs-trained analysis earned its own (fourth) doc rather than being a
  section.
- **The problem doc is deliberately a straw man.** The user said the motivation is broader than the
  detector use case ("we have other use cases that require identifying patterns") and that they'll
  review and expand it. So doc 1 centers the detector case but leaves **[OTHER USE CASE]** slots
  (candidates offered: post-hoc corpus analysis, teacher-facing formative signal, product/UX
  evaluation). **Expect doc 1 to be rewritten** once the user names the real other cases — and the
  center of gravity may shift from "detector" to "the general pattern-finding problem."
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
  Forecasting Live Chat Intent (2408.04668), Joseph digital-frustration preprint (2512.20438).
- **Corrected during verification:** the "intent from browsing" paper is **"Forecasting Live Chat
  Intent from Browsing History"** (Yoon et al., CIKM 2024) and uses an **unnamed LLM, not GPT-3.5**
  (the scan overstated it). The frustration preprint is **single-author (Jibin Joseph)**; its Coveo
  dataset attribution was **not** confirmed — docs say "a real e-commerce dataset (~305k sessions)."
- **Scan-only, NOT independently re-verified (flagged in-doc and in papers-to-obtain):** AKT (KDD
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
