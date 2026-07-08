# When to Intervene — the decision the detectors serve

Most of this doc set is about *detecting* states in an interaction stream. This one steps back to the
question that sits **upstream** of detection: *when is intervention actually warranted, and what cue
justifies it?* You only build a detector for state X because you believe "student is in state X" is a
good moment to act. So choosing what to detect already presupposes an answer to the intervention-timing
question — and that answer is far less settled than the detector literature makes it look.

The working hypothesis behind the affect focus is reasonable: **the best moment to intervene is when a
student's affect turns — e.g. productive struggle sliding into boredom/disengagement.** This doc checks
that hypothesis against the broader literature. Short version: it's a *legitimate* trigger but neither
the only nor, on the evidence, the primary one. The oldest and strongest findings about intervention
timing are **cognitive** (impasses) and **behavioral** (contingency, disengagement), and the affect
story has an important twist — negative affect is often the state you should *not* interrupt.

**Domain caveat up front (applies to the whole doc).** Almost every result below comes from **one-to-one
human tutoring** or **intelligent tutoring systems** with a *clean per-step correctness signal* (physics
problems, algebra steps). **Our own materials span the whole spectrum** — which is the single most
important framing point in this doc:

- **Some CLUE-family activities are well-structured / gated.** E.g. a **wildfire-simulation** activity that
  gives the student a **specific goal on each page**: clear steps, clear gates, a checkable target per
  page. These map *directly* onto the problem-solving tutoring below (§§1–4) — impasse, contingency, and
  the assistance dilemma apply almost off-the-shelf.
- **Others are open-ended / ill-structured.** Open document-building (drawing, tiles, modeling, open
  response) where the student builds up **one artifact over time** with many valid paths and no natural
  per-step checkpoints, so constructs like "impasse" and "error" are hard to *locate in time* even when the
  author defines correctness. Correctness itself is possible but optional here — the author's choice.

**The two need genuinely different intervention machinery, and a solution built for one probably does *not*
transfer to the other** — a tendency worth resisting. The gated wildfire case can reuse step-based
detection; the open-ended case needs the design-tutoring / behavioral-affective approach (§8). A likely
consequence for the open-ended end (picked up in §10): authors may need to **formalize goals** and **break
the work into evaluation points**, and even then, *detecting goal achievement* in an open artifact is
harder than anything in this problem-solving research. This spectrum runs through
[edtech-landscape.md](edtech-landscape.md) too.

---

## 1. The strongest empirical anchor is cognitive, not affective: impasses

The most-cited direct evidence on *when* tutoring produces learning is **VanLehn, Siler, et al. (2003)**,
*Why Do Only Some Events Cause Learning During Human Tutoring?* They analyzed ~125 hours of expert human
tutoring of physics and found that learning happened almost exclusively when the student was **at an
impasse** — stuck, having made an error, or having done something correctly *but expressing uncertainty
about it*. When students were **not** at an impasse, tutorial explanations rarely produced learning, no
matter how good the explanation. Once at an impasse, explanations sometimes helped, and different
explanation types helped different knowledge types.

The practical reading: **an impasse is the highest-value intervention window**, and it is a *cognitive*
cue, not an affective one. This is the clearest single answer the field has to "when," and it partly
speaks to the "maybe there's a different metric" intuition — the metric might be *readiness* (an impasse
creates cognitive need) rather than *affect*. *(Solid — widely replicated framing; **paywalled**,
verified at abstract/metadata level.)*

**CLUE caveat:** "impasse" is defined via errors and stuck-states against a correct solution path. Open-ended
CLUE work often has no single correct path, so detecting an impasse is itself an unsolved sub-problem —
possibly harder than detecting disengagement.

---

## 2. The formal timing policy: contingency and fading

The origin of "scaffolding" — **Wood, Bruner & Ross (1976)**, *The Role of Tutoring in Problem Solving* —
studied a single trained tutor helping **3-, 4-, and 5-year-olds** with a block-construction task and named
six tutoring functions (recruitment, reduction of degrees of freedom, direction maintenance, marking
critical features, **frustration control**, demonstration). The follow-on **contingent shift rule** (Wood
and colleagues) is a concrete when/how-much policy: **increase control after the child fails a step,
decrease it after success**, and *fade* support over time so responsibility transfers to the learner.
Contingent-then-faded support repeatedly comes out as the most effective pattern. (The age effect is
itself telling: 3-year-olds needed constant goal reminders, 4-year-olds mostly verbal correction,
5-year-olds were largely independent.)

