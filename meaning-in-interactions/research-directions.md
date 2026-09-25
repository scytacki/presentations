# Research Directions

The **actionable** companion to the analytical docs. Where the others describe *what the research
says* ([techniques.md](techniques.md), [edtech-landscape.md](edtech-landscape.md),
[ai-architecture-question.md](ai-architecture-question.md)) and *what the problem is*
([finding-meaning-problem.md](finding-meaning-problem.md)), this one lists *what we could actually
build or test.* Each direction links back to the doc/paper that backs it rather than restating it.

Three groups, deliberately separated. Groups 1 and 2 are both **curated** (the user's judgment) and
**feasible**; they differ on **value**. Group 3 is the uncurated staging area.

- **Group 1 — Curated · feasible · high value.** Clearly scoped, buildable now, and worth the effort.
  Kept small on purpose.
- **Group 2 — Curated · feasible · questionable value.** Scoped and buildable, but whose payoff to our
  overall goal is genuinely uncertain — the cost to run them might not be justified. Kept
  deliberately, not rejected: they answer a useful question even if we may choose not to pursue them.
- **Group 3 — Proposed directions to explore.** Candidates surfaced during this work, not yet vetted
  for scope or priority. Raw material for promotion into Group 1 or 2 (or eventual rejection).

A future **Rejected ideas** section will record what we considered and chose not to pursue, and why.

*Structure for each entry: the question · why it matters · what's known / unknown · what it needs ·
feasibility · links.*

---

## Group 1 — Curated · feasible · high value

### 1.1 Cross-application GenAI affect detector

**The question.** Can a **GenAI affect detector** — a prompt plus a log serialization, validated
against human affect labels on one application — be applied to a **different** application and still
work, *better than* feature+regression detectors (which provably don't transfer)?

*Terminology:* across these directions "**GenAI**" and "**LLM**" name the same technology — a prompted
frontier model. The "GenAI affect detector" here and the "[LLM 'interesting-moment'
detector](#32-llm-interesting-moment-detector-for-clue-ddci-style)" in 3.2 are the same class of thing,
differing only in construct.

**Why it matters.** We build many activities and simulations. A detector that only works in the one
app it was trained on doesn't scale to our situation. Cross-**application** transfer is the
recognized, largely-unsolved prize; if a GenAI approach closes it, it changes how we do
meaning-detection across everything we build — and it's the strongest argument for the GenAI path
over the classical one.

**What's known / unknown.**
- *Known:* feature+regression affect detectors are hand-crafted and **system-specific**, and
  cross-system transfer is "barely addressed" with no documented success (systematic review,
  arXiv:2310.13711; [edtech-landscape.md](edtech-landscape.md) §2c). Behavioral *gaming* detectors
  have transferred across systems, but affect is harder. Affect is also the **hard case** for
  log-only coding (Baker et al., 2006). The one near-neighbor GenAI result — GPT coding gaming from
  text replays — was **weak** and blamed **serialization** (Maier & Baker, 2025;
  [papers/text-replays-and-llm-coding.md](papers/text-replays-and-llm-coding.md)).
- *Known (a ceiling worth respecting):* even *within* a single app, feature+regression affect
  detectors match BROMP only modestly. In **Physics Playground** — the open-ended environment closest
  to CLUE — interaction-based affect detectors reached only **A′ ≈ 0.55–0.68** (avg 0.63), and the
  authors attribute the weakness to open-endedness giving coarse success signals (Kai et al., 2015;
  [papers/physics-playground-kai-2015.md](papers/physics-playground-kai-2015.md)). This suggests a
  **real ceiling on how well affect is recoverable from interaction logs at all** — separate from the
  transfer question. Beating feature+regression may still leave us short of a usable detector.
- *Unknown:* whether a GenAI reading a *semantic* representation generalizes across applications for
  affect — **and** whether *any* log-only method (classical or GenAI) can push meaningfully past that
  within-app ceiling. **We did not find such a study** — this appears novel, though the search was not
  exhaustive (search EDM/LAK before claiming it in print).

**What it needs.**
- **Raw-log affect-labeled data from two applications.** The user reports existing relationships with
  both **ASSISTments** and the **Betty's Brain** researchers, so the datasets are likely obtainable
  *if each IRB permits* — this is the enabler that moves the direction into Group 1.
- **Also worth obtaining: the Physics Playground raw logs (Kai et al., 2015).** Same Baker-group
  provenance as Betty's Brain, so likely reachable, and it is the **open-ended environment closest to
  CLUE** with published BROMP labels. Its reported interaction-based affect A′ (avg 0.63) gives us a
  **concrete number to match or beat** — a GenAI-on-semantic-log detector that can't clear ~0.63 on
  this data would be a strong negative signal. Confirm the *raw event stream* (not just feature
  vectors) is available; see [papers/physics-playground-kai-2015.md](papers/physics-playground-kai-2015.md).
- **Raw logs, not just feature vectors.** The catch (see
  [bibliography.md](bibliography.md) → Datasets): affect datasets are often released as
  engineered features + labels; a GenAI-reads-the-log approach needs the raw event stream (or a
  text-replay rendering). Confirm raw-log availability early.
- A **serialization** per app (depends on 3.1), a **prompt**, and a way to **verify** — human
  affect labels (obtainable retrospectively via text-replay coding, 3.3, though affect is the hard
  case there).

**Feasibility.** Plausible now, given the data relationships. Main risks: (a) obtaining *raw* logs
rather than feature vectors; (b) IRB timelines on both sides; (c) serialization design (3.1) is a
real sub-problem; (d) affect labels from replay may be noisy; **(e) the ceiling risk — even a
successful cross-app GenAI detector may top out at the modest log-only affect A′ (~0.63 in Physics
Playground) and never be accurate enough for confident real-time intervention.** Because two labeled
apps are in reach, transfer can be tested **both directions**.

**Why we keep it despite the ceiling.** The ceiling is an argument against expecting a *great* affect
detector, not against *running the experiment*. The value here is largely diagnostic: (1) it directly
tests the GenAI-vs-classical and cross-app-transfer questions that nothing in the literature has;
(2) even a negative result — "GenAI also can't beat ~0.63, log-only affect has a hard ceiling" —
is a genuinely useful finding that would redirect us toward richer signals or the check-in/ask-the-
student fallback rather than more detector engineering; and (3) Physics Playground gives us a
published number to measure against cheaply.

**Links.** [edtech-landscape.md](edtech-landscape.md) §2c ·
[ai-architecture-question.md](ai-architecture-question.md) (open questions) ·
[papers/physics-playground-kai-2015.md](papers/physics-playground-kai-2015.md) (the A′ ceiling + a
data case) · [papers/text-replays-and-llm-coding.md](papers/text-replays-and-llm-coding.md) ·
[bibliography.md](bibliography.md) (Datasets).

---

## Group 2 — Curated · feasible · questionable value

*Scoped and buildable, but whose payoff to our overall goal is genuinely uncertain. Kept, not
rejected — each answers a useful question even if we may choose not to act on it.*

### 2.1 Human upper-bound for "remote" affect labeling (re-code the Physics Playground video)

**The question.** If a human coder is given the **video + the best available representation of what was
on the student's screen** (a screen recording if the researchers have one; otherwise the interaction
log), how well can they reproduce the **in-person BROMP** affect labels? I.e. what is the **A′ of the
best possible *remote* labeler** on this data?

**Why it matters.** Every "don't send a researcher into every classroom" idea — remote/automated
affect labeling from a webcam + screen — is capped by how much affect signal *survives* into
remotely-captured data at all. Before investing in an **AI** that labels remote data, we should know
the **human ceiling** for that same remote data. If a human with video+screen can nearly match BROMP,
an AI target exists and is worth chasing. If even a human falls well short of BROMP, then remote
labeling is structurally lossy and we should just keep **in-person BROMP** rather than build toward a
target that can't be reached. This directly frames the 2015 result: the automated video detector's
gap to BROMP could be *information loss in the remote channel* vs. *the AI being weaker than a human*,
and this experiment separates the two.

**What's known / unknown.**
- *Known:* in Physics Playground the **automated** (FACET) video detector beat the interaction
  detector on average (A′ 0.695 vs 0.634) but still fell short of the human BROMP ground truth, and
  ~25% of video instances were dropped for face-registration failure (Kai et al., 2015;
  [papers/physics-playground-kai-2015.md](papers/physics-playground-kai-2015.md)). BROMP coders
  integrate face + body + context; the automated pipeline saw only facial AUs + gross body movement.
- *Known (the method already exists — don't overclaim novelty):* coding affect from **synced
  face-video + screen-capture** is a standard technique — the **retrospective affect judgment
  protocol** (D'Mello & Graesser and many since), where the student and/or trained judges label affect
  from the replay. So this item *measures* an established method, it doesn't invent one.
- *Known (the field already frames this as data loss):* BROMP is deliberately a **live** protocol
  precisely because in-person observers use work context, utterances, body language, and peer
  interaction and can "gain full context" a camera misses (BROMP manual). And ground-truth *choice* is
  an active question — *"Says Who?"* (Zambrano, Ocumpaugh, Hutt et al., EDM 2024) shows self-report
  vs. BROMP-observation labels yield detectors that capture *different* signal.
- *Unknown / the actual gap (verified by search, July 2026):* I did **not** find a clean head-to-head
  that fixes the construct and the sessions and reports the **agreement (A′/κ) between a human coding
  only the remote video+screen and the live BROMP observer of those same moments** — a direct number
  for the human "data loss" from going remote. The ingredients exist (retrospective-judgment
  reliability; observer–observer agreement ≈ 78–80%; BROMP inter-rater κ ≥ 0.6) but, as far as I
  found, not assembled into that specific remote-vs-live *human* comparison. That number is what this
  item would produce. *(Stated as "not found," not "does not exist" — the search was not exhaustive.)*

**What it needs.** The Physics Playground **video recordings** (and screen recordings if they exist;
else the interaction logs) plus the original **BROMP labels** — obtainable via the same Kai/Shute
relationship as the 1.1 dataset ask. One or more BROMP-certified coders to re-code the remote data
blind to the live labels. Agreement computed as A′ / κ against the in-person BROMP.

**Feasibility.** Buildable and small *if the raw video is obtainable* (the coding method —
retrospective affect judgment — is off-the-shelf, so the work is mostly data access + coder time) —
but that is the crux and the
main risk: webcam video of minors is IRB-sensitive and may simply not be shareable, and the
researchers may not have kept screen recordings. Given that, plus the honest doubt about whether
affect labeling is even central to our overall goal (in-person BROMP may be perfectly adequate),
the **value is questionable** — hence Group 2, not Group 1. Related to the video-sensing tangent
(a richer-channel detector) but distinct: this measures a *human ceiling*, it doesn't build a detector.

**Prior art (verified July 2026).** Retrospective affect judgment protocol (video+screen replay
coding — D'Mello & Graesser); *"Says Who?"* ground-truth-comparison (Zambrano, Ocumpaugh, Hutt et al.,
EDM 2024); BROMP's live-observation rationale (BROMP manual). None is the exact remote-vs-live *human*
A′ comparison; see the *Unknown* bullet.

**Links.** [papers/physics-playground-kai-2015.md](papers/physics-playground-kai-2015.md) ·
[bibliography.md](bibliography.md) (Datasets) · [edtech-landscape.md](edtech-landscape.md)
§2c and its references (Says Who? · Sensor-Free-or-Sensor-Full).

---

## Group 3 — Proposed directions to explore

*Proposed, not yet curated. Order is rough, not a priority ranking.*

### 3.1 Serialization study — how to render an interaction stream for an LLM

**The question.** What representation of a CLUE (or any) event stream makes an LLM read it best — raw
log lines, the document-history diff, a rendered natural-language summary, a text-replay format, or a
hybrid?

**Why it matters.** Every LLM-on-logs direction (1.1, 3.3, 3.5) depends on it, and the evidence says
serialization is the single biggest lever — *"GPT's training data likely contains little that
resembles text replays"* (Maier & Baker, 2025). Human-readable ≠ LLM-legible.

**What's known / unknown.** *Known:* serialization matters a lot (Maier & Baker; LLM-time-series
work; LogLLM's long-context limits, [ai-architecture-question.md](ai-architecture-question.md)); and
**GUIDE** (Yang et al., CVPR 2026) shows that injecting a *structured* representation of user state
(the **ground-truth** behavior + intent, as text) lifts help prediction by up to ~50 pp over raw frames
(help-content prediction 23.7% → 73.9%) — an oracle upper bound, but direct evidence the representation is the lever
([when-to-intervene.md](when-to-intervene.md) §7). *Unknown:* the right rendering for open-ended document
work like CLUE.

**What it needs.** A labeled task to measure against (could piggyback on 1.1's data), a few candidate
serializations, and an eval loop. **A ready-made external testbed: the GUIDE *dataset*** (CC BY 4.0,
guide-bench.github.io). Note we'd reuse its **data, not its method** — GUIDE only ever fed models sampled
video frames (vision-only), never the event log, so the log-representation comparison is entirely unrun.
It ships **keyboard/mouse event streams + struggle/help labels**, so we could test "does log-representation
X beat sampled frames (and each other) at struggle/help detection" off-the-shelf, then port the winning
method to CLUE. **Caveats, in priority order:** (1) **we'd have to trust its labels** —
Gemini-2.5-Pro-bootstrapped then human-verified, so a model is in the ground-truth loop; sanity-check a
sample before relying on it; (2) desktop creative apps = *tool*-struggle, not learning-struggle (the §7
end), so it validates a *method*, not a CLUE result. **Feasibility:** cheap, high-leverage, low-risk — a
natural first step; the external dataset removes the "we need our own labels first" blocker for an initial
pass, *if* the labels check out.

### 3.2 LLM "interesting-moment" detector for CLUE (DDCI-style)

**The question.** Can a **prompted frontier LLM** reach usable *precision and latency* as a real-time
"interesting-moment" detector on CLUE document streams — not just interpret them after the fact?

**Why it matters.** This is the LLM variant of the detector in the
[CLUE-330 spec](https://github.com/concord-consortium/collaborative-learning/pull/2742), and the
spec's premise is "LLM detector first." We should test whether that premise holds on our data.

**What's known / unknown.** *Known:* ClickSight shows a prompted LLM can *interpret* raw clickstreams
([papers/clicksight-radmehr-2025.md](papers/clicksight-radmehr-2025.md)); DDCI
does *detection* with classical ML at ~30 s. *Unknown:* false-alarm rate on open-ended CLUE work —
interpretation ≠ reliable detection.

**What it needs.** CLUE logs, a concrete "interesting" construct, and labels (3.3). Depends on 3.1.
**Feasibility:** medium; directly extends existing engineering.

### 3.3 Replay labeling of CLUE data (human + GenAI-assisted)

**The question.** Can we get ground-truth labels for **past** CLUE data by replaying document
changes and coding them — and can a GenAI coder match human coders on that same replay?

**Why it matters.** Labels are the bottleneck for every trained/verified detector. Text replays let
us label *existing* data without re-running activities (validated method, ~5× faster than live).

**What's known / unknown.** *Known:* text replays are validated for behavior (Baker et al., 2006,
κ moderate but aggregate accuracy ≈ live); GenAI coding of replays is so far **weak** (Maier &
Baker, 2025). Affect is the hard case for replay coding. *Unknown:* how well either works on
CLUE's open-ended document changes.

**Method — reuse CLUE's existing replay, don't build a renderer.** CLUE already has a **visual replay**
for teachers/researchers that plays back each document change (almost a screencast, but with no audio
and no mouse position/clicks recorded). The build is mostly *extension*: (a) show the recorded **log
events** beside the replay, synced to it, with a playhead line marking where in the event list the
replay currently sits; and (b) extend the existing **comment/authorable-label system** — today anchored
to a point in document history — so a label can also anchor to a **specific log event**, including
events that carry *no* document change (which may be exactly the moment a researcher cares about). The
payoff is **information parity**: the researcher codes from the same signal an LLM coder would get, so
the human-vs-GenAI comparison is fair rather than confounded by the human having richer input.

**What it needs.** Extend the existing visual replay + comment/label system (synced log-event timeline;
labels anchorable to a log event), a construct, and human coders. **What to label is its own open
question — see [3.9](#39-what-should-we-label-on-a-clue-replay-label-schema-study).**
**Feasibility:** medium; **enables 1.1,
3.2, and any trained detector**, so high strategic value.

### 3.4 GenAI feedback agent + compact-and-query infrastructure

**The question.** Can a **GenAI feedback agent** replace the current rule-system-plus-states feedback
— detecting meaning in the interactions and crafting suggestions — and what shared
**compact-and-query** machinery does it need?

**Why it matters.** This is use-case 2, variant B in
[finding-meaning-problem.md](finding-meaning-problem.md): potentially less annoying, easier to extend
to new sims/goals, at the cost of non-determinism and lost auditability. The **compact-and-query**
loop it needs (summarize the stream + query the full log / alternative representations) is the
through-line shared with the detector work.

**What's known / unknown.** *Known:* the current rule+states approach; the `-REL/-IRR` abstraction as
a compaction precedent ([papers/differential-sequence-mining-kinnebrew-2013.md](papers/differential-sequence-mining-kinnebrew-2013.md));
grounding the *content* of help is worked in [../grounding-llm-help/](../grounding-llm-help/).
*Unknown:* precision/annoyance, and how to give authors/researchers auditability over a
non-deterministic agent.

**What it needs.** Log compaction + query tools, a target simulation, and the grounding machinery.
**Feasibility:** larger build; depends on 3.1 and overlaps grounding-llm-help.

### 3.5 Foundation model on unlabeled interaction logs

**The question.** Could **self-supervised pretraining on unlabeled** CLUE interaction logs yield a
reusable representation that makes downstream detectors cheap to build, sidestepping the labeling
bottleneck?

**Why it matters.** It's the structural answer to "we never have enough labels" — pretrain once on
abundant unlabeled logs, fine-tune per construct with few labels.

**What's known / unknown.** *Known:* log-anomaly transformers (e.g. LogBERT/LogLLM) do this for
system logs; no education-specific version is established
([ai-architecture-question.md](ai-architecture-question.md)). *Unknown:* whether education-scale
unlabeled logs are enough, and whether the representation captures *meaning* vs. surface patterns.

**What it needs.** A large unlabeled CLUE log corpus and pretraining infrastructure.
**Feasibility:** research-y, longer horizon; highest uncertainty.

### 3.6 Which intervention trigger is most detectable in CLUE logs?

**The question.** Of the candidate "when to intervene" triggers — **impasse / stuck-on-a-goal**,
**behavioral disengagement** (gaming, wheel-spinning, off-task, rage/dead/error-click-style struggle
signals), and **affect trajectory** (the slide from productive struggle into frustration/boredom) —
which can actually be **detected from CLUE document-event logs** with usable precision, and at what
latency?

**Scope note (initiative matters).** This trigger-detection question mostly bites for **proactive** or
**invite** help. If help is **on-request** (our wildfire default), the primary target shifts to
*understanding the situation to advise well when asked* (a possible separate direction), and the only
trigger we still need is **"stuck and not asking"** to *invite* help — which is just the impasse case
again ([when-to-intervene.md](when-to-intervene.md) §9). So this study is most valuable for the invite
layer and the proactive slice.

**Why it matters.** [when-to-intervene.md](when-to-intervene.md) argues affect-change is a legitimate
but probably not primary trigger, and — its central claim — that **the best trigger depends on the
material's structure**, which spans a spectrum: **gated/well-structured** activities (e.g. the
wildfire simulation with per-page goals) can use step-based impasse detection, while **open-ended**
document-building inverts usefulness (impasse hardest to locate; disengagement/affect-trajectory more
portable). So this study should **sample both material types** — the answer, and thus what detector to
build, likely differs between them. It tests that on our own data instead of assuming it, and is
upstream of items 1.1 / 3.2 / 3.3.

**What's known / unknown.** *Known:* impasse/error triggers assume the per-step correctness that our
**gated** materials have and our **open-ended** ones lack (no natural checkpoints in a
document-over-time); behavioral "struggle" signals are cheap and deployed at scale in open-ended
software (application tutors — Lumière, product-analytics frustration signals;
[when-to-intervene.md](when-to-intervene.md) §7); a fresh GUI benchmark (GUIDE) finds frontier LLMs weak
at struggle detection — but *from screenshots*, not from interaction logs (they never tested logs), so it's
only a weak signal for our log case. *Unknown:* which trigger is recoverable from CLUE logs **per material
type**, and whether **authors formalizing goals + breaking work into evaluation points** would make the
impasse trigger tractable on the open-ended end.

**What it needs.** CLUE document-event logs; a small set of candidate trigger-detectors (one per
trigger family, incl. cheap click/hesitation/undo "struggle" heuristics); and *some* validation of
"was this actually an intervention-worthy moment" (human judgement on replays, 3.3). Could piggyback
on 3.2's labeling.

**Feasibility.** Medium; the behavioral/struggle-signal detectors are cheap to prototype (they're
heuristics over existing events), the impasse one is the hard/uncertain part. Good scoping study to run
*before* committing to a specific detector in 1.1 / 3.2. **Value:** high as a decision-informer, which
is why it's a serious Group 3 candidate rather than a latent pointer.

**Links.** [when-to-intervene.md](when-to-intervene.md) · [finding-meaning-problem.md](finding-meaning-problem.md)
(use-case 2) · [edtech-landscape.md](edtech-landscape.md) (behavioral detectors) · items 1.1 / 3.2 / 3.3.

### 3.8 Rank serializations by clustering their embeddings against known groups

**The question.** For a set of candidate serializations of a session stream, which one's *embedding
geometry* best **recovers a known grouping** — human labels, or the groups an existing detector/DDCI
pipeline already produces — when you embed every session, cluster the embeddings unsupervised, and
score the clusters against those groups? A **label-light** way to rank serializations, and a test of
whether an embedding can **match** a classification ML model (reproduce its groups) — with, as a
gated extension, whether it **discovers** structure the classifier missed.

**Why it matters.** It's the cheap sibling of [3.1](research-directions.md): 3.1 needs a prompt + a
labeled task per serialization; this needs only the labels (no prompt, no task-tuning), so it can
**shortlist** serializations before paying for full task evals. Serialization is the single biggest
lever for every LLM-on-logs direction (1.1, 3.2, 3.5), so a cheap pre-filter over it is high-leverage.
The primary success criterion is **reproducing the known groups** — showing the embedding clustering
can recover the human labels (and the detector's groups) it's scored against. Whether it *also*
surfaces new structure the classifier missed is a secondary, welcome result, not what the study has to
prove.

**What's known / unknown.** *Known (all verified — see
[clustering-representation-eval.md](clustering-representation-eval.md)):* every component is canonical.
External cluster validation has primary sources (V-measure — Rosenberg & Hirschberg 2007; ARI —
Hubert & Arabie 1985; NMI — Strehl & Ghosh 2002; AMI — Vinh et al. 2010). "Embed → k-means → score vs
gold labels with V-measure" **is** the MTEB Clustering task (Muennighoff et al., EACL 2023; MMTEB
2025) — representation-quality-via-clustering is a first-class benchmarked setting. Serialization
changes the geometry (TabLLM, AISTATS 2023; prompt-format swings up to 76 pts, Sclar et al. ICLR
2024; clustering quality depends heavily on the embedder, Petukhova et al. 2025). LLM-in-the-loop
clustering exists (ClusterLLM, EMNLP 2023). The discovery angle is a named field (Generalized/Novel
Category Discovery — Vaze et al. CVPR 2022; Han et al. ICLR 2020). *Unknown / the gap:* **no verified
prior work** uses this pipeline to *rank serializations*, or applies it to *learner-interaction logs
validated against human codes / detector groups* — ClickSight *interprets* clickstreams, it doesn't
cluster-vs-label. That gap is the novelty (search EDM/LAK before claiming it in print).

**What it needs.** A fixed embedder (ideally close to the deploy LLM family); a labeled session slice
with **two** ground-truths — human labels (from [3.3](research-directions.md)) and detector/DDCI
groups; the candidate serializations from [3.1](research-directions.md) — **including a raw-timestamp
vs. explicit-gap ("paused N s") pair**, a cheap, well-motivated lever (pre-computing the inter-event
gap dodges the timestamp-subtraction LLMs are measurably bad at; see
[clustering-representation-eval.md](clustering-representation-eval.md) §3); mini-batch k-means at
k = #groups; **AMI/ARI** (chance-corrected — session counts are small) plus V-measure.

**Feasibility.** Cheap and low-risk — it's the MTEB recipe on our data; the main cost is the labels
(shared with 3.3) and honest care about confounds (**hold the embedder fixed**; geometric
separability is a *proxy* for LLM-readability, not identical to it; reproducing a *detector's* groups
≠ matching truth, so validate against human labels primarily). Natural companion to 3.1 — run this
first to shortlist, then 3.1 to confirm.

**Dual use — the embeddings also seed a vector database.** The same per-session embeddings double as
the **index for a vector DB** over the whole log corpus ("find sessions with a pattern like this one"),
so the work overlaps the retrieval/compact-and-query goal, not just serialization choice. Two notes:
(a) for the vector-DB use the proxy worry is *weaker* — retrieval uses the geometry directly — so a
serialization that clusters well is a strong candidate; **but** (b) the vector-DB case has **more
freedom on embedder choice** (nothing reads the raw embedding as a prompt, so we needn't match the
deploy LLM family and should sweep several retrieval embedders), and clustering ≠ retrieval as tasks —
so treat the clustering winner as a prior, not a verdict, and re-run the embedder sweep for retrieval.
See [clustering-representation-eval.md](clustering-representation-eval.md) §10.

**Links.** [clustering-representation-eval.md](clustering-representation-eval.md) (full evidence) ·
[3.1](research-directions.md) (supervised sibling) · [3.3](research-directions.md) (the labels) ·
[papers/clicksight-radmehr-2025.md](papers/clicksight-radmehr-2025.md) (nearest ed-tech neighbor).

### 3.9 What should we label on a CLUE replay? (label-schema study)

**The question.** Given the replay + synced-log labeling UI of [3.3](#33-replay-labeling-of-clue-data-human--genai-assisted),
*what should a researcher actually code?* Which **label types** are worth collecting, which are even
**recoverable from this channel**, and which are well-enough defined to code reliably? This is the
sibling of [3.6](#36-which-intervention-trigger-is-most-detectable-in-clue-logs) one level up: 3.6 asks
which *trigger* is detectable; this asks which *labels* are worth detecting at all.

**Why it matters.** Every labeling item (1.1, 3.2, 3.3, 3.6) quietly assumes "a construct." The
construct is the actual bottleneck, not the tooling — and the candidate label types differ wildly in
maturity, so picking the wrong one wastes scarce coder time. Answering this first tells the other items
*what* to put in front of coders.

**Candidate label types (the raw material — not yet vetted).**

| Label type | Example values | Definedness | Known hardness / channel risk |
|---|---|---|---|
| **Affect** | BROMP categories (bored, frustrated, engaged concentration, …) | well-defined (BROMP) | the **hard case** for log/replay coding (Baker et al., 2006) |
| **Behavior** | trial-and-error · systematicity · decomposition · reuse — see [3.10](#310-detecting-programming-process-strategies-in-a-block-programming-environment) | **researcher-named**, no longer a blank | strategy constructs the log largely *contains*; per-construct hardness in 3.10 |
| **Student state** | productive struggle · at impasse | semi-defined | overlaps affect + behavior |
| **Intervention-worthy moment** | should-a-tutor-step-in (+ why) | tied to 3.6's triggers | inherits 3.6's per-material-type difficulty |
| **"Something else is going on / why"** | free note | meta | *motivated by* the lossy channel — see below |
| **Demonstrated understanding** | "student demonstrated understanding of X" (rubric on the final document) | **proven at document level** (existing rubric coding); pinning *when* on the timeline is not | depends entirely on what X is; document-level coding can't locate *when* understanding was gained — see below |

**Demonstrated understanding is partly proven — but only for the final document.** We have already had
researchers code final CLUE documents against **rubrics they designed** — done for at least **two
rubrics across two curricula**. That is exactly the "student understands X" label, and it proves the
construct is codable. Two caveats scope the remaining work: (a) it is **document-level, not
history-level** — a rubric on the final artifact shows the student *demonstrated* an understanding but
not *when* they gained it (they may have arrived already understanding it), so it doesn't locate a
moment on the replay timeline; hence "**demonstrated** understanding of X" is the more precise phrasing.
(b) It **depends entirely on what X is** — X must be a concept the curriculum is actively exploring or
probing; there is no general "does the student understand things" label. What 3.9 adds beyond the
existing rubric work is whether the replay + synced-log view lets a coder push this from document-level
toward **history-level** — pinpointing where on the timeline the understanding is first demonstrated —
which final-document rubric coding cannot do.

**The lossy channel bounds all of it.** The replay has **no audio, no mouse position/clicks, and no
physical presence**. That loss both *motivates* the "something else is going on" meta-label (a student
may have turned to a peer, learned something, and come back — invisible in the document stream) and
*caps* every other label's recoverability. This is the same data-loss argument that
[2.1](#21-human-upper-bound-for-remote-affect-labeling-re-code-the-physics-playground-video) makes for
remote affect labeling; that item's human-ceiling result would directly inform how much to trust affect
labels coded here.

**What it needs / first probe.** Cheap gate before committing: have a researcher attempt **affect**
labeling (the best-defined type) on a few students from replay alone and report whether it feels
possible at all. If affect — the most mature construct — doesn't survive the channel, the less-defined
types are unlikely to. In parallel, treat **behavior** as an open elicitation: let researchers name the
behaviors *they* want to find rather than imposing our seed list. **That elicitation has now happened
once** — see [3.10](#310-detecting-programming-process-strategies-in-a-block-programming-environment),
which takes four researcher-named strategy constructs from the block-programming work and assesses each
for detectability. Treat 3.10 as the worked example of what this row should contain, not as the whole
answer; other curricula will name different behaviors.

**Status — deliberately staged, not worked.** These label types open further research questions we have
not explored (how to define behavior; whether *demonstrated* understanding can be pushed from
document-level to a *when* on the timeline — the document-level version is already proven; how much the
missing channels matter per material type). Captured here as raw material for promotion, in the house
style of "not found / not worked" rather than "solved."

**Links.** [3.3](#33-replay-labeling-of-clue-data-human--genai-assisted) (the labeling UI) ·
[3.6](#36-which-intervention-trigger-is-most-detectable-in-clue-logs) (trigger sibling) ·
[2.1](#21-human-upper-bound-for-remote-affect-labeling-re-code-the-physics-playground-video)
(channel data-loss ceiling) · [1.1](#11-cross-application-genai-affect-detector) (consumes the labels).

### 3.10 Detecting programming-process strategies in a block-programming environment

**The question.** Can we detect, from the interaction/edit stream of a visual block-programming
environment (CLUE's Dataflow tile — blocks, sensors, thresholds), four researcher-named strategy
constructs: **trial-and-error**, **systematicity**, **decomposition**, and **reuse**?

The constructs as posed by the researcher who wants them:

| # | Construct | As stated |
|---|---|---|
| A | **Trial and error** | rapidly adding/removing/changing blocks (a) without systematicity (one change at a time), (b) without pausing to document (write / draw / fill in tables), and/or (c) without arriving at a working program |
| B | **Systematicity** | the contrast class — students who *do* write down their trials, add documentation, and run many tests varying only one thing |
| C | **Decomposition** | building a program that does *just* one part first (get the sensor working, without the EMG reader) before adding the rest |
| D | **Reuse** | pulling from prior work — their own or someone else's — and modifying it; flagged especially when it is *not* the curriculum's own starter material |

**Why it matters.** Three reasons, in increasing order of importance.

1. These are **strategy constructs — behaviors the log contains, not states it hints at**
   ([edtech-landscape.md](edtech-landscape.md) §2d). Nearly all of this compendium's pessimism (the
   A′ ≈ 0.63 affect ceiling in [1.1](#11-cross-application-genai-affect-detector), affect as the hard
   case for replay coding, non-transferring detectors) attaches to the *other* kind of construct. The
   evidence points the opposite way here — up to and including one reported case of a
   systematic-inquiry detector generalizing to a structurally different simulation
   ([Sao Pedro, Gobert & Betts 2014](bibliography.md#saopedro-2014)). *(⚠️ We have only a search-result
   summary of that paper; it is what would make transfer part of this item's case, so read it before
   leaning on it.)*
2. The signal lives in an **artifact with formal structure**, not in a clickstream. A Dataflow program
   is a graph; you can compute exact diffs, edit granularity, connected components, and run outcomes.
   That is a far richer substrate than the mouse-click streams most cited work runs on, and it is what
   [programming-process analytics](edtech-landscape.md) (§2e) exploits.
3. **It answers [3.9](#39-what-should-we-label-on-a-clue-replay-label-schema-study)'s open question by
   instance.** 3.9 flags the **Behavior** label type as ill-defined and says to "let researchers name
   the behaviors *they* want to find rather than imposing our seed list." This *is* that elicitation
   having happened — four named, researcher-motivated behaviors, all of which plausibly survive the
   lossy replay channel that caps affect labeling.

**What's known / unknown — per construct.** Ranked by expected effort-to-payoff, best first.

- **D · Reuse — least a detection problem, most an instrumentation problem.** *Known:* the largest
  study of student code reuse ([Dasgupta et al. 2016](bibliography.md#dasgupta-2016), 2.4M Scratch
  projects) was only possible because **Scratch records remix provenance explicitly**. If our
  environment logs "copied from document X" / "dragged in from the shared workspace" with a source
  identifier, reuse is exact and nearly free, and "…and made changes" is a diff against that source
  over time — [Techapalokul & Tilevich (2017)](bibliography.md#techapalokul-2017) compute a version
  of this for Scratch remixes, structurally diffing each remix's JSON against its original to count
  added and replaced scripts. If provenance is *not* logged, the fallback is structural matching
  across the corpus. The building block exists — the same paper detects duplicated code in ~600K
  Scratch projects via AST subtree/fragment clone detection — but only **within a single sprite**;
  matching across documents or students would be our extension, not something off the shelf.
  *Unknown / the one real subtlety:* the **curriculum baseline**. Every student starts from the same author-provided starter,
  so identical subgraphs are the norm, not evidence. Define reuse as "matches a source that is neither
  the curriculum starter nor this student's own current document" and most of the noise disappears —
  which also directly satisfies the researcher's "especially if it's not part of the curriculum."
- **A + B · Trial-and-error and systematicity — one construct, two poles. Do not build two
  detectors.** *Known:* the closest precedent for the *outcome* framing is
  [Berland et al.'s EXTIRE](bibliography.md#berland-2013) (exploration → tinkering → refinement, from
  program snapshots), and for the *detector* framing it is
  [Gobert et al. (2012)](bibliography.md#gobert-2012) — CVS ("designed a controlled experiment")
  detected from logs, labeled by **text replay tagging**, validated under student-level
  cross-validation, run-time deployable. "Change one thing, then test" in a program is CVS applied to
  a program. Criterion (b), documentation, is nearly free: writes to text tiles, tables and drawings
  are already discrete logged events. *Unknown / the two catches:* **(i)** criterion (c), "achieving a
  working program," needs the correctness signal that [when-to-intervene.md](when-to-intervene.md)
  identifies as what open-ended CLUE lacks. **Recommendation: keep it out of the detector and use it
  as the *grouping variable* instead** — which is exactly what
  [differential sequence mining](papers/differential-sequence-mining-kinnebrew-2013.md) needs;
  working-vs-not is cheap, outcome-relevant, and needs no human coder. **(ii)** CVS detectors work partly because the environment supplies a well-defined
  **trial** (set variables → run). If Dataflow logs an explicit run/execute/deploy event we inherit
  that structure; if the graph simply runs continuously, the trial boundary has to be synthesised from
  edit-pause structure first ([edtech-landscape.md](edtech-landscape.md) §2d, closing paragraph).
- **C · Decomposition — the hardest, and the most novel.** *Known:*
  [Rich, Egan & Ellsworth (2019)](bibliography.md#rich-2019) reviewed nine CT measures and found
  decomposition is usually **not measured at all**, and when it is, it is counted as artifact-level
  "modularization," which "completely masks the decision-making process inherent in decomposition."
  [Kwon & Cheon (2019)](bibliography.md#kwon-2019) is the instance — decomposition coded by hand from
  **finished Scratch programs**. So **process-level** decomposition detection is close to unrun; this
  is the item with genuine research novelty. *What makes it tractable anyway:* it is a **graph-state
  computation**, not sequence mining. At each edit, compute the connected components of the block
  graph — which components exist, which are complete/runnable, which were actually executed. "Had a
  sensor-only component that ran, and only later introduced the EMG branch" is a computable structural
  signature over the program-state trajectory. *Unknown / the hard part:* **the log cannot give you
  intent.** Deliberately building sensor-first is, in the event stream, identical to simply not having
  reached the EMG part yet — the limit Kwon & Cheon state directly ("Without direct communication
  regarding the student's solution plan…"). Two mitigations: **(1)** require the sub-program to have
  been **run while isolated** — testing a component before integrating it is evidence of a plan,
  whereas accretion produces no such run; **(2)** take the sub-goal set from the curriculum author,
  which [when-to-intervene.md](when-to-intervene.md) and [3.6](#36-which-intervention-trigger-is-most-detectable-in-clue-logs)
  already predict we will need ("authors formalizing goals").

**What it needs.**

- **Step 1, and it gates everything: an instrumentation audit.** Drive the interface deliberately
  performing each of the four behaviors and check whether the resulting log actually distinguishes
  them. Four make-or-break questions: *(a)* does the log carry **per-block edits**, or only whole-tile
  state snapshots? *(b)* is there an explicit **run/execute** event (the trial boundary)? *(c)* does a
  copy/drag-in carry a **source identifier** (the provenance)? *(d)* are **documentation-tile writes**
  timestamped as separate events? Cheap — on the order of a day — and every downstream decision
  depends on the answers.
- **A grouping variable before any labels:** working-vs-non-working program, then Kinnebrew-style
  differential mining. This produces a first result with **no human coding at all**.
- **Then** labels, via [3.3](#33-replay-labeling-of-clue-data-human--genai-assisted)'s replay + synced
  log-event UI, with these four as the concrete **Behavior** schema for [3.9](#39-what-should-we-label-on-a-clue-replay-label-schema-study).
- **Data.** Note the scale at which comparable results were obtained: ~22 students
  ([Kinnebrew](papers/differential-sequence-mining-kinnebrew-2013.md)), a few hundred
  ([Gobert 2012](bibliography.md#gobert-2012)). These are not big-data constructs; a few dozen real
  sessions would give a genuine feasibility read.

**On generating the interaction data by having an AI drive the interface.** Worth separating two uses,
because they have opposite verdicts.

- **Legitimate, and recommended:** the **instrumentation audit** above is exactly this, and it is the
  best use of the technique — a scripted actor performing each behavior on purpose is precisely what
  you want for checking whether the log can tell them apart. Also fine as **unit-test fixtures** for
  the diff/graph engine, and as deliberately **adversarial near-misses** to find where a detector
  breaks.
- **Not legitimate: using synthetic sessions to *validate* a detector or estimate precision/recall.**
  It is circular — the actor is scripted from the same construct definition the detector encodes, so
  you measure whether the detector recovers your script, not whether the construct is recoverable from
  students. It also omits what makes real logs hard: ambiguous middle cases, off-task time, peer
  interruptions invisible in the document stream (3.9's "something else is going on" label), misclicks
  and abandonment. There is now **published evidence** for the general shape of this worry:
  [Ross et al. (2025)](bibliography.md#ross-2025) trained a model on synthetic traces reconstructed
  from final programs and found it "only shows high correlation for the 'small addition' types of
  edits, which are the only kind it sees during training." *(Note the mismatch honestly: their
  synthetic traces were **program-derived reconstructions**, not an LLM driving a UI, so this is
  suggestive of the failure mode, not a direct test of it.)*
- **And the binding constraint is not data volume — it is labels on real data**, which is this
  compendium's own conclusion ([3.3](#33-replay-labeling-of-clue-data-human--genai-assisted),
  [3.9](#39-what-should-we-label-on-a-clue-replay-label-schema-study)). Synthetic data cannot relieve
  a label bottleneck; it manufactures labels that are true by construction and therefore worthless as
  ground truth.

**Feasibility.** Reuse and the trial-and-error/systematicity pair are **medium and near-term** — the
features are computable from the artifact and the precedent detectors are classical. Decomposition is
**higher-risk, higher-novelty**. The instrumentation audit is cheap enough that it should happen
regardless of whether the rest is pursued.

**One favourable asymmetry worth testing.** Every LLM-on-logs direction here inherits the serialization
problem (Maier & Baker: GPT coded gaming from text replays poorly, blamed on the format being unlike
anything in pretraining). But a **block-program diff serializes into something that reads like a code
diff** — a format massively represented in pretraining, unlike a clickstream. That is a concrete reason
to expect an LLM to do better here than the text-replay result predicts, and it makes this environment
an unusually good testbed for [3.1](#31-serialization-study--how-to-render-an-interaction-stream-for-an-llm).

**Links.** [edtech-landscape.md](edtech-landscape.md) §2d–2e (the two literatures this rests on) ·
[3.9](#39-what-should-we-label-on-a-clue-replay-label-schema-study) (this is its **Behavior** label
type, instantiated) · [3.3](#33-replay-labeling-of-clue-data-human--genai-assisted) (the labels) ·
[3.1](#31-serialization-study--how-to-render-an-interaction-stream-for-an-llm) (serialization) ·
[3.6](#36-which-intervention-trigger-is-most-detectable-in-clue-logs) (sibling: which *trigger* is
detectable) · [papers/differential-sequence-mining-kinnebrew-2013.md](papers/differential-sequence-mining-kinnebrew-2013.md)
(the grouping/differential method).

### 3.7 Also latent — the other use cases

Lower-defined directions carried from [finding-meaning-problem.md](finding-meaning-problem.md) that
each reduce to finding meaning in interactions: **post-hoc corpus analysis** (mine past sessions for
recurring trajectories), a **teacher-facing formative signal** (DDCI explicitly proposes this), and
**product/UX evaluation** (does a feature help; what are students trying to do that we don't
support). Kept here as pointers until someone wants to promote one.

---

## Rejected ideas

*(None yet.)* Format when we add them: **the idea · why it was rejected · what would change our mind.**

---

## References

*Full details + accessibility in [the shared bibliography](bibliography.md).*

- [Berland et al. (2013) — EXTIRE / learning pathways of novice programmers](bibliography.md#berland-2013) 🔒
- [Dasgupta et al. (2016) — Remixing as a Pathway to Computational Thinking](bibliography.md#dasgupta-2016) 🟢
- [Gobert et al. (2012) — real-time inquiry-skill detectors (CVS / hypothesis testing)](bibliography.md#gobert-2012) 🟢
- [Kwon & Cheon (2019) — decomposition from block-based programs](bibliography.md#kwon-2019) 🟢✅
- [de Morais et al. (2023) — sensor-free affect review](bibliography.md#demorais-2023) ✅
- [Radmehr et al. (2025) — ClickSight](bibliography.md#radmehr-2025) ✅
- [Rich, Egan & Ellsworth (2019) — A Framework for Decomposition in CT](bibliography.md#rich-2019) 🟢✅
- [Ross et al. (2025) — Modeling Student Learning with 3.8M Program Traces](bibliography.md#ross-2025) 🟢
- [Sao Pedro, Gobert & Betts (2014) — generalizing a systematic-inquiry detector](bibliography.md#saopedro-2014) 🔒⚠️
- [Techapalokul & Tilevich (2017) — recurring quality problems in block-based software](bibliography.md#techapalokul-2017) 🔒⚠️
- [Yang et al. (2026) — GUIDE](bibliography.md#yang-2026) ✅
- [Zambrano et al. (2024) — Says Who? ground-truth emotion](bibliography.md#zambrano-2024) 🟢⚠️
