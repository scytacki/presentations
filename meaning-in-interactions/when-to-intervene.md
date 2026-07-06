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
problems, algebra steps). CLUE-style open-ended work is different in two ways worth separating:

- **Correctness is possible but optional — it's the author's choice.** A CLUE activity *can* carry a
  correctness signal (a modeling goal, an expected answer); whether it has one is up to the activity
  author. So "no correctness signal" is not intrinsic to CLUE — it's a design decision.
- **The *per-step* structure is the deeper gap.** Even when an author sets a goal, the student builds up
  **one document over time** rather than submitting a sequence of discrete, individually-gradable steps.
  There aren't natural checkpoints to evaluate the way a problem-solving tutor evaluates each step, so
  constructs like "impasse" and "error" are hard to *locate in time* even when correctness is defined.

A likely research consequence (picked up in §8): we may need **authors to formalize goals** so an AI
helper can use them, and to **break the work into evaluation points**. And even then, *detecting when a
student has achieved a formalized goal* will likely be harder than anything in this problem-solving
research. This mismatch runs through [edtech-landscape.md](edtech-landscape.md) too.

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
studied expert tutors helping young children with a block-construction task and named six tutoring
functions (recruitment, reduction of degrees of freedom, direction maintenance, marking critical
features, **frustration control**, demonstration). The follow-on **contingent shift rule** (Wood and
colleagues) is a concrete when/how-much policy: **increase control after the child fails a step, decrease
it after success**, and *fade* support over time so responsibility transfers to the learner. Contingent-
then-faded support repeatedly comes out as the most effective pattern.

Why it matters here: this is an explicit, testable **intervention-timing rule** derived from watching
human tutors, and its trigger is *success/failure on the last step* — again behavioral/cognitive, not
affective (though "frustration control" shows affect was in scope from the start). *(Solid — foundational;
**paywalled** (Wiley), metadata + the six functions verified.)*

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
  frustration work (Feild et al.) already in [edtech-landscape.md](edtech-landscape.md) / papers-to-obtain.
- **Cutting edge — and a direct warning for the GenAI path.** *GUIDE* (arXiv:2603.25864, 2026) benchmarks
  models on **open-ended GUI tasks** across Behavior-State Detection, Intent Prediction, and **Help
  Prediction**, and reports that current multimodal LLMs are **near-blind to struggle** — they read
  repeated clicks, hesitation, and undo as *productive progress* (Frustration ≈ 0.07, Debugging ≈ 0.04).
  If a frontier model can't spot struggle in a general GUI, expecting it to spot learning-struggle in CLUE
  logs zero-shot is optimistic — more evidence for the serialization/representation problem in
  [ai-architecture-question.md](ai-architecture-question.md). *(Scan-only — very recent preprint, abstract
  seen in search, not read; verify before leaning on it.)*

**The key distinction to keep honest:** application tutors detect trouble **using the tool**; we care about
trouble **with the learning**. A student fluent in CLUE's UI can be deeply stuck on the science, and vice
versa. So these signals are a *lower bound* — necessary-ish plumbing (you don't want to miss a UI
breakdown) but not sufficient for the pedagogical judgment. Their real gift to us is the **timing
discipline** (the Clippy effect) and a **menu of cheap log-only struggle signals** we could compute on CLUE
document events.

---

## 8. What this means for us

- **"When to intervene" deserves to be its own question in the problem framing**, not silently collapsed
  into "detect affect." See [finding-meaning-problem.md](finding-meaning-problem.md) (use-case 2).
- **Affect-change is a legitimate trigger but probably not the primary one.** The strongest single finding
  points at **impasse / struggle-quality** (cognitive readiness); the most **log-tractable** triggers are
  **behavioral disengagement** signals (gaming, wheel-spinning, off-task). Affect is real but, per §5,
  needs the *trajectory* framing to be actionable, and it is the hardest of the three to detect from logs
  (see [papers/physics-playground-kai-2015.md](papers/physics-playground-kai-2015.md): interaction-based
  affect A′ ≈ 0.63).
- **DDCI is already a "when-to-intervene" system** — its detectors exist precisely to flag the moment worth
  a teacher's attention ([papers/ddci-baker-2024.md](papers/ddci-baker-2024.md)). The intervention-timing
  literature here is the *why* behind that design.
- **The domain mismatch cuts a specific way.** The blocker isn't that CLUE *can't* have a correctness
  signal (that's the author's choice) — it's the missing **per-step structure**: a document built up over
  time has no natural checkpoints, so the best-evidenced trigger (impasse) is the *hardest* to locate,
  while the affect-trajectory and behavioral-disengagement triggers — weaker in the tutoring literature —
  may be the *most* portable. That inversion matters: the strongest result in the literature may be the
  least usable for us, and vice versa. **Genuine open question**, not a settled recommendation.
- **Two concrete build implications.** (a) To use the impasse/goal-achievement triggers at all, we'd
  likely need **authors to formalize goals** and **break the work into evaluation points** — and even then,
  detecting goal achievement in an open document is harder than anything in the problem-solving research.
  (b) The **application-tutor** signals (§7) give us a cheap, deployable-today menu (rage/dead/error
  clicks, hesitation, undo-thrash on CLUE document events) plus the **Clippy-effect timing discipline** —
  worth computing as a baseline even if they only catch tool-struggle, not learning-struggle.

---

## References

