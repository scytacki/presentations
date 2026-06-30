# Working Notes — Context, Decisions, and Open Work

*This file is **meta** — it is not part of the three-document series. It captures context for
anyone (human or a new AI session) picking this work up: the decisions behind the docs, the
conventions they follow, and what's still open. The actual content is in the three companion docs
and `papers/`.*

---

## What's here and its status

A set of documents on **grounding LLM help in scientific facts**, for the Concord-style ed-tech
research group (PIs, PMs, researchers building/studying classroom software — see
[../audiences/ed-tech-research-group.md](../audiences/ed-tech-research-group.md)).

- **[response-types-and-failures.md](response-types-and-failures.md)** — the problem: kinds of LLM
  help and how they fail (the anchors, six claim types, five deep failure scenarios). *Built from
  discussion with the user, not from literature.*
- **[grounding-literature-review.md](grounding-literature-review.md)** — the *conceptual* half of
  the grounding review: what grounding means + grounding to each of the four anchors + empirical
  reliability + scenario→technique mapping.
- **[grounding-in-practice.md](grounding-in-practice.md)** — the *operational* half: sources,
  post-hoc verification/correction loops, vetted fact stores, compression/dedup, page position.
- **[papers/](papers/)** — per-paper deep-dives of the key studies.

All committed and pushed to `main` (last batch: commit `26d365d`). Convention so far: **work stays
uncommitted until the user explicitly asks to commit.**

---

## Key decisions (don't relitigate these without reason)

- **"The four anchors," not "the four Truths."** Originally called "the four truths (A/B/C/D)"; the
  user found the opaque letters and the word "truths" tripping (and "truths" doesn't fit *pedagogy*,
  which is a teaching standard, not a fact). Renamed to **the content anchor / curriculum anchor /
  science anchor / pedagogy anchor**. Rejected alternatives and why: *references* (collides with
  paper references), *standards* (collides with education standards), *grounds* ("the four grounds"
  read wrong). "Anchors" won for the grounding tie and because "anchors pull in different
  directions" captures the conflict idea. **Don't revert to "truths."**
- **Doc division = problem / concepts / operations + papers.** Docs 2 and 3 are both grounding lit
  reviews; the split is deliberate (concepts vs. how-you-build-it), after the user noted doc 3
  shouldn't be a separate "non-sim content" doc.
- **Content is a digital textbook, not just simulations.** Text, images, questions (MC, open
  response, drawing) with sims as one *executable* part. The **content anchor** generalizes: for a
  sim it's the runnable model; for a text page it's just the page (and the science anchor does more
  of the work, with no sim to fall back on).
- **The reference simulation is an assumption.** Docs use a projectile launcher (mass / angle /
  ground-friction, **no air resistance**, mass doesn't affect trajectory). The user has *not*
  confirmed this matches a real target sim — the scenarios depend on these specifics, so **confirm
  before treating them as literal.**
- **"Context graph" terminology was deliberately kept OUT.** The user's related work lives in
  [../context-graphs/](../context-graphs/) and the interaction-history-as-student-model idea
  connects, but the *term* "context graph" was judged too jargony/distracting for this audience.
  Don't reintroduce it here.
- **The "fifth input" (a model of the student) and page-position** are flagged as important but
  likely beyond a first cut — kept as forward-pointers, not built out.
- **LLM-authored simulations (GenAI builds new sims for students)** is explicitly **out of scope**
  for this series — noted as an adjacent direction in [papers/mcp-sim.md](papers/mcp-sim.md).

---

## Conventions these docs follow (match them)

- **Verify every citation.** Each cited paper was resolved to a real arXiv ID / DOI / venue;
  unverifiable 2025–2026 preprints were dropped. The user has been burned by fabricated/misremembered
  citations (notably checked that EduFrameTrap, arXiv:2605.14604, is real, and corrected SimPal's and
  others' overstatements). **Maintain verify-before-cite; never assert a paper from memory.**
- **Honest framing, three tiers:** call out what's *solid research*, what's a *sound-but-unproven
  synthesis*, and what's a *genuine gap* (especially: almost none of this was built/evaluated for
  K-12 science education — say so).
- **Per-paper deep-dive structure:** problem → what it does → what it ran on → how evaluated → the key
  caveat → why it matters for us → limitations → a *Verification* note.
- **Flag domain mismatches plainly** (e.g., MCP-SIM is engineering FEM not PhET; Wang is text-games
  by analogy).

---

## How the research was produced — and what does NOT carry into a new session

The citations and conclusions in the docs came from: an initial **deep-research workflow** (broad
fan-out + adversarial verification), then **per-paper scrutiny agents** (re-reading each source to
catch overstatements), and two **focused verification dives** (post-hoc verification methods;
sources / fact-stores / compression).

**Important for a new session:** the *raw* research outputs (verified findings, scrutiny notes, the
deep-research report) lived in the prior session's context and in session-scoped temp files — they
are **not in the repo** and **won't be in a new session's context.** The docs themselves carry the
conclusions and citations. If you extend or re-open a claim, **re-run targeted verification** rather
than trusting recall.

Scrutiny conclusions worth keeping in mind (all already reflected in the per-paper docs, so this is
just so you don't over-read them):
- **SimPal** is an early *proposal*: no real teachers (authors stood in), the downstream agent is
  hypothetical/never built, variables grounded in sim *descriptions* not the sim. It is *not*
  "an LLM tutor grounded in a simulation."
- **MCP-SIM**: "12/12 solved" = ran-and-converged, *not* verified-correct; engineering FEM; tiny
  author-built benchmark; one ablation rung is human-assisted.
- **Adapting While Learning (Li)**: the "8B beats GPT-4o" result is home-turf only (loses on public
  benchmarks); gains are over its *own base model*.
- **NewtBot**: medium confidence (single secondary-venue source; full text paywalled).

---

## Open work / where the user was headed

- **The user wants to "review the lit review more to clarify some things."** The specific
  clarifications were not yet enumerated — ask.
- **Not chosen (deferred):** adding *non-sim content* failure scenarios to doc 1 (the analog of the
  five sim scenarios, e.g. an open-response or MCQ where help goes wrong). Was offered; user kept the
  reframe as a framing-fix + the operations doc instead.
- **Confirm the reference-sim assumptions** against a real activity if these go further.
- **The deeper non-referential grounding** (verifying *behavioral* claims about a sim, not just that
  referenced controls exist) still routes back to executing the model — a genuine open design
  question for the group.
