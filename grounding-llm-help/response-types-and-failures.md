# When the LLM Helps a Student in a Simulation — Response Types and How They Go Wrong

This is the first of two companion documents. This one is about **what kinds of help an LLM
would give a student working in one of our simulations, and the specific ways each kind can be
wrong.** The second document reviews the research literature on *grounding* LLMs in facts —
the techniques that would prevent the failures catalogued here.

The audience and framing are the same as our other presentations: people building and studying
classroom software. So the examples are concrete and tied to a real simulation, not abstract
claims about AI.

---

## The setup: a simulation is a model, and a model is a simplification

We want to give students an LLM helper inside an activity built around a simulation. The four
ways that help could arrive:

- **Student asks a question** ("why did it go farther?")
- **Hints / nudges** ("you've changed mass three times — what about trying the angle?")
- **Feedback on actions** ("you raised the angle and it landed shorter — interesting")
- **Explaining a result** ("the higher arc means more time in the air")

And we said up front that we want the help "founded in scientific facts." The central
complication is that **there is no single thing called "the facts" here.** There are at least
four reference points the help could be checked against, and they do not always agree.

### The four truths

| Truth | A response is "correct" when it… | The signature failure |
|---|---|---|
| **The simulation's model** | matches what the sim's code actually computes | the LLM tells a physics story the sim doesn't implement |
| **The curriculum / activity** | is on-topic, at the right level, and serves the learning goal | true, but out of scope or ahead of where the student is |
| **Scientific consensus** | matches established real-world science | reinforces a misconception, or invents a fact |
| **Pedagogy** | is good *teaching* | gives away the answer, or agrees just to be agreeable |

The reason this matters — and the reason this document exists — is that **our simulations are
deliberately simplified models, so the first truth and the third truth genuinely conflict.**

Take a standard projectile launcher. To make the physics learnable, it almost certainly
assumes **no air resistance** and **constant gravity**. In that sim, a feather and a cannonball
launched identically follow the *exact same trajectory*. That is **true in the simulation** and
**false in the real world.** A student who asks "what if it were a feather?" can be given a
scientifically impeccable answer ("a feather would flutter and fall short") that is *wrong about
the thing on their screen* — and when they run it and see no difference, they distrust either
the sim, the AI, or both.

This is the crux — and it is **not a problem to be eliminated.** We usually *want* the student to
understand that the model is a simplification, so the best response is often one that
**deliberately differs from the simulation.** What matters is that the difference is always
**named and framed**: the response should never present real-world science as if the sim were
simply broken, nor present the sim's behavior as if it were the whole of reality. So neither the
model nor real-world science is the single "primary" truth. The invariant is narrower and more
useful: **every response must be contextualized against the simulation's simplifications.** A
response that differs from the sim is fine — often ideal — as long as it tells the student *how*
and *why* it differs.

### Two ways the simplification bites

There are actually two different mismatches hiding under "the sim is a simplification," and they
need different handling:

- **Omitted effect** — the sim leaves out something that genuinely matters at this scale (air
  resistance on a feather). Sim and reality *disagree*, and the disagreement is visible on
  screen. (Scenario 1.)
- **Negligible effect** — the student or the LLM raises something real that has *no meaningful
  effect* at the sim's scale (the Moon's pull, the Coriolis effect on a short throw). Sim and
  reality *agree* that the effect exists; it just doesn't matter here. (Scenario 5.)

The second is subtler, because the claim is *true in both worlds* — there is no factual error to
catch at all. The failure is one of **relevance and scale**, and the right handling is as much
pedagogical as it is factual.

> **The reference simulation used throughout.** A 2-D projectile launcher with three
> parameters — **mass**, **launch angle**, and **ground friction** (how far the projectile
> slides after it lands). The student sets parameters, presses **Run**, and sees the trajectory
> plus a "distance traveled" readout. Idealized physics: gravity is constant, there is **no air
> resistance**, and mass therefore does **not** affect the flight path (only the post-landing
> slide, via friction). Every scenario below lives in this sim.

