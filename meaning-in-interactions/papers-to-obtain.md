# Papers to Obtain

Papers that are **paywalled or that I could not fully verify**, which matter enough to be worth you
getting a copy. Grouped by priority. When you retrieve one, the natural next step is to fold its
verified details into the relevant doc (and, for the top ones, a `papers/` deep-dive) and remove the
"paywalled / not accessed" hedges.

## ✅ Obtained — no longer needed

- **Baker et al. (2024), Detector-Driven Classroom Interviewing (DDCI).** *Educational Technology
  Research and Development*, 72(5), 2841–2863. DOI 10.1007/s11423-023-10324-y. **Full text read** —
  local copy at `~/Downloads/DetectorDrivenClassInterviewing.pdf`; deep-dive in
  [papers/ddci-baker-2024.md](papers/ddci-baker-2024.md). Resolved the open questions: the
  **~30-second latency IS stated in the paper** ("delay almost under 30 s"); demonstrated on the
  open-ended **Betty's Brain**; detectors are logistic/step regression + sequential pattern mining;
  evaluated via four illustrative vignettes (no detector accuracy figures reported).

## Priority 2 — strengthen the EdTech-landscape claims

- **Beck & Gong (2013), Wheel-Spinning: Students Who Fail to Master a Skill.** AIED 2013. DOI
  10.1007/978-3-642-39112-5_44. **Paywalled (Springer LNCS).** *Nice-to-have, low priority:* the
  *origin* of the wheel-spinning construct. Its concept and operational definition are already
  covered by the **in-hand Kai et al. (2018)** full text (which cites and restates Beck & Gong), so
  obtain this only if we want to cite the origin directly rather than via Kai et al.
- **D'Mello & Graesser (2012), Dynamics of affective states during complex learning.** *Learning and
  Instruction*, 22(2), 145–157. DOI 10.1016/j.learninstruc.2011.10.001. **Paywalled (Elsevier).**
  *Why:* the confusion→frustration→boredom model we lean on for "meaning is a *transition*."
- **Rodrigo & Baker (2011), incidence/persistence of learning behaviors across tutors vs. games.**
  Author copy appears open (upenn.edu) but not independently re-fetched. *Why:* source of the
  gaming/affect incidence numbers (Aplusix vs Math Blaster).
- **"Deep learning + transfer learning" early detectors of stopout & wheel-spinning** (NSF PAR
  record 10095357). *Why:* the neural exception in struggle-detection; confirm authors/venue/method
  before citing as "the field is starting to go neural here."

## Priority 3 — techniques doc, foundational but paywalled

- **Srikant & Agrawal (1996), GSP.** EDBT 1996, LNCS 1057. DOI 10.1007/BFb0014140. **Paywalled.**
- **Pei, Han et al. (2001/2004), PrefixSpan.** ICDE 2001 / IEEE TKDE 2004. **Paywalled (IEEE).**
- **Geng & Hamilton (2006), Interestingness Measures: A Survey.** *ACM Computing Surveys* 38(3).
  DOI 10.1145/1132960.1132963. **Paywalled (ACM).** *Why:* the objective/subjective/semantic
  interestingness framing is conceptually central; worth reading in full.
- **Feild, Allan & Jones (2010), Predicting Searcher Frustration.** SIGIR 2010. DOI
  10.1145/1835449.1835458. **Paywalled (ACM).**
- **van der Aalst, Weijters & Maruster (2004), Workflow Mining / α-algorithm.** IEEE TKDE 16(9).
  **Paywalled (IEEE).**
- **Sakar et al. (2018), real-time purchasing-intention + LSTM abandonment.** *Neural Computing &
  Applications*. DOI 10.1007/s00521-018-3523-0. **Paywalled (Springer);** dataset open on UCI.

## Verify-before-relying (open access, but I did NOT independently re-verify)

These are on arXiv (so obtainable freely), but I cited them from the research scan without fetching
each one myself. Confirm title/authors/claims before leaning on them hard:

- **arXiv:2310.07820** — Gruver et al., "LLMs Are Zero-Shot Time Series Forecasters," NeurIPS 2023.
- **arXiv:2406.08660** — fine-tuned small models vs. zero-shot LLMs for classification (title/authors
  to confirm).
- **arXiv:2406.07467** — ensemble + RAG hybrid with a pretrained LLM for log anomaly detection
  (title/authors to confirm).
- **arXiv:1905.06874** — Chen et al., Behavior Sequence Transformer (BST). *(Cited; abstract not
  re-fetched.)*
- **KDD 2020 AKT** — Ghosh, Heffernan & Lan; author PDF open on umass.edu. *(Key quotes verified via
  the scan's extraction; original not re-fetched by me.)*

## Already verified — no need to obtain

For reference, these were **directly verified** by fetching the source and need no further chase:
**DDCI** *(full text read locally)*, **ClickSight** (arXiv:2505.15410), **pyKT**
(arXiv:2206.11460), **DKT** (arXiv:1506.05908), **SAKT** (arXiv:1907.06837), **SAINT**
(arXiv:2002.07033), **SASRec** (arXiv:1808.09781), **LogLLM** (arXiv:2411.08561),
**Forecasting Live Chat Intent** (arXiv:2408.04668), **Joseph digital-frustration preprint**
(arXiv:2512.20438), **Owen et al. 2019 wheel-spinning** (ERIC ED599202, open), **Kai et al. 2018
JEDM** (open — *full text now in hand*, local copy `~/Downloads/olney,+kai-1.0.1.pdf`; confirms the
ASSISTments decision-tree method over bottom-out-hint and inter-problem-delay features),
**Jiang et al. 2018** "Expert Feature-Engineering vs. Deep Neural Networks" (AIED 2018 — open PDF
`learninganalytics.upenn.edu/ryanbaker/jiang-aied2018.pdf`, *full text read*; the construction paper
behind DDCI's affect detectors — BROMP labels, 249 features, feature-engineering-vs-DNN tradeoff),
**BROMP 2.0 manual** (`learninganalytics.upenn.edu/ryanbaker/BROMP.pdf`, *read*; momentary
time-sampling, ~20 s per student, states coded not transitions).
