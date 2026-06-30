# MCP-SIM — grounding an LLM by generating and running real simulation code

**Paper:** *A self-correcting multi-agent LLM framework for language-based physics simulation and
explanation*
**Authors:** Donggeun Park, Hyeonbin Moon, Seunghwa Ryu
**Venue:** npj Artificial Intelligence (Nature Portfolio) — a newer Nature Portfolio journal;
published 20 January 2026 (Vol. 2, Art. 10). The DOI prefix is `s44387-025`, but the article's
own publication date is 2026 ·
https://www.nature.com/articles/s44387-025-00057-z

This paper is far from our K-12 setting — its domain is **engineering finite-element / PDE
simulation**, not PhET-style interactive sims for students. But it is worth reading carefully
because it is the **most aggressive version of the "execute the model" grounding strategy** we have
found. Instead of asking an LLM what a simulation *would* do, MCP-SIM has the LLM **write
solver-ready code, actually run it, and then self-correct against the real output**. The grounding
comes from running the model, not from the LLM's knowledge of it. That is the idea we care about;
the FEM specifics are a vehicle for it.

## The problem it addresses

Setting up a real physics simulation (meshing a domain, choosing a solver, picking a time step,
getting it to converge) requires specialized programming and numerical expertise. The paper's goal
is to let **students, engineers, and researchers who lack that expertise** drive a simulation from
**natural language** — turning an underspecified prompt into a validated simulation and a readable
explanatory report. The hard part is that LLMs readily emit plausible-looking solver code that
silently produces garbage or fails to converge, so the framing centers on *correctness under
execution*, not just generating code that looks right.

## What it actually does (step by step)

MCP-SIM is a **multi-agent framework** in which every agent is driven by **GPT-4o**, coordinated by
a **Memory-Centric Orchestrator** running a **Plan → Act → Reflect → Revise** loop with persistent
memory that tracks clarifications, code versions, and error-fix mappings. The paper describes six
specialized agents; the ones that carry the argument:

1. **Input Clarifier Agent.** Transforms a vague, underspecified prompt into a structured
   specification of the simulation to run. (An **Input Rewriter Agent** refines ambiguous
   instructions.)
2. **Code Builder Agent.** Generates **solver-ready Python / FEniCS code** — i.e. real
   finite-element code against an open-source FEM library, not pseudocode.
3. **Simulation Executor Agent.** Actually **runs the code**, monitoring for physical anomalies and
   convergence (the paper uses a dimensionless residual criterion, reported as < 10⁻⁴).
4. **Error Diagnosis Agent.** On a runtime failure or non-convergence, proposes **targeted,
   physics-aware corrections grounded in the computational model** — e.g. **increase mesh density,
   reduce the time step, or adjust solver parameters** — based on convergence analysis and residual
   monitoring, then feeds the fix back into the loop.
5. **Mechanical Insight Agent.** Produces the final multilingual educational report explaining the
   result.

The crucial move is step 3→4: the correction is not the LLM guessing from training knowledge, it is
a **tool-use / verification loop against the simulation that actually executed**. That is the
strongest form of "connect to the real model" — the model is run, and its real output is what the
system reasons over and revises against.

## What it ran on

- **Stack:** Python 3.9 with **FEniCS** (open-source finite-element library), orchestrated by
  **GPT-4o**.
- **Domain:** engineering FEM/PDE problems spanning **elasticity, heat transfer, fluid dynamics, and
  multiphysics**.
- **Benchmark:** a **12-task** suite across those areas.

## How it was evaluated, and what they found

The evaluation is an **ablation** over the 12 tasks, peeling back the framework's components:

- **B1 — One-shot GPT (autonomous):** 6/12 tasks solved.
- **B2 — GPT + automated clarifier (autonomous):** 8/12.
- **B3 — GPT + clarifier + *human* diagnosis:** 10/12. This rung has a **human in the loop**:
  per the paper, the "user must review and decide whether to accept these suggestions before
  re-running." The 10/12 is therefore **not** an autonomous result.
- **Full MCP-SIM (autonomous, no human):** **12/12**, within ≤ 5 iterations.

**What "solved" means here matters a lot.** The paper defines success as **(a) generating
executable code and (b) reaching numerical convergence** — "solver residuals below 10⁻⁴ while
producing physically plausible field distributions." It does **not** require matching a reference
or analytical solution; convergence plus visual/physical plausibility was deemed sufficient. So
"12/12 solved" means *all twelve runs executed and converged to a plausible-looking field*, **not**
that twelve answers were verified correct against ground truth. That is a weaker bar than the word
"solved" suggests, and it should travel with the number.

Read directionally, the ablation tells a clean story: each layer (clarification, then diagnosis)
recovers more tasks, and the full autonomous execute-and-revise loop closes the remaining gap while
removing the human reviewer that B3 still needed. That *shape* — more grounding-through-execution,
more tasks "solved" (i.e. run-and-converged) — is the takeaway, more than any single cell of the
table.

## The one thing to keep straight: this is engineering FEM, with a tiny benchmark, and a thinly-supported headline

Three caveats have to travel with every citation of this paper:

- **It is engineering FEM/PDE simulation, not K-12 PhET-style sims.** The users are students,
  engineers, and researchers doing numerical physics; the "simulation" is a solver run, not an
  interactive classroom widget. The relevance to us is **by analogy**, not by transfer.