**Don't over-read "construction" as open-ended, though.** The task was assembling a *specific* six-layer
pyramid whose blocks interlock by peg-and-hole in essentially **one correct way** — a single target
structure with a single correct assembly. So despite being "building," it is a **well-structured** task,
and it belongs with the physics/math problem-solving studies, *not* with open-ended design. It maps well
onto our **gated** materials (the wildfire-style, per-page-goal activities). What changes when the task is
genuinely ill-structured — many valid solutions, no correct path, our open document-building activities —
is a different tutoring tradition entirely; §8 takes that up.

Why it matters here: the contingent shift rule is an explicit, testable **intervention-timing rule**
derived from watching human tutors, and its trigger is *success/failure on the last step* —
behavioral/cognitive, not affective (though "frustration control" shows affect was in scope from the
start), **and it presumes a step-structured task — which our gated activities have and our open-ended ones
don't.** *(Solid — foundational;
**paywalled** (Wiley), metadata + six functions + the 3–5 age range and pyramid task verified.)*

---

## 3. The framing of the tradeoff: the assistance dilemma

**Koedinger & Aleven (2007)**, *Exploring the Assistance Dilemma in Experiments with Cognitive Tutors*,
named the tension every intervention decision lives inside: **give too much help → shallow processing,
reduced effort, weaker self-regulation; give too little → floundering, frustration, wasted time.** The
"dilemma" is that the optimum depends on the student and moment, and both failure modes are real and
measured. This is the vocabulary the project's "when to intervene" question belongs to — and it reframes
the goal from "detect a bad state" to "find the point where *withholding* stops helping and *assisting*
starts helping." *(Solid framing; unresolved by design — **paywalled** (Springer EPR), metadata
verified.)*

---

## 4. Expert human tutors have been observed and catalogued — yes

Directly answering "have expert tutors been interviewed/observed for the cues they use?": yes, repeatedly.
**Lepper, Drake & O'Donnell-Johnson (1997)**, *Scaffolding Techniques of Expert Human Tutors*, is a
book-chapter catalogue of what expert tutors actually do, built from observation. A headline theme: expert
tutors spend **as much effort managing motivation and affect as cognition** — they use *indirect* cues
("what else?", leading questions) so the student self-corrects rather than being told, they protect the
student's sense of competence, and they manage frustration deliberately. Graesser, Person and colleagues
did parallel work analyzing tutorial-dialogue *moves* in naturalistic tutoring.

So the "what cues do experts use" question has a real observational literature, and its answer is a
**blend**: cognitive readiness (impasse, error) *and* affective/motivational signals (frustration,
flagging interest, confidence). This is descriptive, not causal — it tells you what skilled tutors do, not
that each move provably caused learning. *(Existence of the literature: solid. Lepper, Drake &
O'Donnell-Johnson 1997 full citation verified. Lepper & Woolverton's "wisdom of practice" and Graesser,
Person & Magliano 1995 are mentioned from memory — **not re-verified this session**.)*

---

## 5. The affect twist: negative affect is often the state NOT to interrupt

Here the broad literature refines the affect-change hypothesis in an important way. **D'Mello, Lehman,
Pekrun & Graesser (2014)**, *Confusion Can Be Beneficial for Learning*, argues that confusion —
cognitive disequilibrium from a contradiction, anomaly, or error — **helps deep learning when it is
appropriately induced, regulated, and resolved.** Confusion in that productive band is a state you often
want to *protect*, not rescue the student out of. It only becomes a problem when it goes unresolved and
decays into frustration and then disengagement.

That reshapes the "intervene when affect changes" rule into something sharper:

- **Don't** intervene simply because affect turned negative — productive struggle/confusion *is* negative-
  valence and is where the learning happens.
