# Grounding in Practice — Sources, Verification, and Operations

This is the third of three companion documents, and the **operational half** of the grounding
review. The [second document](grounding-literature-review.md) covers *what grounding is and what you
ground to* — the definitions and the four anchors. This one covers *how you actually source, build,
and check* a grounded response: where the facts come from, how to store them concisely, how to trim
them, what to do when a verification pass finds something wrong, and how the student's position in
the content feeds in. (The [first document](response-types-and-failures.md) is the problem
statement — how help fails.)

These mechanisms are general — they apply to simulation and non-simulation content alike — but they
**matter most for the non-simulation parts**: the digital-textbook pages of text, images, and
questions. On a simulation page, the content anchor is a runnable model you can execute and verify
against (document 2). On a text page there's nothing to run, so grounding leans on the content
itself (the curriculum anchor) and **scientific consensus (the science anchor), with no sim to fall
back on** — the reweighting is spelled out under the content anchor in
[document 2](grounding-literature-review.md). That's why the questions
below — sources, fact stores, verification, position — are where the non-sim case really lives, even
though the techniques themselves are content-agnostic.

The audience and framing are the same as the others: people building and studying classroom
software. Where the research is solid I cite it; where an idea is a sound-but-unproven synthesis, or
a design choice rather than a finding, I say so.

---

## 1. Where do the grounding sources come from?

A student can ask about almost anything, including things no author anticipated. You cannot
pre-source everything, so this is partly a design decision about *coverage vs. vettability*:

- **The content itself.** The page's text/images/questions are the first source — cheap, already
  authored, exactly on-topic. But it's narrow, and you don't want students re-reading it to get an
  answer they could be told.
- **A curated, vetted corpus.** A bounded set of trusted references (a science encyclopedia, a
  vetted textbook, an author-reviewed fact base — see §2) the system can retrieve from. Reviewable
  and safe, but incomplete by construction.
- **Runtime retrieval / web search.** Broad coverage, but it reintroduces exactly the problem we're
  trying to avoid: unvetted source quality. If used at all, it should be a *filtered/trusted* index,
  not the open web.
- **The model's parametric memory.** Broadest coverage, least verifiable — this is the ungrounded
  default we're trying to replace, acceptable only with a verification pass on top (§3, §5).
- **Abstain / scope.** The most underrated option: recognize an out-of-scope question and either
  answer from a trusted general source *with a citation*, or bound gracefully — *"that's beyond this
  lesson; the short version is…, and we'll get to it."* Not every question needs a confident answer.

**The honest shape of it:** a vetted corpus (§2) covers the topics you *can* anticipate; abstain +
trusted-fallback handles the rest. There is no source that is simultaneously complete, safe, and
free — pick the mix per how much risk the activity can tolerate.

---

## 2. A concise, author-vetted fact store

You asked for "something like RAG but more concise — GenAI makes a list of *facts* the content
author could review and vet." This is a good instinct, and the building blocks are established even
though the *combination* is not yet a named, benchmarked method.

- **Atomic facts as the unit, not passages.** Retrieving *propositions* — atomic, self-contained
  factoids — beats sentence- and passage-level retrieval, with the biggest gains on rare entities
  (**Dense X Retrieval**, Chen et al., EMNLP 2024). That's the research backing for "more concise
  than chunk-based RAG": you index small, decontextualized facts instead of paragraphs.
- **Extracting the facts.** Decomposing source content into atomic claims is the front half of every
  fact-checking pipeline (**FEVER**, Thorne et al., NAACL 2018), and recent work scores the quality
  of *extracted* claims on atomicity, faithfulness, and coverage (Ullrich et al., 2025) — useful if
  an LLM is generating the candidate fact list.
- **The author vets it.** AI-extracts-then-humans-curate is a real pattern for building a knowledge
  base (**KnowledgeShovel**, Zhang et al., 2022). Your author-review step is exactly this, and it
  buys two things at once: a **grounding source** and an **authoring artifact** the author already
  trusts.

**Honest verdict:** "an author vets an LLM-extracted atomic-fact list, which then serves as the
grounding source" is a **sound synthesis of established components, not a method with its own
benchmark.** Each piece (propositions-as-units, claim extraction, human-in-the-loop curation) is
backed; gluing them into a vetted fact store for a tutor is something you'd be assembling, with the
research as scaffolding rather than a recipe. And note: none of this has been validated *in
education* specifically — curriculum-aligned fact stores are, as of now, active-but-unvetted preprint
territory.

---

## 3. Preprocessing the sources — compression and dedup

You wondered whether to preprocess sources to strip redundant/unnecessary information across them,
and whether that causes more problems than it solves. The research says: it's a real technique with a
real, nuanced trade-off.

