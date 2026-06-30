# Grounding LLM Help in Scientific Facts

Three companion documents on using an LLM to help students working through our content — a
**digital textbook** (text, images, questions) with **simulations** as one rich part — while
keeping that help founded in scientific facts. Same audience/context as the other presentations
in this repo — people building and studying classroom software.

The framing throughout: there isn't one "the facts." There are **four anchors** a response is held
to — the **content the student is on** (a simulation's model, or the page itself),
the **curriculum/activity**, the **scientific consensus**, and **pedagogy**. For a simulation they
can legitimately differ — sims are deliberate simplifications — and that difference is a *feature*:
the invariant isn't "match the sim," it's "always contextualize against the sim's
simplifications." For non-sim content there's no sim to fall back on, so scientific consensus does
more of the work. A **fifth input**, a model of the student's understanding (including *where they
are* in the content), decides how to deploy those anchors for a particular learner.

1. **[response-types-and-failures.md](response-types-and-failures.md)** — the kinds of help an
   LLM gives (answers, hints, feedback, explanations), a six-claim-type failure matrix, and five
   deep failure scenarios developed in a concrete projectile sim. *Built from our own discussion.*

2. **[grounding-literature-review.md](grounding-literature-review.md)** — what "grounding a
   response" actually means (grounding/faithfulness vs factuality), the techniques organized by
   the four anchors (tool use, RAG, fact-verification, pedagogical policy), the empirical evidence
   on how reliable LLM tutors are today, and a table mapping each technique back to the five
   scenarios. *Built from a fact-checked literature scan.*

3. **[grounding-in-practice.md](grounding-in-practice.md)** — the **operational half** of the
   grounding review (doc 2 is the conceptual half): where sources come from, a concise
   author-vetted fact store, source compression/dedup, what verification does on an error, and
   using the student's **page position** as context. The mechanisms apply across content but matter
   most for the non-sim pages. *Built from a fact-checked literature dive.*

Start with doc 1 for the problem, doc 2 for what grounding is and the four anchors, doc 3 for how to
source, build, and check it.

- **[papers/](papers/)** — per-paper deep-dives of the handful of studies closest to our work
  (SimPal, the model-grounding trio, NewtBot, and the sycophancy research), linked inline from
  doc 2. Each is a faithful, caveated summary for anyone who wants to drill past the citation.