- **Do** intervene on the *bad trajectory* — the transition from confusion/struggle into frustration →
  boredom → disengagement (the affect-*dynamics* view already in
  [edtech-landscape.md](edtech-landscape.md), from D'Mello & Graesser). The signal is the **onset of the
  downhill slide**, not the struggle itself.

This actually *validates* the productive-struggle→boredom framing while correcting the simpler "any affect
change" version. The hard part — and the real detection target — is distinguishing productive struggle
from the start of the unproductive slide. *(Sound; the confusion-benefit mechanism verified at
abstract level, **paywalled** (Elsevier). The related "productive failure" line — Kapur 2008 — is
mentioned from memory, **not verified this session**.)*

---

## 6. Triggers beyond affect — the "different metric" candidates

If affect is not the exclusive trigger, what else is? The well-studied alternatives — several of them more
*log-detectable* than affect:

- **Impasses / errors** (cognitive) — §1. Strongest evidence, hardest to define in open-ended work.
- **Disengaged behaviors** — gaming the system, wheel-spinning, sustained off-task (Baker; Beck & Gong).
  These are *intervention triggers* distinct from affect, and they are exactly the behavioral detectors
  already covered in [edtech-landscape.md](edtech-landscape.md). For CLUE they may be the most portable
  signal, since they need engagement/behavior patterns rather than a correctness oracle.
- **Student uncertainty** — Forbes-Riley & Litman's spoken-tutoring work detects uncertainty (an
  affect/cognition hybrid) and adapts to it. A cue that sits between §1 and §5. *(Scan-only — title/venue
  seen in search, full text not read.)*
- **Dysfunctional help-seeking** — **Aleven, Stahl, Schworm, Fischer & Wallace (2003)** (a *Review of
  Educational Research* synthesis) and **Aleven, McLaren, Roll & Koedinger (2006)** (a running model of
  help-seeking, ~57 rules capturing effective vs. ineffective behavior) treat **help avoidance** and
  **hint abuse** as intervention-worthy metacognitive signals — the tutor acts on *how* the student seeks
  help, not on their affect or their answer. *(Both citations verified; the 2006 model PDF is open on
  cs.cmu.edu.)*

---

## 7. Adjacent field: struggle detection in *open-ended software* (application tutors)

All of §§1–6 is about learning content. But CLUE is also just **an application**, and there's a separate
literature on detecting when a user is struggling *with the software itself* and offering help — which is
directly relevant because it's built for **open-ended tools, not problem-solving tutors**, exactly our
setting. The signals and (especially) the timing lessons transfer even though the target differs.

- **The classic: Lumière / the Office Assistant.** Horvitz et al. (1998), *The Lumière Project: Bayesian
  User Modeling for Inferring the Goals and Needs of Software Users* (UAI 1998; arXiv:1301.7385, open),
  built Bayesian models that infer a user's **time-varying goals and needs** from action sequences +
  program state, to decide when to offer help. It shipped as the Office '97 **Office Assistant** — i.e.
  **Clippy**. That's a double lesson: the *inference* problem (goals/needs from an action stream) is our
  problem, and its *failure* is the sharpest warning about the assistance dilemma (§3). *(Verified: title,
  venue, arXiv, Office-Assistant lineage; author list from memory.)*
- **The "Clippy effect" is the assistance dilemma in a UI.** The recurring finding is that **the hard part
  is *when*, not *what*** — intervening too early, when a user is merely pausing to think, alienates them.
  This is precisely §3's dilemma, and it's the single most-cited reason proactive help fails. For a CLUE
  helper it's the central risk.
- **Deployed practice: product-analytics "frustration signals."** The web-analytics/UX industry detects
  struggle-with-the-app at scale via cheap heuristics — **rage clicks** (≥3 rapid clicks on one target),
  **dead clicks**, **error clicks**, **thrashed cursor** (FullStory coined these; Glassbox auto-detects
  ~30 such behaviors; Datadog/Amplitude/Heap ship them). **Digital adoption platforms** (Pendo, WalkMe,
  Whatfix, Appcues) then *intervene* with in-app guidance, and newer ones claim to **predict** where users
  will struggle and act before friction. This is the open-ended-application analog of affect/struggle
  detection — heuristic, real-time, log-only. *(Industry/gray-literature, not peer-reviewed; useful as a
  catalogue of cheap log signals, not as evidence they improve learning.)* Overlaps the UX material in
  [techniques.md](techniques.md), and the clickstream-frustration preprint (Joseph 2025) and searcher-
  frustration work (Feild et al.) already in [edtech-landscape.md](edtech-landscape.md) / bibliography.
