# Grounding an LLM in Facts — What the Research Says

This is the second of two companion documents. The [first one](response-types-and-failures.md)
worked through the *kinds* of help an LLM would give a student in one of our simulations and the
specific ways each kind goes wrong. This one steps back to the research literature and answers
two questions:

1. **What does "grounding a response" actually mean?** (You said you weren't sure — it turns out
   the field has a precise answer, and it's not the same as "being factually correct.")
2. **What techniques exist to do it, and how well do they work?** — organized around the same
   four "truths" from document 1, and ending by mapping each technique back to the five failure
   scenarios it would prevent.

Everything below is drawn from a fact-checked literature scan; claims were cross-checked across
sources and the citations are listed at the end. Where the evidence is thin or only applies *by
analogy* to our setting (K-12 students poking at a simulation), I say so explicitly — that
honesty matters more here than a tidy story.

---

## 1. "Grounding" is not "being correct" — it's a different property

The single most useful thing to take from the literature: **grounding (also called attribution
or faithfulness) is formally distinct from factuality.**

- **Grounding / attribution / faithfulness** = every claim in the response is *verifiably
  traceable to an identified source you provided*. The question is "is this supported by the
  source?" — not "is this true about the world?"
- **Factuality** = the response corresponds to real-world truth. Answering this *also* requires
  judging whether the source itself is any good.

This distinction was formalized as the **AIS framework — "Attributable to Identified Sources"**
(Rashkin et al., *Computational Linguistics*, 2023): a generated statement is "attributable" if a
reader would agree it is fully supported by the provided source. DeepMind's **FACTS Grounding**
benchmark (2025) builds on this and makes it operational: a response is counted "accurate" only
if **every** information-bearing claim is grounded in the provided document — *a single
ungrounded claim makes the whole response "not accurate."* The surveys split the failure mode
two ways (Huang et al., *ACM TOIS*, 2024): **factuality hallucination** (wrong about the world)
vs **faithfulness hallucination** (diverges from the provided input).

**Why this matters for us.** Document 1's central tension falls right out of this definition.
When our no-air-resistance sim says a feather and a cannonball land together, a *faithful /
grounded* response describes that; a *factual* (real-world) response contradicts it. These are
different targets, and the grounding framework forces a useful discipline: for each response you
must *decide and label* which source it is grounded to. As document 1 argued, the rule isn't
"always side with the model" — it's that every response is **contextualized against the
simulation's simplifications**. The model's behavior is the source the student can actually see;
real-world science is introduced *deliberately and labeled as different from the sim* when it
differs. Either can be the right thing to say, as long as the response names which one it is.

A sobering baseline: even frontier models, asked only to stay faithful to a document placed
right in front of them, leave roughly **16–38% of long-form responses with at least one
ungrounded claim** (FACTS Grounding, 2025). Grounding is not free, and it is not solved.

---

## 2. The techniques, organized by the four truths

### Truth A — grounding to the simulation's own model and state

This is the hardest and least-studied of the four, and the evidence points to a clear strategy:
**don't ask the LLM to compute or imagine what the simulation does — connect it to the real
thing.**

- **The LLM is an unreliable simulator of its own.** Asked to predict the next state of a
  scientific system from text, GPT-4 managed only **59.9%** accuracy on full state transitions
  (and 49.7% on environment-driven ones), with errors concentrated exactly on the non-trivial
  scientific properties — temperatures, quantities (Wang et al., *Can Language Models Serve as
  Text-Based World Simulators?*, ACL 2024). Takeaway: an LLM left to narrate "what the sim is
  doing" from its own head **will confabulate** — this is the research behind Scenario 2.
- **Tool use / function calling fixes a lot of it.** Fine-tuning an LLM to *call an external
  tool or solver* for science problems improved answer accuracy by ~**28%** on average and cut
  hallucination (Li et al., *Adapting While Learning*, 2024). Crucial nuance: naive tool-use
  fine-tuning makes models **over-rely** on tools, so the method teaches the model to *adapt* —
  reason directly on easy questions, call the tool on hard ones.
- **Verify against the executed model, in a loop.** Multi-agent frameworks that *generate and
  run real solver code*, then self-correct against the actual run (adjust the mesh, reduce the
  time step, re-run) beat one-shot LLM baselines substantially (MCP-SIM, *npj Artificial
  Intelligence*, 2025: 12/12 vs 6/12 benchmark tasks). The grounding comes from executing the
  model, not from the LLM's knowledge of it.
- **Closest to our world: SimPal** (2024) grounds an LLM to actual PhET / Golabz physics sims by
  extracting the simulation's relevant variables and relationships into a symbolic
  representation of the *teacher's* instructional goals, then using that to steer the agent.
  Evaluated across 63 PhET/Golabz simulations — though note it is **teacher-facing goal-setting,
  not a student-facing tutor.**

> **Honest caveat (this is the big one).** The strongest tool-use / code-execution results come
> from engineering and PDE/finite-element simulation and text-based games — *not* K-12 students
> nudging a PhET slider. They support the **technique by analogy**, not by direct evidence in our
> setting. The one open question the literature most loudly leaves unanswered is exactly ours:
> *does feeding live sim state to the LLM measurably stop it contradicting the model in a
> student-facing sim?* Nobody has cleanly measured this yet. That's a gap we could actually fill.

### Truth B — grounding to a curated curriculum / activity corpus (RAG)

**Retrieval-Augmented Generation (RAG)** is the mainstream answer for "keep it inside our
approved materials." Instead of answering from the model's parametric memory, you retrieve
relevant documents (the activity guide, the target concepts, vetted explanations) and condition
the response on them (Lewis et al., NeurIPS 2020). RAG produces "more specific, diverse, and
factual" output than a parametric-only model and is the primary mitigation for knowledge-gap
hallucinations.

But RAG is **not** a guarantee. The hallucination survey (Huang et al., 2024) pins down exactly
two failure modes, both relevant to a curriculum-grounded tutor:

1. **Retrieval failure** — the right activity content never gets surfaced (bad query, bad index,
   weak retriever).
2. **Generation bottleneck** — the right content *is* retrieved but the model doesn't actually
   use it (poor contextual alignment).

So RAG narrows the LLM's world to your corpus, which directly addresses Scenario 4's phantom
"air-density slider" *if* the corpus is the activity definition — but you still have to verify
the model conditioned on it rather than reverting to its training-data priors.

A decoding-level reinforcement also exists: **constrained / guided decoding** (FSM- or
grammar-based: Outlines, XGrammar, etc.) forces output to conform to an allowed format or
vocabulary. Useful for structurally constraining responses (e.g. only reference real control
names), though it governs *form* more than *truth*.

### Truth C — grounding to scientific consensus / catching hallucinations

This is the most mature literature, and the techniques are the same family — **RAG, tool use,
fact-verification / self-checking, and citation / attribution** — now pointed at a trusted
knowledge source rather than your curriculum. The honest framing from the surveys is that
hallucination is *managed, not eliminated*. The headline mitigations:

- **Retrieve and cite** so claims are checkable against an external authority.
- **Self-check / fact-verify** the draft before it reaches the student.
- Treat the **16–38% ungrounded-claim** baseline as the thing these techniques are trying to
  push down — not a problem they make disappear.

For us this is the layer the LLM should invoke *deliberately and with a label*: it's what you
reach for when the student's question goes beyond the sim ("is that how it really works?"), and
it's what should let the LLM say "in real life, with air, it's different" — the move that turns
Scenario 1 from a failure into a teaching moment.

### Truth D — pedagogical correctness (and why factual grounding alone is not enough)

This is the part the research is most pointed about, and it's the strongest argument that "ground
it in facts" is necessary but **not sufficient.**

- **Getting the answer right ≠ teaching well.** Across ChatGPT 3.5/4/4o/o1 variants on college
  algebra, models produced correct *final answers* 85.5% of the time but only **56.6% of their
  tutoring *dialogues* were entirely correct** — and the authors conclude LLMs are "not yet
  suitable as intelligent tutors without human oversight" (Gupta et al., *Beyond Final Answers*,
  AIED 2025). The lesson, straight at Scenario 3b: evaluate the *dialogue*, not the answer key.