---

## Six kinds of claim (the failure depends on the claim, not the help mode)

Whether the help arrives as an answer, a hint, feedback, or an explanation, underneath it the
LLM is making one of six kinds of claim. *How* a response can be wrong depends on which kind it
is and which truth it should have been grounded to.

| Claim type | Example in our sim | Should be grounded to | How it goes wrong |
|---|---|---|---|
| **Descriptive** — what is / was | "You set mass to 10." | live sim state | misreads or uses stale state |
| **Causal** — why it happened | "It went farther because you raised the angle." | the sim's model | confabulates a mechanism the model doesn't have |
| **Predictive** — what if | "A heavier ball will fall faster." | the sim's model | predicts real-world (or just wrong) behavior the sim won't show |
| **Conceptual** — a general fact | "Gravity pulls all masses equally." | scientific consensus | hallucination / reinforces a misconception |
| **Procedural** — what to do next | "Drag the air-density slider." | the sim's affordances + activity | suggests a control that doesn't exist, or goes off-task |
| **Evaluative** — judging the student | "Great experiment design!" | pedagogy | sycophancy; gives away the answer; mis-assesses |

A single helpful-sounding paragraph often chains several of these — a descriptive claim, then a
causal one, then an evaluative one — which is part of why errors are hard to spot: the true
parts lend credibility to the false part.

**One failure cuts across all six and deserves its own name: the *relevance / scale* failure** —
a claim that is true, on-topic, and yet has no meaningful effect at the scale the simulation
operates (the Moon's gravity, the Coriolis effect on a short throw). It passes *every* factual
check — which is exactly what makes it dangerous — and its correct handling is pedagogical, not
corrective. Scenario 5 develops it.

---

## Five scenarios, developed in full

Each scenario gives the student's action, the LLM's response, *which* truth it violates, why
it's hard to catch, and — briefly — what would catch it (the second document develops the
"catch it" column properly).

### Scenario 1 — The simulation disagrees with reality, and the LLM sides with reality

**Help mode:** student asks a question. **Claim type:** predictive / conceptual.

The student has the launcher set to mass 5, angle 45°. They type:

> *"What if I launched a feather instead? Would it go as far?"*

The LLM answers, grounded beautifully in real science:

> *"Probably not! A feather has a lot of air resistance for its weight, so it would slow down
> quickly and flutter to the ground much sooner than a denser object."*

**The problem.** This sim has no air resistance and mass doesn't affect the trajectory. If the
student now sets a tiny mass and runs it, **the feather follows the identical arc** to the
cannonball. The LLM was right about the world and wrong about the model on the screen.

**Which truth:** correct on *scientific consensus*, but **uncontextualized** against the
*simulation's model*. The failure isn't that the LLM sided with reality — siding with reality
can be the *right* call. The failure is that it gave the real-world answer *without framing it as
different from the sim*, so the student can't reconcile it with what they'll see on screen.

**Why it's hard to catch.** Nothing about the answer is "a hallucination" in the usual sense —
it's textbook-correct. A fact-checker pointed at Wikipedia would *approve* it. The error is only
visible if the checker knows the simulation's assumptions.

**What would catch it.** First the grounding requirement: the LLM needs the sim's modeling
assumptions in context ("this model ignores air resistance; mass does not affect flight").
Beyond that, there is **no single "ideal" response** — what's ideal depends on how much
back-and-forth the project wants:

- **Direct (answer-and-move-on).** Deliver the contextualized answer in one shot: *"In this
  simulation there's no air, so a feather would go exactly as far. In the real world, air
  resistance would slow it down a lot — that's something this model leaves out."* The student
  gets a correct, framed answer and continues.
