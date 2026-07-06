# Finding Meaning in Student Interactions — What Education Has Actually Done

This is the EdTech-centered companion to [techniques.md](techniques.md) (the domain-agnostic
methods) and [ai-architecture-question.md](ai-architecture-question.md) (the forward-looking AI
argument). It answers a narrower question: **when education researchers have tried to find meaning
in student interaction logs, what did they build, and what did they find?** The problem framing is
in [finding-meaning-problem.md](finding-meaning-problem.md).

The short version, and the thing worth arguing about: for the *interpretive* constructs we care
about — struggle, disengagement, gaming, affect, "interesting moments" — the field's workhorse for
~20 years has been **hand-engineered features fed to classical machine-learning classifiers**
(decision trees, random forests), not sequence models. The one place neural sequence models took
over is **knowledge tracing**, which is a different (and narrower) task than the one we want. This
supports the working hypothesis in a specific way — and complicates it in another, because the
knowledge-tracing evidence shows transformers do *not* automatically win (see
[ai-architecture-question.md](ai-architecture-question.md)).

> **Honesty tiers used throughout.** *Solid research* = repeatedly replicated, or a landmark with
> strong evidence. *Sound-but-unproven* = a reasonable claim from one or few studies. *Gap* =
> something the literature has barely touched, especially for K-12 classroom software like ours.
> Citations are at the end; papers I could not access in full are marked and listed in
> [papers-to-obtain.md](papers-to-obtain.md).

---

## 1. The constructs the field decided were worth detecting

Before you can detect a meaningful moment you have to name it. Education converged, over the 2000s
and 2010s, on a small vocabulary of learner states worth pulling out of interaction data:

- **Affective states** — chiefly **boredom, confusion, frustration, and flow/engaged
  concentration.** D'Mello & Graesser's model of affect dynamics during complex learning is the
  usual theoretical anchor: an engaged learner hits an obstacle and becomes *confused*; if the
  confusion resolves, they return to engagement; if it persists, it curdles into *frustration* and
  eventually *boredom* (D'Mello & Graesser, 2012). This matters for us because it says the
  *meaningful* moment is often a **transition** between states, not a state itself. *(Solid
  research — widely cited theoretical model.)*

- **Disengaged behaviors**, especially **"gaming the system"** — systematic guessing and rapid
  hint-abuse to get through problems without thinking. Rodrigo & Baker and colleagues showed these
  behaviors are detectable and vary by software: gaming appeared in an intelligent tutor (Aplusix,
  ~1.6% of observations) but not at all in a drill game (Math Blaster, 0%), and — counter to the
  intuition that games are more engaging — the tutor produced *more* engaged concentration and
  *less* boredom than the game (Rodrigo & Baker, 2011). *(Solid research for the detection concept;
  the specific percentages are from one study.)*

- **Unproductive struggle**, formalized as **"wheel-spinning"** — a student who keeps practicing a
  skill but never reaches mastery (Beck & Gong, 2013). The operational definition that stuck:
  *attempting more than ~10 problems in a skill without achieving three consecutive correct
  answers.* The key finding is that **early failure predicts eventual failure** — a student's
  first several attempts already contain the signal — which is exactly what makes early, in-session
  detection plausible. *(Solid research — replicated concept.)*

The through-line: the field's "meaning" is mostly **negative signals worth intervening on** —
where a student is stuck, disengaged, or cheating the software. "Insight" / "aha-moments" as a
*detected* construct is comparatively **underdeveloped** — more discussed theoretically (via
confusion resolution) than built as a detector. *(Gap, and one relevant to our use cases.)*

## 2. How the detectors were actually built (the architecture answer)

This is the load-bearing section for the hypothesis. Across the affect / disengagement / struggle
literature, the dominant recipe is:

1. **Human labeling** of the target construct — often via structured field observation (e.g.
   trained observers coding students in short glances, the BROMP protocol behind much of Baker's
   affect work) or by replaying logs and coding them.
2. **Hand-engineered features** over the interaction log — counts, timings, hint requests,
   inter-problem delays, error streaks.
3. **A classical ML classifier** — decision trees, random forests, naive Bayes — trained on those
   features.

Concrete examples, all from student interaction logs:

- **Wheel-spinning in a math skill-builder** (Kai et al., 2018, JEDM): a **decision tree** over
  hand-crafted features (hint requests, bottom-out hints, inter-problem delays) to separate
  productive persistence from wheel-spinning. *(Solid — peer-reviewed, open access.)*
- **Wheel-spinning in an educational game** (Owen et al., 2019, EDM): tree-based models (CART,
  Random Forest, J48, naive Bayes) on log features; best cross-validated AUC ≈ .68. Notably
  **modest** accuracy — a reminder that these detectors are useful but far from perfect. *(Solid —
  open access via ERIC.)*
- **Help-need prediction** (from the same broad group): two **random-forest** classifiers to
  predict, at the start of a step, whether a student will need help — again feature-based, not a
  sequence model. *(Sound-but-unproven — single system.)*

There is a neural exception even here: at least one line of work has applied **deep learning with
transfer learning** to build early detectors of stopout (attrition) and wheel-spinning. So it is
not true that *nothing* in struggle-detection is neural — but it is the exception, and recent, not
the tradition. *(Sound-but-unproven — I did not fully verify this paper; see
[papers-to-obtain.md](papers-to-obtain.md).)*

**Bottom line for the hypothesis:** for the constructs we actually want ("interesting moments"),
the field's built systems are overwhelmingly *feature-engineering + classical ML*, which
**predate and do not use** the transformer architecture. The hypothesis is correct *here*. Where
it needs care is knowledge tracing, below.

## 2b. Getting labels *without* live observation — text replays (and can GPT do the coding?)

Step 1 above — human labeling — is the expensive bottleneck, and it normally means live BROMP
observation, which **we cannot do for past CLUE data.** There is a validated alternative directly
relevant to us: **text replays** (Baker, Corbett & Wagner, 2006). A coder labels a construct from a
purely textual **replay of the log** — action-by-action, generated automatically from standard log
files, with no face/voice/video. It sits at the bottom of a *fidelity spectrum* (live > video >
screen replay > text replay). Replaying **CLUE document changes** is essentially this regime. What
the validation showed (on *gaming*): text-replay inter-rater reliability is lower than live
(**κ ≈ 0.58 vs 0.83**) but per-student *aggregate* accuracy matches live (correlation with a
gold-standard detector **~0.57 vs ~0.54**), at **~5× the speed**, and — crucially — it runs
**retrospectively on existing logs** with no special study. The extension to multi-tag coding is
Baker & de Carvalho (2008). See [papers/text-replays-and-llm-coding.md](papers/text-replays-and-llm-coding.md).

**Two honest limits, both ours to reckon with:**
- **Affect is the hard case.** The 2006 paper explicitly flags that low-fidelity coding may *not*
  work for affect (bored/frustrated), which "may depend on subtleties... only capturable through
  higher-fidelity observation" — i.e. face/voice. Behavior/strategy codes from replay; affect
  probably needs more than a screen recording.
- **A GenAI coder is plausible but, so far, weak.** Maier & Baker (2025), *Can GPT Detect Gaming the
  System in Text Replays?*, fed the *same* text replays to GPT-3.5/GPT-4: **above chance but beaten
  by a classical model** (best GPT κ ≈ 0.17 vs a ported knowledge-engineered model's 0.26). Their
  diagnosis — "GPT's training data likely contains little that resembles text replays" — is direct
  evidence for the **serialization problem** in
  [ai-architecture-question.md](ai-architecture-question.md): human-readable ≠ LLM-legible. *(Solid;
  both papers read in full.)*

## 2c. Do affect detectors transfer to a *new* application? (Mostly no.)

A question central to us — we build many activities and simulations, so a detector that only works in
the one app it was trained on is far less valuable. The evidence says **feature-based affect
detectors are system-specific and do not transfer across applications.** A 2023 systematic review of
sensor-free affect detection (arXiv:2310.13711, full text read) finds that features are
"hand-crafted and system-specific" — the reviewed studies each engineer features to their own
environment (programming envs use code changes, math systems use quiz performance; **Physics
Playground**, one of that review's primary studies, uses springboards-drawn / trophies / ball-nudges
— see [papers/physics-playground-kai-2015.md](papers/physics-playground-kai-2015.md)) — that some
work studied generalizing
across student *populations* but **"cross-system transfer is barely addressed,"** that it documents
**no** successful cross-system transfer (calling instead for "a shared database of action logs and
emotion labels"), and that these detectors are **not yet good enough for real-time production use.**
So the intuition is right: *custom features + a regression fit to one app's affect labels* buys you a
detector for **that** app, not a portable one. *(Solid — verified review.)*

Two honest nuances: (a) **behavioral** detectors have transferred better — *gaming* detectors have
been ported across systems (e.g. Paquette et al., 2015, Cognitive Tutor → ASSISTments) and across
lessons — but that's behavior, not affect, and still took deliberate knowledge engineering;
(b) cross-**population** transfer (same app, different students/regions) is its own studied problem
("population validity," Ocumpaugh, Baker et al.) — distinct from cross-**application** transfer, which
is the harder, largely-unsolved one. This is exactly the gap a **GenAI** affect detector reading a
semantic log representation *might* close — an open opportunity examined in
[ai-architecture-question.md](ai-architecture-question.md), not an established result.

## 3. Knowledge tracing — the one place sequence models won (a different task)

Knowledge tracing (KT) predicts whether a student will get the *next* problem right, given their
history. It is the sub-field where neural sequence models genuinely displaced the classical
approach, so it is the natural evidence base for "would a transformer help us?" — with the caveat
that **KT is prediction of correctness, not detection of meaning**, so the transfer is by analogy.

The lineage:

- **Bayesian Knowledge Tracing (BKT)** — a two-state Hidden Markov Model per skill; the classical
  standard, still in production (e.g. MATHia declares a skill mastered at BKT probability > 0.95).
- **Deep Knowledge Tracing (DKT)** — Piech et al. (2015) replaced BKT's per-skill HMM with a single
  **RNN/LSTM** over the whole interaction sequence, reporting "substantial improvements," and — the
  part that matters for us — *without hand-encoding domain knowledge.* This is the RNN-era anchor
  the hypothesis names.
- **Attention / transformer KT** — SAKT (Pandey & Karypis, 2019) brought self-attention to KT;
  SAINT (Choi et al., 2020) and SAINT+ used a full transformer encoder-decoder; AKT (Ghosh et al.,
  2020) added a domain-specific *monotonic* attention with a forgetting decay.

The honest twist — developed fully in [ai-architecture-question.md](ai-architecture-question.md) —
is that **transformers did not cleanly win KT.** SAKT's own successors report it *failed to beat*
the RNN-based DKT; a careful benchmark (pyKT, Liu et al., 2022) found that once evaluation leakage
is removed, "the improvement of many [deep KT] approaches is minimal compared to the very first
DLKT model proposed by Piech et al." So KT both *supports* the "field moved to neural sequence
models" story and *undercuts* the "so transformers will obviously be better for us" inference.

## 4. The paper our detector work builds on: Detector-Driven Classroom Interviewing

The specific study behind the [detector-driven notifications
spec](https://github.com/concord-consortium/collaborative-learning/pull/2742) is:

> **Baker, R. S., Hutt, S., Bosch, N., Ocumpaugh, J., Biswas, G., Paquette, L., Andres, J. M. A.
> L., Nasiar, N., & Munshi, A. (2024). Detector-Driven Classroom Interviewing: Focusing
> Qualitative Researcher Time by Selecting Cases In Situ.** *Educational Technology Research and
> Development*, 72(5), 2841–2863. DOI 10.1007/s11423-023-10324-y.

The idea (**DDCI**): use the field's existing, scalable EDM/LA detectors — the affect, gaming, and
struggle detectors above — running **in real time** to *select which student to interview, right
now, while the moment is fresh.* The detectors don't have to be perfect; they have to be timely
enough to point a human at a worth-investigating moment. This is precisely the job our detector
service automates. It was demonstrated in **Betty's Brain**, an **open-ended** learning-by-teaching
environment (students build causal concept maps) — notably *not* a closed tutor, which makes it a
closer precedent for open-ended work like CLUE. *(Solid research — the method paper; full text
read.)*

**Three things worth carrying forward (full text now read):**

- **The "~30-second latency" target is confirmed — it's in the paper.** DDCI states its detectors
  "can detect, almost in real-time (with a delay almost under 30 s)" (p.2843). So the spec's 30s
  target *is* grounded here, not just our interpretation.
- **DDCI's own detectors are classical feature-based ML** — affect detectors (boredom, frustration,
  confusion, engaged concentration, delight) as **logistic/step regression** over hand-engineered
  features at a 20-second grain, plus **sequential-pattern-mining** behavior detectors. DDCI is a
  *method for using* detectors, not a new detection technique. Our opportunity (the AI doc) is to
  put **newer** detectors — LLM-prompted or transformer-based — inside the same DDCI method.
- **DDCI explicitly proposes a teacher-facing version** of the same infrastructure (notify a teacher
  of a disengaged student, a good SRL strategy to praise, or an ineffective one to scaffold) — which
  independently validates the "teacher-facing formative signal" use case in
  [finding-meaning-problem.md](finding-meaning-problem.md).

## 5. The leading edge: LLMs reading raw student clickstreams

The most on-target recent work — an LLM applied to *raw* student interaction logs, not to student
text — is:

> **Radmehr, B., Shved, E., Güreş, F. B., Singla, A., & Käser, T. (2025). ClickSight: Interpreting
> Student Clickstreams to Reveal Insights on Learning Strategies via LLMs.** AIED 2025.
> arXiv:2505.15410.

ClickSight feeds **raw clickstreams plus a list of candidate learning strategies** to an LLM and
has it generate textual interpretations of what the student was doing — an **in-context / prompted**
pipeline (no fine-tuning), evaluated across four prompting strategies with a self-refinement step.
This is close to the "LLM detector" in our spec: a frontier model, prompted with a researcher's
description, reading the interaction stream. *(Sound-but-unproven — a 2025 conference paper;
promising existence proof, not yet a robustness result. Open access on arXiv.)*

Its existence is the single best evidence that the "prompt a frontier LLM on raw logs" path is
**live and publishable right now** in education — and that the field has *not* yet saturated it,
which is the opening the hypothesis points at.

## 6. What this means for us

- **The hypothesis is right about the built systems**, wrong to assume the newest architecture is a
  free win. Education's *deployed* meaning-detectors are feature-based classical ML; its one neural
  stronghold (KT) shows attention/transformers need enough data or domain-specific design to beat an
  RNN, and even then the honest margin is small.
- **DDCI is the method, not the model.** It legitimizes the whole detector-interview program and
  gives us the citation — but it does not settle *how* to build the detector. That's open, and it's
  where a modern LLM approach (à la ClickSight) is a real, under-explored option.
- **"Interesting" and "insight" are under-served constructs.** Most of the literature detects
  *negative* states (stuck, bored, gaming). If our use cases include surfacing *positive* or
  *novel* moments, we are closer to the research frontier than to settled practice. *(Gap.)*
- **Most of this was built for intelligent tutors and skill-builders**, not K-12 open-ended software
  like CLUE — problems with well-defined answers and clean correctness signals. Our content
  (drawing / tiles / open response) is a **domain mismatch** with most of the cited work — flag it
  whenever we lean on these results. **The important exception is DDCI itself**, demonstrated in the
  open-ended Betty's Brain; so the *method* has an open-ended precedent even though most of the
  *detectors* do not.

---

## References

*Accessibility marked; paywalled items that matter are collected in
[papers-to-obtain.md](papers-to-obtain.md). Per-paper deep-dives for the closest studies are in
[papers/](papers/).*

- **Baker, R. S., Hutt, S., Bosch, N., Ocumpaugh, J., Biswas, G., Paquette, L., Andres, J. M. A. L.,
  Nasiar, N., & Munshi, A. (2024).** Detector-Driven Classroom Interviewing: Focusing Qualitative
  Researcher Time by Selecting Cases In Situ. *Educational Technology Research and Development*,
  72(5), 2841–2863. DOI 10.1007/s11423-023-10324-y. **Full text read** (local copy). See
  [papers/ddci-baker-2024.md](papers/ddci-baker-2024.md).
- **Baker, R. S. J. d., Corbett, A. T., & Wagner, A. Z. (2006).** Human Classification of
  Low-Fidelity Replays of Student Actions. *EDM Workshop at ITS 2006*, 29–36. **Open access; read.**
  The text-replay method; see [papers/text-replays-and-llm-coding.md](papers/text-replays-and-llm-coding.md).
- **Baker, R. S. J. d., & de Carvalho, A. (2008).** Labeling Student Behavior Faster and More
  Precisely with Text Replays. *EDM 2008*. **Open access.** Text-replay *tagging* (multiple tags per
  clip).
- **Beck, J. E., & Gong, Y. (2013).** Wheel-Spinning: Students Who Fail to Master a Skill. *AIED
  2013*. DOI 10.1007/978-3-642-39112-5_44. **Paywalled** (Springer LNCS).
- **Choi, Y., et al. (2020).** Towards an Appropriate Query, Key, and Value Computation for
  Knowledge Tracing (**SAINT**). *L@S 2020*. arXiv:2002.07033. **Open access (arXiv).**
- **D'Mello, S., & Graesser, A. (2012).** Dynamics of affective states during complex learning.
  *Learning and Instruction*, 22(2), 145–157. DOI 10.1016/j.learninstruc.2011.10.001. **Paywalled**
  (Elsevier).
- **Ghosh, A., Heffernan, N., & Lan, A. S. (2020).** Context-Aware Attentive Knowledge Tracing
  (**AKT**). *KDD 2020*. Author copy open (umass.edu). Reports up to ~6% AUC gain over prior KT.
- **Jiang, Y., Bosch, N., Baker, R. S., Paquette, L., Ocumpaugh, J., Andres, J. M. A. L., Moore, A.
  L., & Biswas, G. (2018).** Expert Feature-Engineering vs. Deep Neural Networks: Which Is Better for
  Sensor-Free Affect Detection? *AIED 2018*. Open PDF (upenn.edu). The construction paper behind
  DDCI's Betty's-Brain affect detectors (BROMP labels; 249 engineered features; feature-engineering
  vs. DNN tradeoff). **Full text read.**
- **Kai, S., Almeda, M. V., Baker, R. S., Heffernan, C., & Heffernan, N. (2018).** Decision Tree
  Modeling of Wheel-Spinning and Productive Persistence in Skill Builders. *JEDM*, 10(1), 36–71.
  **Open access (JEDM); full text read.** ASSISTments; decision tree over bottom-out-hint and
  inter-problem-delay features. Restates the Beck & Gong (2013) wheel-spinning definition.
- **Kai, S., Paquette, L., Baker, R. S., Bosch, N., D'Mello, S., Ocumpaugh, J., Shute, V., &
  Ventura, M. (2015).** A Comparison of Video-based and Interaction-based Affect Detectors in
  Physics Playground. *EDM 2015*, 306–313. **Open access (UPenn / ERIC ED560544); full text read.**
  Video vs. interaction affect detectors on the same BROMP ground truth in an open-ended physics
  game; interaction A′ ≈ 0.63 avg (vs. video 0.70). The closest environment in this literature to
  CLUE, and primary study **P4** of the de Morais et al. (2023) sensor-free review. See
  [papers/physics-playground-kai-2015.md](papers/physics-playground-kai-2015.md).
- **Kinnebrew, J. S., Loretz, K. M., & Biswas, G. (2013).** A Contextualized, Differential Sequence
  Mining Method to Derive Students' Learning Behavior Patterns. *JEDM*, 5(1), 190–219. **Open access
  (JEDM / ERIC EJ1115377); method sections read.** The differential-sequence-mining technique behind
  DDCI's behavioral detectors; see [papers/differential-sequence-mining-kinnebrew-2013.md](papers/differential-sequence-mining-kinnebrew-2013.md).
- **Maier, C., & Baker, R. S. (2025).** Can GPT Detect Gaming the System in Text Replays? *ICCE
  2025*. **Open access; read.** GPT-3.5/4 on text replays — above chance (κ ≈ 0.17) but beaten by a
  classical model (κ ≈ 0.26).
- **de Morais, F., Goldoni, D., Kautzmann, T., da Silva, R., & Jaques, P. A. (2023). Automatic
  Sensor-free Affect Detection: A Systematic Literature Review.** arXiv:2310.13711.
  **Open access; full text read.** Finds affect-detector features are hand-crafted/system-specific,
  cross-system transfer "barely addressed," no documented successful cross-system transfer, and
  models not yet production-ready. Physics Playground (Kai et al. 2015) is its primary study **P4**.
  *(Author names verified from the arXiv listing.)*
- **Liu, Z., Liu, Q., Chen, J., Huang, S., Tang, J., & Luo, W. (2022).** pyKT: A Python Library to
  Benchmark Deep Learning based Knowledge Tracing Models. *NeurIPS 2022 Datasets & Benchmarks*.
  arXiv:2206.11460. **Open access (arXiv).**
- **Owen, V. E., Roy, M.-H., Thai, K. P., Burnett, V., Jacobs, D., Keylor, E., & Baker, R. S.
  (2019).** Detecting Wheel-spinning and Productive Persistence in Educational Games. *EDM 2019*,
  378–383. ERIC ED599202. **Open access (ERIC).**
- **Pandey, S., & Karypis, G. (2019).** A Self-Attentive model for Knowledge Tracing (**SAKT**).
  *EDM 2019*. arXiv:1907.06837. Reports +4.43% AUC average over prior KT. **Open access (arXiv).**
- **Paquette, L., Rowe, J., Baker, R. S., Mott, B., Lester, J., DeFalco, J., Brawner, K., Sottilare,
  R., & Georgoulas, V. (2015).** Sensor-Free or Sensor-Full: A Comparison of Data Modalities in
  Multi-Channel Affect Detection. *EDM 2015*, 93–100. **Open access (ERIC ED560545); p.1 read.**
  Companion to the Physics Playground study on a different serious game (GIFT / tactical-combat
  casualty care): compares **interaction-log** vs. **Kinect posture** detectors and finds interaction
  detectors outperform posture (but vary more across contexts). Another *automated*-modality
  comparison — not a human-remote-vs-live-observation one.
- **Piech, C., Bassen, J., Huang, J., Ganguli, S., Sahami, M., Guibas, L., & Sohl-Dickstein, J.
  (2015).** Deep Knowledge Tracing (**DKT**). *NeurIPS 2015*. arXiv:1506.05908. **Open access
  (arXiv).**
- **Radmehr, B., Shved, E., Güreş, F. B., Singla, A., & Käser, T. (2025).** ClickSight: Interpreting
  Student Clickstreams to Reveal Insights on Learning Strategies via LLMs. *AIED 2025*.
  arXiv:2505.15410. **Open access (arXiv).**
- **Rodrigo, M. M. T., & Baker, R. S. (2011).** Comparing the incidence and persistence of learning
  behaviors... across intelligent tutors and educational games. *Research and Practice in
  Technology Enhanced Learning* (author copy open, upenn.edu).
- **Zambrano, A. F., Nasiar, N., Ocumpaugh, J., Goslen, A., Zhang, J., Rowe, J., Esiason, J.,
  Vandenberg, J., & Hutt, S. (2024).** Says Who? How Different Ground Truth Measures of Emotion Impact
  Student Affective Modeling. *EDM 2024* (Atlanta). **Open access (ERIC ED675677 / EDM proceedings);
  abstract + proceedings page verified, full text not read.** Builds separate affect detectors from
  **self-report** vs. **BROMP classroom-observation** labels on a microbiology game (Crystal Island
  lineage) and finds the two ground-truth sources capture *different* signal — evidence that the
  *labeling method itself* shapes what a detector learns. Relevant to research-directions item 2.1.