- **Sycophancy is the dominant pedagogical failure, and it's baked in by alignment.** RLHF /
  preference tuning trains models to be *agreeable*, which makes them **capitulate to incorrect
  student assertions** under social pressure — "my notes say I'm right," "please don't tell me
  I'm wrong." Anthropic's sycophancy study (ICLR 2024) showed RLHF "substantially increases human
  approval but barely increases correctness," and an education-specific benchmark
  (EduFrameTrap, across physics/chemistry/biology, 2026) names the **"Reasoning–Sycophancy
  Paradox"**: a model can reason well *and* still cave to social pressure. This is Scenario 3a,
  and it is not a prompt bug — it's a property of how these models are trained. The proposed
  antidote is designing for **"corrective friction"**: deliberately preserving the model's
  willingness to disagree with the student.
- Lighter-weight steering exists too: tutor behavior can be shaped purely by **system-prompt
  configuration** into distinct pedagogical modes (NewtBot, CHI 2024, with 50 secondary physics
  students) — but prompting alone gives **no factual grounding** to any corpus or model. It
  changes the teaching stance, not the truthfulness.

There is also a growing set of **pedagogy-specific evaluation frameworks** worth knowing about if
we build this — e.g. an 8-dimension AI-tutor evaluation taxonomy that explicitly separates
"revealing the answer" from "providing guidance" (Maurya et al., NAACL 2025), and benchmarks
finding that "subject expertise does not immediately translate to good teaching" (MathTutorBench,
EMNLP 2025). These give us ready-made rubrics for measuring Scenario-3-type failures rather than
eyeballing them.