*Verify-before-cite; accessibility and verification level noted. Most were verified this session at
abstract/metadata level via web search — full texts not read unless stated.*

- **VanLehn, K., Siler, S., Murray, C., Yamauchi, T., & Baggett, W. B. (2003).** Why Do Only Some Events
  Cause Learning During Human Tutoring? *Cognition and Instruction*, 21(3). **Paywalled (Taylor & Francis;
  ERIC EJ675271).** Impasse-driven learning from ~125 h of expert physics tutoring. *(Venue/issue + first
  two authors search-verified; remaining author list from memory.)*
- **Wood, D., Bruner, J. S., & Ross, G. (1976).** The Role of Tutoring in Problem Solving. *Journal of
  Child Psychology and Psychiatry*, 17(2), 89–100. **Paywalled (Wiley); PDFs circulate.** Origin of
  "scaffolding"; six tutoring functions incl. frustration control. *(Metadata + functions verified.)*
- **Koedinger, K. R., & Aleven, V. (2007).** Exploring the Assistance Dilemma in Experiments with Cognitive
  Tutors. *Educational Psychology Review*, 19, 239–264. **Paywalled (Springer); ERIC EJ785065.** *(Verified.)*
- **Lepper, M. R., Drake, M. F., & O'Donnell-Johnson, T. (1997).** Scaffolding Techniques of Expert Human
  Tutors. In K. Hogan & M. Pressley (Eds.), *Scaffolding Student Learning: Instructional Approaches and
  Issues* (pp. 108–144). Brookline Books. **Book chapter (not online).** *(Full citation verified.)*
- **D'Mello, S., Lehman, B., Pekrun, R., & Graesser, A. (2014).** Confusion Can Be Beneficial for Learning.
  *Learning and Instruction*, 29, 153–170. **Paywalled (Elsevier).** Productive confusion — beneficial if
  induced/regulated/resolved. *(Verified at abstract level.)*
- **Aleven, V., Stahl, E., Schworm, S., Fischer, F., & Wallace, R. (2003).** Help Seeking and Help Design
  in Interactive Learning Environments. *Review of Educational Research*, 73(3), 277–320. **Paywalled
  (SAGE); ERIC EJ782605.** *(Verified.)*
- **Aleven, V., McLaren, B., Roll, I., & Koedinger, K. (2006).** Toward Meta-cognitive Tutoring: A Model of
  Help Seeking with a Cognitive Tutor. *International Journal of Artificial Intelligence in Education*,
  16(2), 101–128. **Open PDF (cs.cmu.edu).** Help-avoidance / hint-abuse as intervention signals. *(Verified.)*
- **Forbes-Riley, K., & Litman, D. (2008).** Responding to Student Uncertainty During Computer Tutoring.
  *ITS 2008* (Springer LNCS). **Paywalled.** *(Scan-only — title/venue from search, not read.)*
- **Horvitz, E., Breese, J., Heckerman, D., Hovel, D., & Rommelse, K. (1998).** The Lumière Project:
  Bayesian User Modeling for Inferring the Goals and Needs of Software Users. *UAI 1998*, 256–265.
  **Open (arXiv:1301.7385).** Basis for the Office '97 Office Assistant ("Clippy"). *(Title/venue/arXiv +
  Office-Assistant lineage verified; author list from memory.)*
- **GUIDE: A Benchmark for Understanding and Assisting Users in Open-Ended GUI Tasks (2026).**
  arXiv:2603.25864. **Open (arXiv).** MLLMs near-blind to struggle in open-ended GUIs (Frustration ≈ 0.07).
  *(Scan-only — abstract seen in search, not read; authors not captured.)*
- *Industry / gray-literature (not peer-reviewed), cited as a signal catalogue only:* product-analytics
  frustration signals — rage / dead / error clicks, thrashed cursor (FullStory, Glassbox, Datadog,
  Amplitude, Heap); digital adoption platforms (Pendo, WalkMe, Whatfix, Appcues).
- *Mentioned from memory, NOT re-verified this session (verify before leaning on):* Lepper & Woolverton
  (2002), "The wisdom of practice"; Graesser, Person & Magliano (1995), naturalistic tutoring dialogue
  patterns; Kapur (2008), "Productive Failure."

## Verification note

The backbone citations (§§1–4, 6) — VanLehn et al. 2003, Wood/Bruner/Ross 1976, Koedinger & Aleven 2007,
Lepper/Drake/O'Donnell-Johnson 1997, Aleven et al. 2003, Aleven/McLaren/Roll/Koedinger 2006, and D'Mello
et al. 2014 — were searched and confirmed this session (metadata/abstract level; the 2006 help-seeking
model is open full text). Forbes-Riley & Litman is scan-only. Lepper & Woolverton, Graesser/Person/
Magliano, and Kapur are memory-only and flagged in-line. For §7: **Lumière** (Horvitz et al. 1998) is
verified at title/venue/arXiv level (author list from memory); **GUIDE** (arXiv:2603.25864) is scan-only
(a very recent preprint — verify before citing its numbers); the product-analytics / digital-adoption
material is **industry gray-literature**, cited as a catalogue of cheap log signals, not as evidence of
learning benefit. Nothing here has been read in full; before any of these becomes load-bearing in a
presentation, fetch the primary text. This doc makes **no new empirical claim** — it synthesizes
established findings to frame the intervention-timing decision.