- **Cutting edge — useful mostly as a *dataset*, less as a method.** *GUIDE* (Yang et al., CVPR 2026;
  arXiv:2603.25864) benchmarks models on **open-ended GUI tasks** (10 desktop apps; 67.5 h; ~1K labeled
  instances) across Behavior-State Detection, Intent Prediction, and **Help Prediction**. The **big caveat
  that limits its methodology for us:** models were fed **only 32 still frames sampled from the screen
  video** (vision-only, zero-shot) — **the keyboard/mouse interaction events were used only to label the
  data, never given to the model.** So its headline "zero-shot struggle detection is weak" result
  (behavior-state acc ~45%; frustration/debugging misclassified as productive action; help-need recall
  <37%) is a verdict on **reading pixels**, not on reading an interaction log — which is what we would
  actually do. If anything it's mild evidence that *screenshots are the wrong representation*, indirectly
  supporting a log-first approach. Two things still transfer: **(a)** injecting a structured state
  representation — the *ground-truth* behavior + intent as text — lifted help prediction up to ~50 pp
  (help-content prediction 23.7% → 73.9%); an **oracle upper bound**, but evidence the bottleneck is the representation,
  not raw model ability ([ai-architecture-question.md](ai-architecture-question.md)). **(b)** The
  **labeled dataset itself is reusable** — it ships the keyboard/mouse event streams + struggle/help
  labels we'd want, so we could test *log* representations on it directly (research-directions 3.1) —
  **contingent on trusting its labels** (Gemini-2.5-Pro-bootstrapped, then human-verified). Models
  evaluated: Gemini-2.5-Pro/Flash, GPT-4o/-mini, Claude-4.5-Sonnet, + open-weight Qwen3-VL-8B /
  InternVL3-8B / InternVideo2.5-8B. *(Full text read this session.)*

**A distinction to keep honest — but it's a *continuum*, not a binary.** As a first cut, application
tutors detect trouble **using the tool** while we care about trouble **with the learning**, and a student
fluent in CLUE's UI can be deeply stuck on the science (and vice versa). But the line is genuinely blurry,
and in a well-designed environment it's blurry *by design*: in a visual-programming activity, using a
**threshold** block requires both the abstract concept (what a threshold *is* and *does*) **and** the
mechanical skill (how to configure that block in the UI) — a single stuck moment can be either, both, or
one masquerading as the other.

**There is established vocabulary for this UI-affordance ↔ learning-goal continuum** — three complementary
lenses, at three levels:

- **Semantic vs. articulatory distance** (Hutchins, Hollan & Norman, 1985 — the "gulfs of execution and
  evaluation"). A difficulty can sit at the **articulatory** end (mapping intent to the physical form of an
  action — *how do I configure this block?*) or the **semantic** end (does the interface even offer a
  concept matching my intention — *what should a threshold do here?*). The threshold example spans both.
  This is the cleanest *decomposition* of the gap.
- **Instrumental genesis** (Vérillon & Rabardel, 1995; Rabardel) — the ed-tech/math-ed framework built for
  exactly this blur: an *artifact* becomes a conceptual *instrument* through use, via intertwined
  **instrumentation** (the tool shaping the learner's concepts) and **instrumentalization** (the learner
  adapting the tool). Here the fusion of technical and conceptual is not noise to separate out — it *is*
  the learning. Most on-point for CLUE.
- **Intrinsic vs. extraneous cognitive load** (Sweller) — the same line from the attention side: concept
  difficulty (intrinsic) vs. load imposed by the interface (extraneous). Caveat that maps to the point
  above: when the tool *is* the representation, "UI overhead" isn't automatically extraneous — it can be
  germane or intrinsic.