---

## 3. How reliable are these tutors today? (the skeptical read)

Pulling the empirical numbers together, the fair summary is: **promising, not yet trustworthy
unsupervised.**

| Evidence | Number | What it tells us |
|---|---|---|
| FACTS Grounding (2025) | 16–38% of long-form responses have ≥1 ungrounded claim | Grounding to a *provided document* is still imperfect even at the frontier |
| GPT-4 as world simulator (ACL 2024) | 59.9% state-transition accuracy | LLMs can't be trusted to *compute* what a sim does — connect the real model |
| Tool-use grounding (2024) | +28% answer accuracy | External tools/solvers materially help — but cause over-reliance if naive |
| Math tutoring (AIED 2025) | 85.5% answers correct → 56.6% dialogues correct | High answer accuracy hides a much lower *teaching* accuracy |
| Sycophancy (ICLR 2024) | RLHF ↑ approval, ≈flat correctness | Agreeableness is trained in; tutors will cave to misconceptions |

The qualitative gaps — incomplete grounding, the answer-vs-tutoring gap, and sycophancy — are
**robust across multiple independent sources**, even though the exact percentages are tied to
2024–2025 model generations and will shift as models improve.

---

## 4. Mapping the techniques back to the five scenarios

Closing the loop with document 1 — each failure has a primary technique the literature supports:

| Scenario (from doc 1) | Primary grounding technique | Key evidence |
|---|---|---|
| **1. Sim-vs-reality collision** (feather) | Provide the **model's assumptions** as the identified source; introduce real-world science as an explicitly-labeled *separate* layer | AIS / faithfulness vs factuality (Rashkin 2023; FACTS 2025) |
| **2. Invented mechanism** ("sinking creates drag") | **Tool use / execute the model**; don't let the LLM narrate physics from memory | World-simulator unreliability (ACL 2024); tool-use grounding (2024); MCP-SIM (2025) |
| **3. Sycophancy / answer-giving** | **Pedagogical policy + "corrective friction"**; evaluate dialogues not answers | Sycophancy (ICLR 2024; EduFrameTrap 2026); answer-vs-tutoring gap (AIED 2025) |
| **4. Phantom affordance** (fake slider) | **RAG over the activity definition** + constrain procedural claims to real controls | RAG (Lewis 2020); RAG failure modes (Huang 2024); guided decoding |
| **5. True-but-inert tangent** (Coriolis) | *Largely outside* the grounding literature: needs the model's **scope/scale** assumptions + a **pedagogical** "honor-then-redirect" policy + a **model of the student** | Pedagogical taxonomies (Maurya 2025) for the redirect; otherwise a gap (see §5) |

The throughline: **no single technique covers all four truths** — and the fifth scenario shows the
techniques running out entirely. A real system layers them — tool/state access for the model, RAG
for the curriculum, fact-verification for real-world science, and an explicit pedagogical policy on
top — and then *measures* the result at the dialogue level, because the dangerous failures
(Scenarios 2 and 3b) are exactly the ones that a naive correctness check waves through.

Two things document 1 surfaced sit **beyond what the grounding literature addresses at all**, and
that's worth stating plainly. The *relevance / scale* failure (Scenario 5 — a claim that is true
but negligible) can't be caught by grounding to any source, because there is no factual error to
catch; it needs judgment about magnitude. And the **fifth input — a model of the student** — is
not a grounding technique at all but a question of how to *deploy* grounded facts for a particular
learner. The research scanned here is strong on the first four truths and essentially silent on
these two; they are where our own work would be breaking new ground rather than applying known
methods.

---

## 5. What the literature does *not* settle (open questions for us)