- **n = 12 is a very small, author-designed benchmark.** The twelve tasks are a **custom suite
  built by the authors** (no standard benchmark cited), graded into difficulty levels 1–12 (simple
  single-physics through multi-physics phase-field crack growth) and deliberately seeded with vague
  boundary conditions, missing material properties, and ambiguous geometry. That makes it a tailored
  proof-of-concept, not a statistically robust or independently-set result. Treat the percentages as
  illustrative.
- **"Solved" = ran-and-converged, not verified-correct.** Success was scored on executable code plus
  residual < 10⁻⁴ and physically plausible fields — **not** against reference solutions. A converged,
  plausible-looking run can still be wrong.
- **The headline 12/12 is only weakly supported in our own verification.** The framework
  description verified cleanly, but the specific **12/12** figure received only a **2-1** vote in
  the adversarial verification pass. Present it as suggestive, not settled — and do not lean on it
  as evidence of reliability.

## Why it matters for us

- It is the **purest realization of the execute-the-model strategy** behind
  [the content anchor](../grounding-literature-review.md): grounding the LLM's output in the *actual run of the
  computational model* rather than in the LLM's memorized knowledge of how the model behaves. When
  we ask what it would mean to "connect the help to the real simulation," MCP-SIM is the most
  literal answer — generate code, run it, verify, revise.
- The **Error Diagnosis loop maps directly onto Scenario 2** in
  [response-types-and-failures.md](../response-types-and-failures.md): when the model's behavior
  contradicts a naive expectation, the right move is to interrogate the *actual model state* (here:
  residuals, convergence, mesh) and correct against it — not to have the LLM rationalize from priors.
- But it is **aspirational, not a template**. Our sims are (today) pre-built interactive K-12
  simulations; we are not asking an LLM to write and run FEM solvers per student. The value is in
  the *principle* it demonstrates — execute the real model and reason over its true output —
  applied to a domain and a benchmark scale very different from ours. (One direction where we
  *would* have an LLM build models is sketched just below, and is out of scope here.)

## An adjacent direction we're considering (out of scope for this series)

MCP-SIM lines up with a direction we've discussed but are deliberately **not** pursuing in these
documents: having the LLM **build a new simulation on the fly** to work with a student. The
difference from MCP-SIM is the *purpose*. MCP-SIM builds and runs a model to **solve a stated
problem**; we'd want models built to **teach** — visual, interactive sims aimed at surfacing a
specific misconception or exploring a tangent the activity supports, not at producing an answer.

The MCP-SIM machinery would likely carry over: we'd want the same **generate → run → self-correct**
loop so whatever the LLM builds actually *converges* and runs. But teaching sims need layers
MCP-SIM doesn't address:

- **Interaction design.** What do we expect the student to *do* with this sim, and what outcome
  should they see when they do it?
- **Learning value.** Will that expected outcome actually help them learn the intended concept, or
  just be an interesting toy?
- **Scientific grounding.** A simulation can *converge* without being *real* — MCP-SIM's
  convergence check says nothing about whether the model reflects scientific consensus. A
  deliberately unreal sim is acceptable in some cases, but usually we'd want a GenAI-built sim to
  model reality, which means layering a grounding check ([the science anchor](../grounding-literature-review.md))
  on top of the convergence loop.

This is flagged only to mark the alignment with MCP-SIM; the design of LLM-*authored* simulations
is a larger problem we're treating as out of scope for this series.

## Limitations (stated and evident)

- **Domain mismatch:** engineering FEM/PDE, not interactive K-12 simulations; transfer to our
  setting is by analogy only.
- **Tiny, author-built benchmark:** 12 tasks designed by the authors across four physics areas
  (no standard benchmark) — proof-of-concept scale.
- **"Solved" is a weak bar:** success = executable + converged (residual < 10⁻⁴) + physically
  plausible, **not** matched to a reference/analytical solution. Convergence is not correctness.
- **One ablation rung is human-assisted:** the 10/12 (B3) requires a human to review and accept the
  diagnosis before re-running; only B1, B2, and full MCP-SIM are autonomous.
- **Headline number is weakly verified:** the 12/12 result carried only a 2-1 verifier vote (see
  below); the framework description is the solid part.
- **Built on GPT-4o + FEniCS:** ties results to a specific model and a specific FEM stack; absolute
  figures are tied to that configuration.
- The paper emphasizes solving (convergence, correct execution); it does **not** establish anything
  about pedagogy, misconception handling, or student-facing explanation quality in our sense.

---

*Verification: the fetch of the Nature/npj Artificial Intelligence article page succeeded, and the
framework details above (the multi-agent design, the Plan→Act→Reflect→Revise loop, Python/FEniCS code
generation and execution, and the physics-aware Error Diagnosis corrections) were confirmed 3-0 HIGH
confidence in the adversarial verification pass. The specific ablation headline — full MCP-SIM
solving 12/12 benchmark tasks versus 6/12 for one-shot GPT-4o, with intermediate variants at 8/12
and 10/12 (the 10/12 rung human-assisted) — received only a SPLIT 2-1 verifier vote and should be
treated as suggestive rather than established. Note also that "solved" was scored as
executable-and-converged (residual < 10⁻⁴, plausible fields), not as matching a reference solution.*