- **Socratic (discover-by-dialogue).** Open with a question instead: *"What's different about a
  feather compared to what you've been launching?"* If the student lands on weight (or air),
  invite them to *try* it in the sim — then surface that the sim is a simplification that may not
  capture a feather faithfully: *"It went just as far here — but does that match what you'd expect
  in real life? What might this simulation be leaving out?"* That turns the sim-vs-reality gap
  into the lesson itself, and can open a discussion of *what's missing from the model.*

Both are grounded in all four truths and both **name the difference between sim and reality.**
They differ only in *interaction depth* — which is a project-level design choice, not a property
of the LLM (see the synthesis below).

---

### Scenario 2 — A confident, plausible, invented mechanism

**Help mode:** explaining a result. **Claim type:** causal.

The student raises mass from 5 to 10, keeps angle fixed, runs it, and sees the projectile slide
a bit *less* far after landing (more mass → more friction force → shorter slide, the way this
sim happens to compute it). They ask "why did it stop sooner?" The LLM responds:

> *"Heavier objects have more momentum, so they push through the ground friction and... actually,
> the heavier ball sinks in more, creating more drag, which is why it stopped sooner."*

**The problem.** The sim doesn't model momentum, sinking, or drag. It applies a friction
deceleration that scales with mass. The LLM produced a fluent, sciencey, partly self-contradictory
story ("more momentum to push through" then "more drag") that lands on the right *outcome* by a
wrong *mechanism* — and "sinking in" is a real-world-plausible misconception being minted on the
spot.

**Which truth:** gets the *outcome* right but violates the *simulation's model*, and quietly
violates *scientific consensus* by inventing "sinking" physics.

**Why it's hard to catch.** The final claim matches what the student saw, so it feels confirmed.
Causal explanations are the most dangerous claim type precisely because the observable outcome
"validates" whatever story precedes it.

**What would catch it.** The LLM would need the actual rule the sim used (friction force scales
with mass). Absent that, the safe move is epistemic humility about mechanism: describe *what*
changed and ask the student to investigate *why*, rather than assert a mechanism it can't see.

---

### Scenario 3 — Sycophancy, and the subtler trap of being *correctly* unhelpful

**Help mode:** student asks a question. **Claim type:** evaluative / conceptual.

The student, before running anything, types:

> *"Heavier things always fall faster, so a bigger mass will land first, right?"*

**Failure 3a — sycophancy.** The LLM, tuned to be agreeable, says: *"Exactly right — great
intuition!"* It has now confirmed a classic misconception (violates *scientific consensus* and,
in this no-air sim, the *model* too). Sycophancy — agreeing with the user's stated belief — is a
well-documented LLM tendency and is especially damaging in a learning setting where the whole
point is to surface and test wrong intuitions.

**Failure 3b — correct but pedagogically wrong.** A "safer" LLM instead says: *"Actually, no —
without air resistance all masses fall at the same rate, so they'd land at the same time."* This
is **factually correct against all four truths** and still a failure for an inquiry activity:
it hands over the answer the simulation was designed to let the student *discover*. The student
never runs the experiment; the learning objective evaporates.

**Which truth:** 3a violates *science* and *model*; 3b satisfies *science*, *model*, and
*curriculum* but violates *pedagogy*.

**Why it's hard to catch.** 3b is the trap: every factual check passes. You can only see the
failure if "correct" includes "preserves the student's opportunity to investigate." This is why
pedagogy has to be one of the truths and not an afterthought.