**Implication for a CLUE helper:** you can't cleanly route "UI question → app-tutor, concept question →
learning-tutor," because the same document action can be both. The reading of the interaction has to
**place a difficulty on the continuum** (nearer articulatory or nearer semantic) rather than bin it — and
the affordance-end signals are still worth computing because they're cheap and, per instrumental genesis,
often the *observable surface* of a conceptual struggle. So these signals remain a useful *lower bound*
(don't miss a UI breakdown), and their real gift is the **timing discipline** (the Clippy effect) plus a
**menu of cheap log-only struggle signals** on CLUE document events.

---

## 8. Well-structured vs. ill-structured: the design / engineering-tutoring tradition

Stepping back from software to *learning* tasks: nearly everything in §§1–6 (and the block study in §2)
assumes a **well-structured** problem — a convergent answer, a definable solution path, gradable steps.
That fits our **gated** materials (the wildfire-style, per-page-goal activities) well. But a large share of
our materials are the opposite — open-ended document-building — and the intuition that an **"engineering
tutor"** (open-ended building/design, many valid solutions) is a different animal from a **problem-set
tutor** (physics/math) is correct. It's a named distinction with its own literature, and it's the tradition
that fits the **open-ended** end of our range.

**This is the doc's central practical claim: the two ends need different machinery, and don't assume one
transfers.** A helper tuned for the gated wildfire activity (step goals, impasse detection, contingent
hints) will not simply carry over to an open modeling activity, and vice versa — they differ in what a
"good moment to intervene" even *is*.

- **The vocabulary: well- vs. ill-structured problems (Jonassen, 1997).** Well-structured = constrained,
  convergent, a limited rule set within defined parameters. **Ill-structured = multiple solutions and
  solution paths, ambiguous parameters, and uncertainty about which concepts even apply** — and *design*
  problems are the most ill-structured of all. Jonassen argues they need a **different solving model and a
  different instructional model** (constructivist / situated), not the information-processing model behind
  step-based tutoring. This is the formal version of the engineering-vs-problem-tutor distinction, and it's
  the lens for sorting our own materials: the gated wildfire-style activities are well-structured (step
  machinery fits); the open document-building activities are ill-structured (it doesn't).
  *(Verified: ETR&D 45(1), 65–94.)*
- **The tutoring tradition: Schön's reflective design studio (1983/1987).** The canonical account of
  tutoring *open-ended design* is the architecture studio — the **crit**, **reflection-in-action**, a
  "reflective conversation with the situation." The tutor's move is to help the student **reframe the
  problem**, not step them toward a known answer (Schön's Quist–Petra design-review vignette is the classic
  example). That's a fundamentally different *intervention mode* than "detect impasse → give hint": the
  trigger isn't an error, it's a **framing worth challenging**, and the response is a prompt to reflect.
  Harder to detect, and much harder to automate — Schön's mode is deeply human. *(Foundational; concepts
  verified this session, primary books not read.)*
- **The ITS attempt: tutoring in ill-defined domains.** A recognized hard sub-field. Model-tracing /
  ACT-R–style tutors assume a solution path to trace against; design domains have *many controversial
  solutions and no clear way to evaluate one*, so the classic techniques don't transfer. Work exists
  (e.g. Fournier-Viger, Nkambou et al., *Building ITS for Ill-Defined Domains*; recent engineering-design
  ITS), but it's explicitly an open problem. *(Scan-only — chapter PDF located, not read.)*

**The tie-back to our own anchor:** **Betty's Brain** — the environment behind DDCI
([papers/ddci-baker-2024.md](papers/ddci-baker-2024.md)) — is itself an ill-structured *construction* task
(build a causal model; many valid maps). That is almost certainly *why* its detectors are **behavioral and
affective, not correctness-based**: when the task goes open-ended, the field's own move is to drop the
correctness oracle and read behavior/affect instead. That's direct precedent for the §10 conclusion, and it
reinforces §6.

---

## 9. Give, ask, or invite — the *initiative* dimension

A dimension the sections above leave implicit: **who initiates** the help — the system pushes it, or the
student requests it. It is directly studied, it's not a clean win for either side, and it matters to us
specifically because our **wildfire simulation requires the student to ask**.

- **On-demand tends to win on learning.** Razzaq & Heffernan (2010), *Hints: Is It Better to Give or Wait
  to Be Asked?*, ran a randomized comparison in ASSISTments — proactive hints (shown on error) vs.
  hints-on-demand (click a button). **Students learned reliably more on-demand**, concentrated among those
  who ask a lot; no significant difference for those who rarely ask. *(Verified — ITS 2010, open PDF.)*
- **Unsolicited help also carries a motivational cost.** Self-Determination Theory and attribution research
  find help that "just shows up" can **undermine perceived competence and autonomy, signal that the helper
  thinks you can't do it, cut intrinsic motivation, and push learners to avoid challenge** (Sierksma et al.
  2025; "Proactive AI Adoption … When Help Backfires" 2025). A deeper reason for the Clippy effect (§7)
  than interruption alone. Both results *support* the wildfire design.
