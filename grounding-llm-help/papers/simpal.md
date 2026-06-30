# SimPal — adapting a simulation's conversational agent to a teacher's goals

**Paper:** *SimPal: Towards a Meta-Conversational Framework to Understand Teacher's Instructional
Goals for K-12 Physics*
**Authors:** Effat Farhana, Souvika Sarkar, Ralph Knipper, Indrani Dey, Hari Narayanan, Sadhana
Puntambekar, Shubhra Kanti Karmaker
**Venue:** arXiv:2407.06241 (physics.ed-ph), July 2024 · https://arxiv.org/abs/2407.06241

This is the single paper in the literature scan **closest to our setting in topic** — it names
interactive K-12 physics simulations (PhET and Golabz) directly and proposes an LLM layer for
them. It is worth reading carefully *and* being precise about what it does and does not do,
because it is easy to over-read as "an LLM tutor grounded in a simulation." What it actually
delivers is considerably narrower: a study showing an LLM can extract physics-topic variables
from a simulation's *web-page description*, with the teacher conversation, the agent steering, and
the student tutoring all remaining proposed design rather than executed research. The accumulated
caveats below (no model grounding, no existence check, no real teachers, no downstream agent) are
the point — this is an early proposal, not a working grounded tutor.

## The problem it addresses

Conversational agents embedded in science simulations are usually **hard-wired to one
simulation, with a predesigned set of Instructional Goals (IGs)**. The paper's framing: this
makes it "difficult for teachers to adjust IGs," so teachers hesitate to adopt a simulation whose
built-in agent doesn't match their own evolving pedagogy. SimPal targets that *alignment gap*
between a fixed agent and a teacher's actual instructional intent — not the question of whether
the agent's physics is correct.

Worth being precise about this "embedded agent," because it is easy to over-read: it is the
paper's *motivating category*, not a specific PhET or Golabz product. PhET and Golabz are **not**
described as shipping conversational agents; the only agents the paper names are **Betty's Brain**
and **MetaTutor**, cited as general examples of pedagogical agents in science education. As we'll
see, the downstream agent SimPal is meant to steer is never actually built or run in this paper.

## What it actually does (step by step)

SimPal is a **"meta-conversational" framework**: a conversation *about* how the simulation's
agent should behave, rather than the student-facing conversation itself. The pipeline:

1. **Teacher dialogue (the *intended* workflow).** A teacher is meant to talk to SimPal in natural
   language to articulate the instructional goals they want for a given simulation. In the actual
   study, **no real teachers were involved** — the authors stood in for the teacher role (see
   below).
2. **Variable extraction.** SimPal identifies a set of **relevant physical variables** for that
   topic — e.g. for a waves activity, *wavelength* and *frequency*.