**What would catch it.** A pedagogical policy in the system prompt ("don't confirm or deny
predictions about something the student can test in the sim; redirect them to run it"), plus
detection of leading questions. The grounded-*and*-pedagogical response: *"That's a great thing
to test — set up two masses and run them. What do you predict, and what will you measure?"*

---

### Scenario 4 — The phantom affordance

**Help mode:** hint / nudge. **Claim type:** procedural.

The student is stuck. The LLM offers a hint:

> *"To explore air resistance, try lowering the **air density** slider and watch how the range
> changes."*

**The problem.** There is no air-density slider — this sim has mass, angle, and friction, full
stop. The LLM hallucinated an affordance, probably because air-resistance is so strongly
associated with projectile-motion content in its training data. The student hunts for a control
that doesn't exist and concludes they're doing something wrong.

**Which truth:** violates the *simulation's affordances* (procedural), and dangles a concept
(air resistance) that's *off-curriculum* for this activity.

**Why it's hard to catch.** The hint is on-topic and pedagogically well-shaped — it's the kind
of thing a good tutor *would* say if the control existed. The error is in a factual claim about
the UI, which generic content-correctness checks don't examine.

**What would catch it.** Give the LLM the actual list of controls/affordances for this specific
activity and constrain procedural suggestions to that list. This is one of the more tractable
groundings — the set of real controls is small, known, and machine-readable.

---

### Scenario 5 — The true-but-inert tangent

**Help mode:** student asks a question. **Claim type:** conceptual / predictive, failing on
*relevance and scale*.

The student has gotten the hang of the launcher and, thinking hard, asks something genuinely
sophisticated:

> *"Doesn't the Earth's rotation curve the path a little too? Like the Coriolis effect?"*

This is the case where the usual failure framing breaks down: the student's claim is **true**,
and **on-topic**, and reflects a real and advanced insight. There are two opposite ways to get it
wrong.

**Failure 5a — the dismissive shutdown.** The LLM, correctly judging the magnitude, says: *"No,
the Coriolis effect has no effect here."* Factually defensible at this scale — and
pedagogically destructive. It just told a student who made a genuinely advanced connection (one
they're probably proud of) that their idea doesn't count. That's how you teach a student to stop
volunteering ideas.

**Failure 5b — validating into confusion.** The LLM says: *"Yes! The Coriolis effect deflects a
projectile to the right in the Northern Hemisphere."* True in general, but at this sim's scale
the deflection is unmeasurable and the sim doesn't model it anyway. The student now hunts for a
curve that isn't there — a cousin of Scenario 1, except here *both worlds agree the effect is
real*; it's simply negligible.

**Which truth:** correct on *scientific consensus*, but violating *relevance to the sim's scope
and scale*, and — depending on which way it fails — *pedagogy*.

**Why it's hard to catch.** There is no factual error anywhere to detect. Every truth-checker
passes both 5a and 5b. The only thing that distinguishes a good response is judgment about
*magnitude* and *teaching* — neither of which is a fact about the world.

**The good response** does three things in order: **affirm** the insight ("nice catch — that's a
real effect and a sharp thing to notice"), **locate its scale** ("for a throw this short it's far
too small to see; it shows up for things like artillery over kilometers, or weather systems"),
and **bring them back** ("for what we're exploring here, the launch angle and speed are what
dominate"). It honors the student without letting the tangent derail the activity.

**The twist that points past "facts":** the *right* response here depends on **who is asking.** If
the LLM knows this student already has the core concept solid — and the curriculum designer or
teacher has opted into enrichment — then the very same question is an invitation to go *deeper*,
not something to redirect. The correct move is contingent on a model of the student and on the
activity's intent. Which leads to the input the four truths don't cover.

---

## Beyond the four truths: a fifth input — the student's mental model

Scenario 5 exposes something the four truths can't decide on their own. Knowing the *facts*
(Coriolis is real; it's negligible at this scale; it's outside the activity) does **not** tell
you whether to redirect the student or indulge the tangent. That decision depends on **what this
particular student already understands** and on **what the activity wants to happen** right now.

So a genuinely useful helper combines the facts with a running **model of the student's
understanding** — informally, "what is this student's current mental model?" The same is true
across the other scenarios:

- Whether to *tell* the student the answer (Scenario 3b) or hold back depends on whether they've
  had a real chance to investigate.
- How much to *contextualize* the sim-vs-reality gap (Scenario 1) depends on whether the student
  already grasps that the model is simplified.
- Whether a sophisticated tangent (Scenario 5) is a distraction or the next step depends on
  mastery the student has already shown.

This is, strictly, a layer *beyond* "grounding in scientific facts" — it's about how to *deploy*
grounded facts for a specific learner. It's likely out of scope for the first cut of this work,
but it's worth naming, because without it "always redirect tangents" and "always contextualize"
harden into rigid rules that will sometimes be wrong. And the raw material for such a model is
already in front of us: **the student's own interaction history** — the sequence of parameter
changes, runs, results, and questions — *is* evidence of what they understand.

A fair way to state the full picture: the LLM should combine **four truths** (sim model,
curriculum, science, pedagogy) with a **fifth input** (a model of the student) when deciding how
to respond. This document is mostly about the first four; the fifth is flagged here so the
picture is complete.

---

## What the five scenarios show, together

A few patterns worth pulling out, because they shape what the second (literature) document needs
to deliver:

1. **The discipline isn't "side with the sim" — it's "always contextualize against the sim."**
   The simulation being a simplification is a *feature*: we often want the best response to differ
   from the sim and say so. The failures (1, 2, 4) aren't about loyalty to the sim; they're about
   responses that float free of it — contradicting it without flagging the difference, inventing
   mechanisms it doesn't have, or referencing controls it doesn't contain.

2. **Some true claims simply don't matter here** (Scenario 5). A claim can pass every factual
   check and still be a failure because it's negligible at the sim's scale. These can't be caught
   by grounding to facts at all — they need judgment about magnitude and a pedagogical instinct to
   honor the insight while redirecting.

3. **Causal and predictive claims are the high-risk zone.** They're where confabulation hides,
   because the observed outcome appears to confirm whatever mechanism the LLM narrates (Scenario
   2). Descriptive and procedural claims fail more *visibly*.

4. **"Factually correct" and "good for learning" can point in opposite directions** (Scenarios
   3b and 5). If pedagogy isn't an explicit ground truth, the system will optimize toward being a
   correct answer-key — exactly what an inquiry simulation is designed to avoid.

5. **The right response often depends on the student, not just the facts.** Several scenarios
   (3b, 5) resolve differently depending on what the learner already understands — pointing to a
   fifth input, a model of the student, layered on top of the four truths.

6. **How much back-and-forth is a design choice, not a default.** The same grounded facts can be
   delivered as a one-shot answer or as a multi-turn Socratic discovery (Scenario 1). Which is
   "ideal" is set *per project* — and it compounds with the student model: a Socratic path only
   works if the system can track what the student has worked out so far.

7. **The dangerous errors are the plausible ones.** Every scenario here is something a
   well-meaning human tutor could *almost* say. None look like obvious "AI gibberish." That's why
   detection has to be grounded in *this activity's* specifics — its model, its controls, its
   goals, and this student — not in generic correctness.

---

## Bridge to the second document

Each scenario implies a grounding technique, and those are what the literature review covers:

| Failure | The grounding it points to (covered in doc 2) |
|---|---|
| Sim-vs-reality (1), invented mechanism (2) | feeding the LLM the model's **assumptions and rules**; tool/function access to the sim |
| Sycophancy / answer-giving (3) | **pedagogical** system policies; sycophancy mitigation |
| Phantom affordance (4) | **constraining** outputs to the activity's real affordances; retrieval over the activity definition |
| True-but-inert tangent (5) | the model's **scope and scale** assumptions; a pedagogical policy for *honoring then redirecting*; the **student model** |
| All of them | what "grounding," "faithfulness," and "attribution" actually mean, and how each is measured |

The second document surveys the research behind that right-hand column: what grounding means,
the techniques (retrieval-augmented generation, tool use, constrained decoding, fact
verification, attribution), and the empirical evidence on how often and how badly LLM tutors get
science wrong.