- **Compressing retrieved context.** **RECOMP** (Xu et al., ICLR 2024) summarizes retrieved
  documents into a short form before they hit the model (and emits *nothing* when retrieval is
  irrelevant), reporting compression to ~6% with little loss. **LLMLingua** (Jiang et al., EMNLP
  2023) compresses at the token level for inference efficiency — but that's a different goal (speed,
  not a human-readable digest), so don't conflate the two.
- **Reducing redundancy.** **MMR** (Carbonell & Goldstein, SIGIR 1998) — rank by relevance *minus* a
  penalty for similarity to what's already selected — is the standard way to de-duplicate/diversify
  retrieved chunks. (Corpus-level dedup à la Lee et al., ACL 2022, is about *training data*, not a
  RAG store — an analogy, not a method to copy.)
- **The trade-off, honestly.** There's strong evidence that *more* context often *hurts* — accuracy
  degrades when key information sits in the middle of a long context, sometimes below answering with
  no context at all (**Lost in the Middle**, Liu et al., TACL 2024). That argues *for*
  filtering/compression. But over-compression also hurts (RECOMP's and LLMLingua's own ablations show
  it). And your specific worry — that **dedup removes corroborating redundancy and lowers
  reliability** — is **genuinely underexplored**: I found no controlled study isolating it. So treat
  it as a plausible risk to watch, not an established result. The safe reading: compress and dedup to
  fight context bloat, but keep corroboration where a claim is contested.

---

## 4. What happens when verification finds something wrong?

You asked what the post-hoc methods from document 2 actually *do* on a bad response — fix it
themselves, or feed it back to the LLM and re-check? Both patterns exist; pick by cost and control:

- **Detect-only.** **FActScore** (Min et al., EMNLP 2023) and **SelfCheckGPT** (Manakul et al., EMNLP
  2023) *score or flag* — they don't edit. You decide what the flag triggers: suppress the claim,
  regenerate, or route to a fixer.
- **Detect-and-revise (one pass).** **RARR** (Gao et al., ACL 2023) researches evidence and *edits
  the unsupported spans in place*, changing as little as possible. **Chain-of-Verification**
  (Dhuliawala et al., Findings ACL 2024) has the model draft, write its own verification questions,
  answer them independently, and produce a corrected answer.
- **Iterative correct-and-recheck loops** — your "feed it back, ask it to fix, check again." **Self-
  Refine** (Madaan et al., NeurIPS 2023) loops self-critique→revision; **CRITIC** (Gou et al., ICLR
  2024) critiques using *external tools* then corrects; **Corrective RAG / CRAG** (Yan et al., 2024)
  scores the retrieved evidence and triggers a re-retrieve when it looks weak; **Reflexion** (Shinn
  et al., NeurIPS 2023) reflects in words and retries. They stop on a passing check or a max-iteration
  budget.

**For us:** the loop is the natural fit — verify the draft against the vetted source, and if a claim
isn't supported, either suppress it or send it back with the failing check and re-verify. The
detectors are cheap and composable; the revise/loop methods cost more calls but need less bespoke
glue. As with everything here, **none of these were built or evaluated for education** — that's a
genuine gap, and a place our own work could contribute.

---

## 5. Knowing where the student is — position as context

Because our content is multi-page, there's a context dimension the simulation documents didn't need:
**where in the content the student is.** Knowing the current page (and the sequence) lets the help do
things it otherwise can't:

- **Defer correctly.** *"Great question — we get into exactly that on the next page."* Without
  position, the LLM either spoils upcoming material or answers something the activity meant to build
  to.
- **Back-reference.** *"Remember when we covered that on page X?"* — reinforcing rather than
  re-explaining.
- **Scope what the student "should" know.** What they've already read is fair to assume; what's
  coming should not be assumed (or spoiled).