3. **Symbolic representation.** Those variables are encoded into a compact structured form. This is
   thinner than the phrase "symbolic representation of relationships" suggests — the worked example
   (the paper's Table 5) is essentially a list of **name–symbol pairs**, e.g.
   `{"Name": "Wavelength", "Symbol": "λ"}`, not a relationship graph.
4. **Prompt generation.** That representation is used to generate **prompts intended for a
   downstream conversational agent**, to steer it toward the teacher's goals. That downstream
   agent is **assumed, not built** — in this paper the pipeline stops at producing the prompts.

## Where do the variables come from? — and the grounding gap

This is the question that decides how much "grounding" SimPal actually demonstrates, and the
answer is important: **the variables are grounded in *text about* the simulation, not in the
simulation itself.**

- **The input is a description, never the running sim.** For the evaluation, prompts were built by
  "extracting IGs and topics from lab web pages" — PhET's stated *learning goals* and Golabz's
  *lab descriptions*. The LLM was **not** given the simulation's source code, its list of
  controls/variables, its HTML, or screenshots. (The paper is somewhat contradictory here: the
  abstract implies the variables come from the teacher dialogue, while §4.3 describes pulling them
  from lab-page text. Either way, the source is **natural-language description**, not the live
  model.)
- **There is no existence check.** The paper describes **no** step that verifies an extracted
  variable actually exists in the simulation. So if a teacher — or a lab description — mentions a
  variable the sim doesn't have, SimPal would extract and propagate it into the generated prompts
  anyway. Nothing reconciles the extracted variables against the sim's real affordances.
- **The "ground truth" was a textbook, not the sim.** Extraction accuracy was judged against
  teacher-annotated topics cross-referenced with an open-source **CK-12 physical science
  textbook** — i.e. grounded in physics-topic vocabulary, not in what the simulation exposes. (The
  authors note their gold standard "may lack the subject matter expertise of real school
  teachers.")

So the grounding SimPal performs is to a simulation's *description and to physics-topic
vocabulary* — **not** to the simulation's actual variable set, computational model, or scientific
consensus. The capability to "look at the sim" (analyze its source, drive it with a browser tool)
simply wasn't available in the GPT-3.5 era this was built in; reading the sim's web-page text was
the substitute, and it is metadata, not the model.

## What it ran on

- **63 physics simulations** drawn from **PhET** (Physics Education Technology) and **Golabz**
  (Global Online Science Labs).
- Two LLMs were tested: **ChatGPT-3.5** and **PaLM 2**.

## How it was evaluated, and what they found

The evaluation focused on the **variable-extraction step** — can the LLM reliably identify the
relevant physical variables for a simulation? They studied "the impact of different prompting
techniques on LLM's performance," using the **TELeR taxonomy** (a scheme for describing prompt
levels of detail) to structure the prompts. The headline finding: SimPal can do this **"with a
high degree of accuracy when provided with a well-defined prompt."** Performance is contingent on
prompt quality. Note what "accuracy" means here: extraction was scored against the
teacher-annotated, textbook-cross-referenced gold standard described above — i.e. against
physics-topic vocabulary, **not** against the simulation's actual variables.

And note where the evaluation *stops*: at variable extraction. SimPal's generated prompts were
**never actually fed to a downstream conversational agent**, and no student-facing dialogue was
produced or measured. End-to-end use is explicitly future work. So "steering the agent" — the
part that would matter most to us — is asserted by design, not demonstrated.

## The one thing to keep straight: teacher-facing by design — but no teachers actually used it

**SimPal does not talk to students.** It is a tool for *teachers* to configure what a
simulation's agent should emphasize. But be blunt about how much was actually *demonstrated*:
**no real teachers participated in the study at all.** The "teacher converses with SimPal"
workflow is the intended design; in the reported experiments the **authors stood in for
teachers** — they extracted the instructional goals from lab web pages and wrote the gold-standard
variable lists themselves, acknowledging these "may lack the subject matter expertise of real
school teachers." Teacher feedback is named as future work. And nothing measures the
student-facing side either: no reliability, misconception, or correctness evaluation of any
downstream dialogue, because no downstream agent was run. That is the boundary of what this paper
supports.

## Why it matters for us

- It is **direct evidence that PhET/Golabz-style sims can be paired with an LLM layer** — but note
  what it does *not* show. The variables are extracted from the sim's *description*, with no check
  against the sim's real model, so this is **not** an instance of the model-grounding our
  [content anchor](../grounding-literature-review.md) needs. If anything, the missing existence-check is a
  concrete illustration of the very gap the content anchor has to close.
- The **symbolic-representation-of-goals idea maps onto two of our pieces at once**: it's a way to
  encode *curriculum/activity intent* (the curriculum anchor) and it overlaps with the **teacher/curriculum side
  of the "fifth input"** — the activity's intent that, in document 1's Scenario 5, decides whether
  a sophisticated tangent is a distraction or an invitation.
- But it leaves **our central question untouched**: nothing here measures whether the
  student-facing agent's explanations actually match the simulation's model or avoid
  misconceptions. SimPal aligns the agent to a *teacher*; grounding its answers to the *model* is
  still open.

## Limitations (stated and evident)

- The paper does not enumerate limitations explicitly; the clearest one is that accuracy is
  **prompt-quality-dependent** ("when provided with a well-defined prompt").
- Evaluation centers on **variable extraction**, not on the quality, correctness, or pedagogy of
  any resulting student dialogue. The **downstream conversational agent is never implemented or
  run** — the prompts SimPal produces are not fed to anything, so the end-to-end claim is untested.
- **Teacher-facing only** — no student-interaction or reliability results.
- **No real teachers were involved.** Inputs and gold-standard annotations were produced by the
  authors standing in for teachers; real-teacher use and feedback are future work.
- Built on **ChatGPT-3.5 / PaLM 2**, both now superseded; absolute accuracy figures are dated even
  if the approach is not.

---

*Verification: the core claims here (meta-conversational framework, the symbolic representation as
name–symbol pairs, 63 PhET/Golabz simulations, ChatGPT-3.5 + PaLM 2, teacher-facing) were confirmed
3-0 in the adversarial verification pass of the original research scan. The detail on **where the
variables come from** — lab-web-page text rather than the running sim, the absence of any
existence-check, and the CK-12-textbook gold standard — was read from the paper's full text (§4.3
and Table 5 of the arXiv HTML), which is also where the paper's own internal inconsistency on the
input source surfaces. The downstream conversational agent SimPal targets is likewise **assumed
rather than implemented** — no agent is named, built, or run, and the evaluation stops at variable
extraction. **Real-teacher involvement is similarly absent** — inputs and gold-standard variable
lists were authored by the researchers (who note the gold standard "may lack the subject matter
expertise of real school teachers"), with actual teacher use flagged as future work. All confirmed
from the paper's full text (§4.3, Table 5) and its future-work framing.*
