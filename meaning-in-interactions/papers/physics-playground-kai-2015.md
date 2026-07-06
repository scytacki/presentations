# Deep-Dive: Affect Detection in Physics Playground (Kai et al., 2015)

> Kai, S., Paquette, L., Baker, R. S., Bosch, N., D'Mello, S., Ocumpaugh, J., Shute, V., &
> Ventura, M. (2015). **A Comparison of Video-based and Interaction-based Affect Detectors in
> Physics Playground.** *Proceedings of the 8th International Conference on Educational Data Mining
> (EDM 2015)*, 306–313. **Open access** (UPenn / ERIC ED560544). **Full text read.**

**Why it's here.** It is the concrete instance of the "physics simulation" affect detector that the
sensor-free-affect review ([../edtech-landscape.md](../edtech-landscape.md), §2c) gestures at — and
it is one of the ~19 **primary studies (P4)** in the de Morais et al. (2023) systematic review
(arXiv:2310.13711), *not* just a background cite. More importantly for us, Physics Playground is the
**closest environment in this whole literature to CLUE**: open-ended, exploratory, multiple
solutions per level, no clean per-step correctness signal. So it's the best available evidence for
how well log-only affect detection works in the kind of software we actually build — and the paper is
unusually candid that open-endedness *hurts* the interaction detector.