- **Point at the simulation.** Even on a non-sim page, the system should know a sim is coming or has
  been — so it can route a hands-on question to it (*"you'll be able to test that in the simulation
  in a couple of pages"*) instead of answering in prose.

This needs two things: a **structured map of the content** (what's on each page — concepts, learning
goals, which page has the sim, what's upcoming) and the **student's current position** in it.
Position is also the cheapest, most reliable signal feeding the **fifth input** (the [model of the
student](response-types-and-failures.md)) from document 1: you don't have to infer it, you just know
it. Conceptually this is adjacent to knowledge-tracing and prerequisite-sequencing in intelligent
tutoring systems, but for our purposes the practical core is simpler — *give the LLM a content map
and a pointer to the current page.*

---

## How simulations fit back in

Framing the content as a digital textbook doesn't demote the simulations — it locates them. A
simulation is the **executable page**: the one place where the content anchor is a real model you
can connect to, run, and verify against (document 2). The textbook around it is where the curriculum
anchor and the science anchor do the work. The grounding *system* has to span both: ground in the
page's content and trusted science on a
text page, ground in the running model on a sim page, and — using the content map from §5 — know
which kind of page the student is on and which sim is nearby.

---

## What's solid, what's a synthesis, what's a gap

To keep the honesty explicit:

- **Solid research:** propositions-as-retrieval-units (Dense X Retrieval), claim extraction (FEVER),
  HITL knowledge-base curation (KnowledgeShovel), context compression (RECOMP, LLMLingua), retrieval
  diversification (MMR), the long-context degradation that motivates filtering (Lost in the Middle),
  and the full family of post-hoc verify/revise/loop methods (FActScore, SelfCheckGPT, RARR, CoVe,
  Self-Refine, CRITIC, CRAG, Reflexion).
- **Sound but unproven synthesis:** the **author-vetted extracted fact store** as a grounding source
  — real parts, no validated whole, untested in education.
- **Genuine gaps:** that **dedup/compression removes corroboration and lowers reliability** is
  plausible but unstudied; **none** of the verification or source-management methods above were built
  or evaluated for classroom/curriculum content; and "where do unanticipated-question sources come
  from" has no clean answer beyond *vetted corpus + abstain*. These gaps are, again, where our own
  studies could contribute.

---

## References

Verified to a stable arXiv ID / DOI / venue.

**Concise / vetted fact stores**
- Chen et al. (2024), *Dense X Retrieval: What Retrieval Granularity Should We Use?*, EMNLP 2024. https://arxiv.org/abs/2312.06648
- Thorne et al. (2018), *FEVER: a Large-scale Dataset for Fact Extraction and VERification*, NAACL 2018. https://aclanthology.org/N18-1074/
- Ullrich, Mlynář & Drchal (2025), *Claim Extraction for Fact-Checking* (FEVERFact), preprint. https://arxiv.org/abs/2502.04955
- Zhang et al. (2022), *KnowledgeShovel: An AI-in-the-Loop Document Annotation System for Scientific Knowledge Base Construction*. https://arxiv.org/abs/2210.02830

**Source compression / dedup**
- Xu, Shi & Choi (2024), *RECOMP: Improving Retrieval-Augmented LMs with Compression and Selective Augmentation*, ICLR 2024. https://arxiv.org/abs/2310.04408
- Jiang et al. (2023), *LLMLingua: Compressing Prompts for Accelerated Inference of LLMs*, EMNLP 2023. https://arxiv.org/abs/2310.05736
- Lee et al. (2022), *Deduplicating Training Data Makes Language Models Better*, ACL 2022 (training-data dedup, cited as analogy). https://arxiv.org/abs/2107.06499
- Carbonell & Goldstein (1998), *The Use of MMR, Diversity-Based Reranking…*, SIGIR 1998. https://dl.acm.org/doi/10.1145/290941.291025
- Liu et al. (2024), *Lost in the Middle: How Language Models Use Long Contexts*, TACL 2024. https://aclanthology.org/2024.tacl-1.9/

**Verification: detect / revise / loop**
- Min et al. (2023), *FActScore*, EMNLP 2023. https://arxiv.org/abs/2305.14251
- Manakul et al. (2023), *SelfCheckGPT*, EMNLP 2023. https://arxiv.org/abs/2303.08896
- Gao et al. (2023), *RARR: Researching and Revising What Language Models Say*, ACL 2023. https://arxiv.org/abs/2210.08726
- Dhuliawala et al. (2024), *Chain-of-Verification Reduces Hallucination in LLMs*, Findings of ACL 2024. https://arxiv.org/abs/2309.11495
- Madaan et al. (2023), *Self-Refine: Iterative Refinement with Self-Feedback*, NeurIPS 2023. https://arxiv.org/abs/2303.17651
- Gou et al. (2024), *CRITIC: LLMs Can Self-Correct with Tool-Interactive Critiquing*, ICLR 2024. https://arxiv.org/abs/2305.11738
- Yan et al. (2024), *Corrective Retrieval Augmented Generation (CRAG)*. https://arxiv.org/abs/2401.15884
- Shinn et al. (2023), *Reflexion: Language Agents with Verbal Reinforcement Learning*, NeurIPS 2023. https://arxiv.org/abs/2303.11366

*Method note: the source-management and verification papers above were gathered in a focused,
citation-verified literature dive (every entry resolved to a stable arXiv ID / DOI / venue;
unverifiable 2025–2026 preprints were dropped). The grounding-definition and hallucination-survey
papers underpinning this document (Rashkin's AIS, FACTS Grounding, Huang et al.) are cited in full in
[document 2](grounding-literature-review.md).*
