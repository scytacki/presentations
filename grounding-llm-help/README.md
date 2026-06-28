# Grounding LLM Help in Scientific Facts

Two companion documents on using an LLM to help students working in our simulations, while
keeping that help founded in scientific facts. Same audience/context as the other presentations
in this repo — people building and studying classroom software.

The framing throughout: there isn't one "the facts." There are **four truths** a response can be
checked against — the **simulation's own model**, the **curriculum/activity**, **scientific
consensus**, and **pedagogy**. They can legitimately differ — our sims are deliberate
simplifications — and that difference is a *feature*: the invariant isn't "match the sim," it's
"always contextualize against the sim's simplifications." A **fifth input**, a model of the
student's understanding, decides how to deploy those truths for a particular learner.

1. **[response-types-and-failures.md](response-types-and-failures.md)** — the kinds of help an
   LLM gives (answers, hints, feedback, explanations), a six-claim-type failure matrix, and five
   deep failure scenarios developed in a concrete projectile sim. *Built from our own discussion.*

2. **[grounding-literature-review.md](grounding-literature-review.md)** — what "grounding a
   response" actually means (grounding/faithfulness vs factuality), the techniques organized by
   the four truths (tool use, RAG, fact-verification, pedagogical policy), the empirical evidence
   on how reliable LLM tutors are today, and a table mapping each technique back to the five
   scenarios. *Built from a fact-checked literature scan.*

Start with doc 1 for the problem, doc 2 for the techniques and evidence.