- **But pure on-demand has a systematic blind spot: help avoidance.** The best-documented finding in
  help-seeking research is that **the students who most need help are the least likely to ask**
  (Karabenick; Aleven et al., §6) — help-seeking is a metacognitive skill many lack, and it predicts
  learning about as strongly as cognitive performance does. So requiring the ask **under-serves exactly the
  struggling students an intervention should catch** — the quietly-stuck student who never clicks "help" is
  invisible.
- **The field's resolution isn't binary — it's *invite*.** Between push and pull sits **mixed-initiative**
  (Bunt, Conati & McGrenere): keep the student in control but let the system *offer*. The practical version
  is **detect help-avoidance and surface a gentle, dismissable "want a hint?"** ("Avoiding Help Avoidance";
  Aleven's help-seeking tutor) — **proactive invitation, user-controlled acceptance** — reaching the
  non-askers without forcing help.

**Two consequences that reshape our problem:**

- **On-request help changes *what we mine the log for.*** If the default is on-demand, then for most
  interactions the target is **not** "detect the moment to intervene" — it's **"characterize the student's
  current goal, difficulty, and state well enough to give the best advice *at the moment they ask*."** A
  different, arguably more tractable meaning-extraction problem: situation-understanding on demand, not
  continuous trigger-monitoring. Much of the "when to intervene" machinery in this doc applies only to the
  *proactive* slice.
- **…except the invite layer drags impasse detection back in.** To catch help-avoiders you must detect
  **"stuck and not asking,"** which *is* the impasse/struggle-detection problem (§1), just aimed at a
  narrower question. So the middle path doesn't escape the hard detection problem — it **relocates** it, and
  only works where "stuck" is detectable: the **gated** materials (the wildfire sim, with per-page goals),
  not the open-ended ones.

**Net for the wildfire activity:** require-the-ask is well-supported (learning, motivation, non-annoyance) —
keep it as the default. The evidence-based upgrade is *not* switching to proactive help, but adding a
**detect-stuck-then-invite** layer on top, and only on gated materials where the page goal makes "stuck"
detectable.

---

## 10. What this means for us

- **First, sort the material — the approach follows from its structure.** Our materials range from
  **gated/well-structured** (the wildfire simulation with per-page goals: clear steps and checkable
  targets) to **open-ended/ill-structured** (open document-building: many valid paths, no natural
  checkpoints). These need **different intervention machinery**, and a helper built for one likely won't
  transfer to the other — the gated end can use step-based impasse/contingency detection (§§1–4), the
  open-ended end needs the design-tutoring + behavioral/affective approach (§8). Resisting the "one
  solution fits both" reflex is the doc's main practical warning. *(This is a spectrum, and many
  activities mix both — e.g. an open modeling task nested inside a gated activity.)*
- **"When to intervene" deserves to be its own question in the problem framing**, not silently collapsed
  into "detect affect." See [finding-meaning-problem.md](finding-meaning-problem.md) (use-case 2).
- **The help-initiative choice (§9) reframes the whole meaning-detection target.** If help is
  **on-request** (as in the wildfire sim), then most of the time we are *not* looking in the log for a
  moment to intervene — we're **reading the situation to give the best advice when asked**. "When to
  intervene" only governs the *proactive* slice. The one place the two converge: detecting a **stuck
  help-avoider** to *invite* help is impasse detection again (§1), so it doesn't dodge the hard problem —
  and it's only feasible on the gated materials.