**The problem it addresses.** Affect detectors come in two families — **sensor/video-based** (read
the student's face/body) and **interaction-based** (infer affect from log features). Each had been
built separately; little work compared them *on the same ground truth in a real classroom*. This
paper builds both on the same BROMP-labeled data in an authentic 8th/9th-grade classroom and asks
which does better, per affective state.

**What Physics Playground is.** A 2D Newtonian-physics *game* (formerly Newton's Playground; Shute &
Ventura, Florida State). The student draws simple machines — ramps, levers, pendulums, springboards
("agents of force and motion") — with the mouse to guide a ball to a red balloon. Seven playgrounds,
~10 levels each; multiple solutions; trophies (incl. gold for elegant solutions). Deliberately
exploratory and self-paced. All objects obey gravity/Newtonian motion.

**How affect was labeled (the labeling bottleneck, concretely).** Live human field observation via
**BROMP 2.0** (Baker Rodrigo Ocumpaugh Monitoring Protocol) — a momentary time-sampling protocol —
by **two BROMP-certified observers** using the **HART** Android app, coding the first predominant
affect + behavior per student in a fixed round-robin order (≤20 s per observation). Affective states:
**boredom, confusion, engaged concentration, frustration, delight** (dejection was added late and
*not* modeled). Behaviors: **off-task, on-task conversation.** This is exactly the expensive,
in-person path we *can't* retro-apply to past CLUE data — no text-replay, no survey, trained coders
in the room.

**What it ran on.** 137 students (57 male, 80 female), 8th & 9th grade, one SE-US public school,
~20 per class, 55-min periods over 4 days; two full gameplay sessions (days 2–3) supplied the affect
data. Physics pretest/posttest bracketed the study. ~2,374 observations initially; engaged
concentration dominated (**62%**), then frustration (**11.3%**); boredom (3.2%), delight (2.2%),
confusion (1.8%), and off-task (4.0%) were all rare — a severe class imbalance handled by resampling
(cloning for interaction; SMOTE/downsampling for video), always on **training data only**.

**How the two detector suites were built.**
- **Interaction-based** — features engineered *entirely from Physics Playground gameplay*: number of
  springboards / freeform objects drawn, time between actions, time in a level, gold/silver trophies,
  ball "nudges," ball objects lost off-screen, stacking (gaming) events, level restarts/quits.
  Correlation filtering (drop |r|<0.04 with the label) + **forward selection** cut these to a handful
  per detector (their Table 1). Classifiers: JRip, J48, KStar, Naïve Bayes, step & logistic
  regression (RapidMiner). Missing values filled by zero/average/decision-tree imputation.
- **Video-based** — **FACET** (commercial CERT) → 19 facial Action Units + head pose/position,
  aggregated (max/median/SD) over 3–20 s windows → **78 facial + 3 body-movement** features
  (body movement = proportion of changed pixels vs. a running background). WEKA, 14 classifiers.

**How evaluated.** 10-fold **student-level** cross-validation; metric **A′** (= AUC / probability the
detector ranks a true positive above a true negative; 0.50 = chance). Same ground truth held constant
across suites for a fair comparison.

**Key results (A′; chance = 0.50).**

| Construct | Interaction | Video |
|---|---|---|
| Boredom | 0.629 | 0.617 |
| Confusion | 0.588 | 0.622 |
| Delight | 0.679 | **0.860** |
| Engaged concentration | 0.586 | 0.658 |
| Frustration | 0.559 | 0.632 |
| Off-task behavior | 0.765 | 0.780 |
| **Average** | **0.634** | **0.695** |

All detectors beat chance. Video wins on average, driven mostly by **delight** (visible smiling).
The paper's framing: face-based detectors are **more accurate but only fire when a face is
registered** (~25% of instances were dropped for face-registration failure); interaction-based
detectors are **less accurate but apply in far more situations**, run in the background at no extra
cost, and scale — so the two are **complementary**, arguing for late/decision-level fusion.

**Why it matters for us.**
- **The concrete "detectors don't transfer" exhibit.** Every interaction feature (springboards,
  trophies, nudges) is meaningless outside Physics Playground. You cannot port these detectors
  elsewhere — the exact "custom features + regression fit to one app's labels ⇒ one-app detector"
  case in §2c and in the [research-directions](../research-directions.md) cross-application item.
- **Open-endedness measurably *hurts* the log-only detector — the authors say so.** Their Discussion
  argues interaction affect detectors do worse here than in Cognitive Tutors because an exploratory
  environment gives "coarser-grained indicators of success per unit of time": success/failure isn't
  apparent until minutes of building elapse. Interaction-based affect A′ topped out ~0.63–0.68. This
  is a direct, sobering read for **CLUE**, whose open-endedness is similar or greater.
- **Labeling cost = live BROMP observation** — the expensive path we've flagged as infeasible to
  retro-apply, motivating text-replay / GenAI coding as the cheaper alternative
  ([text-replays-and-llm-coding.md](text-replays-and-llm-coding.md)).
- **Shared lineage with DDCI.** Same research group, same BROMP + engineered-features + classical-ML
  recipe as the Betty's Brain affect detectors behind [ddci-baker-2024.md](ddci-baker-2024.md).

**Limitations.**
- **Small, imbalanced samples per state** (confusion n=38, boredom n=66) forced heavy resampling;
  rare-state A′ figures are the least trustworthy.
- **A′ 0.55–0.68 for interaction affect is modest** — usable for aggregate research, thin for
  confident real-time per-student intervention in an open-ended tool.
- **Single environment, single school, one 4-day study** — illustrative of the modality tradeoff, not
  a generalization claim.
- **FACET is defunct** (commercial CERT no longer available as standalone software), so the video
  pipeline isn't directly reproducible today.

**Verification.** Full 8-page EDM 2015 PDF read from the UPenn open copy
(learninganalytics.upenn.edu/ryanbaker/2015paper176.pdf; also ERIC ED560544). Authors, venue, the
137-student sample, BROMP/HART labeling, the five affect states, feature lists (Table 1), and the A′
values (Table 2) are **directly verified from the primary text**. That the de Morais et al. (2023)
review lists this as **primary study P4** is **verified from the review's arXiv HTML**
(arXiv:2310.13711, Table 1 of included studies). The companion **science-inquiry** simulation paper —
Paquette, Baker et al. (2014), *Sensor-Free Affect Detection for a Simulation-Based Science Inquiry
Learning Environment*, ITS 2014 (ref [29] here) — is **cited, abstract-level only, not read.**
