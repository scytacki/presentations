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
> [bibliography.md](bibliography.md).

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

- **Strategy / process constructs** — *how* a student is going about the work, independent of whether
  they are stuck or bored: are they **experimenting systematically** (changing one variable at a
  time), **tinkering**, **planning**, **testing a stated hypothesis**? This is a smaller but real
  line, and unlike the three above it is **not** an internal state — it is a property of the action
  sequence itself. It is developed in §2d–2e below, and it is the family our own
  trial-and-error / systematicity / decomposition / reuse questions belong to. *(Solid research for
  systematic experimentation; thinner for the rest.)*

The through-line: the field's "meaning" is mostly **negative signals worth intervening on** —
where a student is stuck, disengaged, or cheating the software. "Insight" / "aha-moments" as a
*detected* construct is comparatively **underdeveloped** — more discussed theoretically (via
confusion resolution) than built as a detector. *(Gap, and one relevant to our use cases.)* The
strategy constructs are the partial exception: they are neutral-to-positive descriptions of *how*
someone works rather than alarms, and they are the ones that transfer best (§2d).

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
[bibliography.md](bibliography.md).)*

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

## 2d. Strategy detectors — the line where log-only detection worked *and* transferred

§2c's pessimism is specifically about **affect**. There is a parallel line of work on **strategy**
constructs where the same recipe (text-replay labels → hand-engineered features → classical ML)
produced detectors that were both usable in real time *and*, apparently, portable — and this
compendium had been missing it.

The anchor is **Gobert, Sao Pedro, Baker, Toto & Montalvo (2012, JEDM, open access)**: machine-learned
detectors of two **inquiry-strategy** constructs in Science Assistments microworlds — whether a
student **designed a controlled experiment** (the control-of-variables strategy, CVS) and whether they
**tested their articulated hypothesis**. Labels came from **text replay tagging** — the very method in
§2b — the detectors were validated under **student-level cross-validation**, and the paper states they
"can be applied at run-time to drive scaffolding intervention." *(Solid research; **abstract-level for
us** — full text not read, so no accuracy numbers are asserted.)*

The part that matters most, and that should be read with care: **Sao Pedro, Gobert & Betts (2014, ITS)**
is reported to take a systematic-inquiry detector built on physical-science simulations with a
*simple, linear causal structure* and identify the same skill in a **life-science Ecosystems simulation
with a complex causal structure.** If that holds up, it is a **behavioral/strategy detector transferring
across structurally different applications** — the exact thing §2c documents as *unachieved for affect*
and that [research-directions.md](research-directions.md) §1.1 frames as the open prize. *(⚠️ **Honest
status: we have only a search-result summary of this paper** — Springer elides the abstract. It is the
top acquisition priority in [bibliography.md](bibliography.md#to-obtain--ranked). Do not cite it in
print before reading it, and in particular find out whether the detector was applied *as is* or
retrained on the new simulation — the two claims are very different in strength.)*

**Why this asymmetry is not a surprise.** Affect is a *latent internal state* that the log reflects only
indirectly — hence the A′ ≈ 0.63 ceiling in Physics Playground and the finding that text replays are
weakest exactly for affect (§2b). A strategy construct like "changed one variable, then ran a trial" is
not reflected in the log; it is **constituted by** the log. The same reasoning explains why *gaming*
detectors ported across systems while affect detectors did not (§2c). **For any construct we want to
detect, the first question should be: is this a state the log hints at, or a behavior the log
contains?**

**The catch, and it is a real one.** CVS detectors work partly because the environment supplies a
well-defined **trial** — set the variables, run the simulation, observe. That boundary is what makes
"varied one thing" a computable predicate. Any environment without an explicit run/test action has to
manufacture that boundary before this method transfers.

## 2e. Programming-process analytics — a second literature we had not covered

Where §2d comes from science inquiry, there is a separate tradition that analyses **the program under
construction** rather than clicks, and it is the closest match to block-programming work like CLUE's
Dataflow tile.

- **Blikstein (2011, LAK)** logs "hundreds of snapshots of students' code during a programming
  assignment" and extracts behaviors quantitatively, categorizing them by programming experience —
  motivated exactly as we would motivate it: in open-ended programming, "students' work can evolve in
  ways that are too subtle or too complex to be detected by the human eye." **Blikstein et al. (2014,
  JLS)** is the scaled-up successor. *(Abstract-level for 2011; **metadata-only for 2014** — do not
  cite its numbers.)*