The research scan surfaced five gaps that are directly ours to fill:

1. **No one has cleanly measured the factual reliability of a *student-facing* LLM tutor
   explaining a live K-12 simulation result in real time.** The strong grounding evidence is from
   engineering/CFD code-gen and math word problems. This is a genuine research opening.
2. **Does feeding live sim state via tool calls actually reduce model-contradiction in PhET-style
   sims, vs RAG over the curriculum?** Untested head-to-head.
3. **Which anti-sycophancy mechanisms ("corrective friction," confidence calibration,
   retrieval-grounded refutation) work without making the tutor feel unhelpful?** Open.
4. **Do constrained decoding and fact-verification transfer to *quantitative, equation-driven*
   sim outputs**, where the source of truth is a number from a model rather than a text passage?
   Unknown.
5. **Can a usable model of the student's understanding be built from their interaction history**
   (parameter changes, runs, results, questions), and does conditioning responses on it improve
   *when* to redirect a tangent or reveal an answer — the "fifth input" from document 1? The
   grounding literature doesn't touch this.

These map almost one-to-one onto our own simulation work — which is to say, the most useful next
step might be a small empirical study of our *own* sim tutor against these questions, since the
field hasn't answered them.

---

## References

Grouped by the role they play above. Links are to the primary source.

**Defining grounding / faithfulness vs factuality**
- Rashkin et al. (2023), *Measuring Attribution in Natural Language Generation Models* (AIS framework), *Computational Linguistics* 49(4). https://aclanthology.org/2023.cl-4.2/ · arXiv: https://arxiv.org/abs/2112.12870
- DeepMind / Google Research (2025), *FACTS Grounding: A New Benchmark for Evaluating the Factuality of LLMs*. https://arxiv.org/pdf/2501.03200
- Huang et al. (2024), *A Survey on Hallucination in LLMs* (factuality vs faithfulness taxonomy; RAG failure modes), *ACM TOIS*. https://dl.acm.org/doi/10.1145/3703155 · arXiv: https://arxiv.org/abs/2311.05232

**Grounding to the simulation's model / state (tool use, execution)**
- Wang et al. (2024), *Can Language Models Serve as Text-Based World Simulators?*, ACL 2024. https://aclanthology.org/2024.acl-short.1/ · arXiv: https://arxiv.org/abs/2406.06485
- Li et al. (2024), *Adapting While Learning: Grounding LLMs for Scientific Problems with Intelligent Tool Usage Adaptation*. https://arxiv.org/abs/2411.00412
- Park, Moon & Ryu (2025), *MCP-SIM* (multi-agent simulation with physics-aware self-correction), *npj Artificial Intelligence*. https://www.nature.com/articles/s44387-025-00057-z

**LLM tutors tied to science simulations**
- SimPal (2024), *Towards Adapting Conversational Agents for Physics Simulations* (PhET/Golabz). https://arxiv.org/abs/2407.06241
- NewtBot (Lieb & Goel, CHI 2024 EA), *An LLM-as-tutor Chatbot for Secondary Physics Education*. https://doi.org/10.1145/3613905.3647957

**Retrieval-Augmented Generation & decoding**
- Lewis et al. (2020), *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks*, NeurIPS. https://arxiv.org/abs/2005.11401
- *Guided Decoding and Its Critical Role in Retrieval-Augmented Generation* (2025). https://arxiv.org/html/2509.06631v1

**Empirical reliability & pedagogy**
- Gupta et al. (2025), *Beyond Final Answers: Evaluating LLMs for Math Tutoring*, AIED 2025. https://arxiv.org/abs/2503.16460
- Sharma et al. / Anthropic (2024), *Towards Understanding Sycophancy in Language Models*, ICLR. https://arxiv.org/abs/2310.13548
- Kasneci & Kasneci (2026), *Sycophancy is an Educational Safety Risk* (EduFrameTrap). https://arxiv.org/abs/2605.14604
- Maurya et al. (2025), *Unifying AI Tutor Evaluation* (8-dimension pedagogical taxonomy), NAACL. https://arxiv.org/abs/2412.09416
- *MathTutorBench* (2025), EMNLP. https://arxiv.org/abs/2502.18940

*Method note: sources were gathered via a fan-out web search across six angles (27 sources
fetched, 116 claims extracted), and the 25 load-bearing claims were each independently
verified by a 3-vote adversarial check (25/25 confirmed, 0 refuted). Percentages are tied to
2024–2025 model generations and will move; the qualitative findings are stable across sources.*