- **Affect-change is a legitimate trigger but probably not the primary one.** The strongest single finding
  points at **impasse / struggle-quality** (cognitive readiness); the most **log-tractable** triggers are
  **behavioral disengagement** signals (gaming, wheel-spinning, off-task). Affect is real but, per §5,
  needs the *trajectory* framing to be actionable, and it is the hardest of the three to detect from logs
  (see [papers/physics-playground-kai-2015.md](papers/physics-playground-kai-2015.md): interaction-based
  affect A′ ≈ 0.63).
- **DDCI is already a "when-to-intervene" system** — its detectors exist precisely to flag the moment worth
  a teacher's attention ([papers/ddci-baker-2024.md](papers/ddci-baker-2024.md)). The intervention-timing
  literature here is the *why* behind that design.
- **For the open-ended materials, the design/ill-structured tradition (§8) fits better than the
  problem-solving studies most of this doc draws on.** There, Schön-style *reframing/reflection* is closer
  to what a good helper does than impasse-then-hint — but that mode is the hardest to detect triggers for
  and to automate. And the precedent (Betty's Brain going behavioral/affective when it went open-ended)
  says that when the correctness oracle disappears, reading **behavior and affect** is the field's
  fallback — which loops back to why affect and disengagement, weak as they are, keep mattering. *(For the
  gated materials, by contrast, the correctness-and-impasse machinery is available and probably the first
  thing to try.)*
- **The domain mismatch cuts a specific way — for the open-ended end.** The blocker there isn't a missing
  correctness signal (that's the author's choice) — it's the missing **per-step structure**: an artifact
  built up over time has no natural checkpoints, so the best-evidenced trigger (impasse) is the *hardest*
  to locate, while affect-trajectory and behavioral-disengagement triggers — weaker in the tutoring
  literature — may be the *most* portable. That inversion matters: the strongest result in the literature
  may be the least usable for our open-ended materials, and vice versa. **Genuine open question**, not a
  settled recommendation.
- **Two concrete build implications.** (a) To use the impasse/goal-achievement triggers at all, we'd
  likely need **authors to formalize goals** and **break the work into evaluation points** — and even then,
  detecting goal achievement in an open document is harder than anything in the problem-solving research.
  (b) The **application-tutor** signals (§7) give us a cheap, deployable-today menu (rage/dead/error
  clicks, hesitation, undo-thrash on CLUE document events) plus the **Clippy-effect timing discipline** —
  worth computing as a baseline even if they only catch tool-struggle, not learning-struggle.

---

## References

*Full details + accessibility in [the shared bibliography](bibliography.md). The doc-specific
verification status of each is summarized in the [Verification note](#verification-note) below.*

- [VanLehn et al. (2003) — impasse-driven learning](bibliography.md#vanlehn-2003) 🔒
- [Wood, Bruner & Ross (1976) — scaffolding](bibliography.md#wood-1976) 🔒
- [Koedinger & Aleven (2007) — assistance dilemma](bibliography.md#koedinger-2007) 🔒
- [Lepper, Drake & O'Donnell-Johnson (1997) — expert-tutor scaffolding](bibliography.md#lepper-1997) 🔒
- [D'Mello et al. (2014) — productive confusion](bibliography.md#dmello-2014) 🔒
- [Aleven et al. (2003) — help seeking & help design](bibliography.md#aleven-2003) 🔒
- [Aleven et al. (2006) — meta-cognitive tutoring / help seeking](bibliography.md#aleven-2006) 🟢
- [Forbes-Riley & Litman (2008) — responding to uncertainty](bibliography.md#forbes-riley-2008) 🔒⚠️
- [Horvitz et al. (1998) — Lumière](bibliography.md#horvitz-1998) 🟢
- [Yang et al. (2026) — GUIDE](bibliography.md#yang-2026) ✅
- [Product-analytics & digital-adoption gray literature](bibliography.md#product-analytics-graylit) ⚠️ — cited as a signal catalogue only.

*§8's design / ill-structured tutoring tradition:*
- [Jonassen (1997) — well- vs. ill-structured problem-solving](bibliography.md#jonassen-1997) 🔒
- [Schön (1983 / 1987) — the reflective practitioner](bibliography.md#schon-1983) 🔒
- [Fournier-Viger, Nkambou et al. — ITS for ill-defined domains](bibliography.md#fournier-viger) 🟢⚠️

*§7's UI-affordance ↔ learning-goal continuum:*
- [Hutchins, Hollan & Norman (1985) — semantic vs. articulatory distance](bibliography.md#hutchins-1985) 🟢
- [Vérillon & Rabardel (1995) — instrumental genesis](bibliography.md#verillon-1995) 🔒
- [Sweller — cognitive load theory](bibliography.md#sweller) ⚠️

*§9's help-initiative (give / ask / invite) studies:*
- [Razzaq & Heffernan (2010) — give or wait to be asked?](bibliography.md#razzaq-2010) 🟢
- [Aleven et al. (2016) — help helps, but only so much](bibliography.md#aleven-2016) 🔒⚠️ — anchors the [Karabenick](bibliography.md#karabenick) "those who need help most ask least" phenomenon.
- [Bunt, Conati & McGrenere (2007) — mixed-initiative customization](bibliography.md#bunt-2007) 🔒⚠️
- [Sierksma et al. (2025) — consequences of receiving help](bibliography.md#sierksma-2025) 🔒⚠️ — grounded in [Self-Determination Theory (Ryan & Deci)](bibliography.md#ryan-deci).
- Scan-only arXiv: [Avoiding Help Avoidance](bibliography.md#avoiding-help-avoidance-2020) 🟢⚠️ · [When Help Backfires](bibliography.md#help-backfires-2025) 🟢⚠️

*Mentioned from memory, not re-verified (verify before leaning on):* [Lepper & Woolverton (2002)](bibliography.md#lepper-2002) · [Graesser, Person & Magliano (1995)](bibliography.md#graesser-1995) · [Kapur (2008)](bibliography.md#kapur-2008).

## Verification note

The backbone citations (§§1–4, 6) — VanLehn et al. 2003, Wood/Bruner/Ross 1976, Koedinger & Aleven 2007,
Lepper/Drake/O'Donnell-Johnson 1997, Aleven et al. 2003, Aleven/McLaren/Roll/Koedinger 2006, and D'Mello
et al. 2014 — were searched and confirmed this session (metadata/abstract level; the 2006 help-seeking
model is open full text). Forbes-Riley & Litman is scan-only. Lepper & Woolverton, Graesser/Person/
Magliano, and Kapur are memory-only and flagged in-line. For §7: **Lumière** (Horvitz et al. 1998) is
verified at title/venue/arXiv level (author list from memory); **GUIDE** (Yang et al., CVPR 2026,
arXiv:2603.25864) was **read in full this session** (authors, dataset, tasks, and the two headline results
confirmed — an earlier "Frustration ≈ 0.07" figure from a search snippet was **not** found in the paper
and has been removed); the product-analytics / digital-adoption
material is **industry gray-literature**, cited as a catalogue of cheap log signals, not as evidence of
learning benefit. The §7 continuum vocabulary (Hutchins/Hollan/Norman 1985 semantic-vs-articulatory
distance; Vérillon & Rabardel 1995 / Rabardel instrumental genesis; Sweller cognitive load) was verified
at concept/metadata level this session; primaries not read. For §8: Jonassen 1997 verified (venue/vol/
pages + the well-vs-ill-structured distinction); Schön verified at concept level (books not read);
the ITS-for-ill-defined chapter is scan-only. For §9: **Razzaq & Heffernan 2010** verified
(title/venue/finding, open PDF); Bunt/Conati/McGrenere, Sierksma 2025, and Aleven 2016 verified at
metadata/abstract level (not read); the Karabenick origin paper is not pinned (concept only); the two
arXiv items ("Avoiding Help Avoidance", "When Help Backfires") are scan-only. Nothing here has been read in
full except GUIDE; before any of these becomes load-bearing in a presentation, fetch the primary text. This
doc makes **no new empirical claim** — it synthesizes established findings to frame the intervention-timing
decision.