- **Berland, Martin, Benton, Petrick Smith & Davis (2013, JLS)** derive the **EXTIRE** pathway from
  program snapshots — novices move from **exploration → tinkering → refinement** — giving "empirical
  support for previously theorized processes" and "identifying a role of tinkering in novices'
  learning." This is the closest published precedent for a **tinkering-vs-refinement** construct, and
  worth noting for its stance: tinkering is treated as *a valuable approach for novices*, not as a
  deficit. *(Abstract-level; sample and environment not verified.)*
- **Ross, Srivastava, Blanchard & Andreas (2025; AIED 2026)** train language models on **3.8M
  edit-by-edit program traces** from Pencil Code and find that "many properties of code traces, such
  as **goal backtracking** or number of comments, can be predicted from learned representations of the
  students who write them." This is the modern-AI-on-process-data data point the
  [AI doc](ai-architecture-question.md) wants — and note the serialization advantage over §2b's text
  replays: a **program edit trace looks like a code diff**, a format massively represented in LLM
  pretraining, whereas Maier & Baker blamed GPT's weak text-replay coding on the format being unlike
  anything in training data.
- **The same paper carries the best available evidence on synthetic traces.** Its model trained on
  synthetically generated traces (reconstructed from each trace's final program) "only shows high
  correlation for the 'small addition' types of edits, **which are the only kind it sees during
  training**." *(Read directly.)*

**Decomposition is the outlier — measured on artifacts, not on process.** **Rich, Egan & Ellsworth
(2019, ITiCSE)** reviewed nine published CT measures and found, verbatim: "Regarding decomposition, the
most common approach was to **not measure it at all**. When decomposition was measured, it was often
measured by counting the ways in which students 'modularized' their code… this narrow definition
reveals little about how an individual went about modularizing or decided how to modularize a block of
code, and **completely masks the decision-making process inherent in decomposition**." **Kwon & Cheon
(2019)** is a concrete instance — 11 Scratch programs from 7 middle-schoolers, coded by hand from the
**finished artifacts** — and states the bounding limit plainly: "Without direct communication regarding
the student's solution plan and conceptual understanding of the code, it will be difficult to pinpoint
the reasons for the errors by only examining the outcome of the thinking process." *(Both read
directly.)* **So: detecting decomposition *as it is happening*, from the build history, is a genuine
gap** — see [research-directions.md](research-directions.md) 3.10.

**Reuse is a provenance problem, not a detection problem.** The largest study of student code reuse —
**Dasgupta, Hale, Monroy-Hernández & Hill (2016, CSCW)**, over **2.4M projects from >1M users** —
found more remixing predicts broader command vocabularies and that exposure to a concept via remixing
predicts adopting it. But the reason that study was possible is that **Scratch records remix provenance
explicitly**: the platform knows which project a project came from. With provenance, "reused and then
changed" is a structural diff — **Techapalokul & Tilevich (2017, VL/HCC)** diff each Scratch remix
against its original to measure how much was added. Without it, the fallback is structural matching;
the same paper's static analysis detects **duplicated code** as AST clones across ~600K Scratch
projects, but only *within* a single sprite, so cross-document matching would still have to be built.
*(Read directly, pp. 1–5.)*

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
- **The construct you pick decides how hard the problem is.** The single most useful distinction to
  come out of §2c–2e: is the target a **state the log hints at** (affect — indirect, modest ceiling,
  doesn't transfer) or a **behavior the log contains** (strategy — detectable, real-time, and
  apparently portable)? Almost all of this compendium's pessimism attaches to the first kind. Before
  committing coder time to a construct, ask which kind it is.
- **Programming-process analytics is the closest-matching literature we have** for block-programming
  work like the Dataflow tile — it analyses the artifact-under-construction rather than clicks, which
  is a far richer substrate than a clickstream, and it partly answers the domain-mismatch complaint
  below. It is also the one corner where an LLM has a natural serialization advantage, because an edit
  trace reads as a code diff (§2e).
- **Most of this was built for intelligent tutors and skill-builders**, not K-12 open-ended software
  like CLUE — problems with well-defined answers and clean correctness signals. Our content
  (drawing / tiles / open response) is a **domain mismatch** with most of the cited work — flag it
  whenever we lean on these results. **The important exception is DDCI itself**, demonstrated in the
  open-ended Betty's Brain; so the *method* has an open-ended precedent even though most of the
  *detectors* do not.

---

## References

*Full details + accessibility in [the shared bibliography](bibliography.md). Per-paper deep-dives
for the closest studies are in [papers/](papers/).*

- [Baker et al. (2024) — DDCI](bibliography.md#baker-2024) ✅
- [Baker, Corbett & Wagner (2006) — text replays](bibliography.md#baker-2006) ✅
- [Baker & de Carvalho (2008) — text-replay tagging](bibliography.md#baker-2008) 🟢
- [Beck & Gong (2013) — Wheel-Spinning](bibliography.md#beck-2013) 🔒
- [Berland et al. (2013) — EXTIRE / learning pathways of novice programmers](bibliography.md#berland-2013) 🔒
- [Blikstein (2011) — learning analytics in open-ended programming](bibliography.md#blikstein-2011) 🔒
- [Blikstein et al. (2014) — Programming Pluralism](bibliography.md#blikstein-2014) 🔒⚠️
- [BROMP 2.0 manual — Ocumpaugh, Baker & Rodrigo (2015)](bibliography.md#bromp-manual) ✅
- [Choi et al. (2020) — SAINT](bibliography.md#choi-2020) 🟢
- [Dasgupta et al. (2016) — Remixing as a Pathway to Computational Thinking](bibliography.md#dasgupta-2016) 🟢
- [D'Mello & Graesser (2012) — affective-state dynamics](bibliography.md#dmello-2012) 🔒
- [Ghosh, Heffernan & Lan (2020) — AKT](bibliography.md#ghosh-2020) 🟢
- [Gobert et al. (2012) — real-time inquiry-skill detectors (CVS / hypothesis testing)](bibliography.md#gobert-2012) 🟢
- [Jiang et al. (2018) — Expert Feature-Engineering vs. DNN](bibliography.md#jiang-2018) ✅
- [Kai et al. (2018) — Decision-Tree Wheel-Spinning](bibliography.md#kai-2018) ✅
- [Kai et al. (2015) — Physics Playground affect detectors](bibliography.md#kai-2015) ✅
- [Kinnebrew, Loretz & Biswas (2013) — differential sequence mining](bibliography.md#kinnebrew-2013) ✅
- [Kwon & Cheon (2019) — decomposition from block-based programs](bibliography.md#kwon-2019) 🟢✅
- [Maier & Baker (2025) — GPT gaming detection](bibliography.md#maier-2025) ✅
- [de Morais et al. (2023) — sensor-free affect review](bibliography.md#demorais-2023) ✅
- [Liu et al. (2022) — pyKT](bibliography.md#liu-2022) 🟢
- [Owen et al. (2019) — wheel-spinning in games](bibliography.md#owen-2019) 🟢
- [Pandey & Karypis (2019) — SAKT](bibliography.md#pandey-2019) 🟢
- [Paquette et al. (2015) — Sensor-Free or Sensor-Full](bibliography.md#paquette-2015) 🟢
- [Piech et al. (2015) — DKT](bibliography.md#piech-2015) 🟢
- [Radmehr et al. (2025) — ClickSight](bibliography.md#radmehr-2025) ✅
- [Rich, Egan & Ellsworth (2019) — A Framework for Decomposition in CT](bibliography.md#rich-2019) 🟢✅
- [Rodrigo & Baker (2011) — incidence/persistence](bibliography.md#rodrigo-2011) 🟢⚠️
- [Ross et al. (2025) — Modeling Student Learning with 3.8M Program Traces](bibliography.md#ross-2025) 🟢
- [Sao Pedro et al. (2011) — machine-learned systematic-inquiry detectors](bibliography.md#saopedro-2011) 🔒
- [Sao Pedro, Gobert & Betts (2014) — generalizing a systematic-inquiry detector](bibliography.md#saopedro-2014) 🔒⚠️
- [Techapalokul & Tilevich (2017) — recurring quality problems in block-based software](bibliography.md#techapalokul-2017) 🟢✅
- [Zambrano et al. (2024) — Says Who? ground-truth emotion](bibliography.md#zambrano-2024) 🟢⚠️
