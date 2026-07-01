# Deep-Dive: ClickSight (Radmehr et al., 2025)

> Radmehr, B., Shved, E., Güreş, F. B., Singla, A., & Käser, T. (2025). **ClickSight: Interpreting
> Student Clickstreams to Reveal Insights on Learning Strategies via LLMs.** AIED 2025.
> **arXiv:2505.15410**. **Open access.**

**This is the closest existing work to our "LLM detector."**

**The problem it addresses.** Clickstream data from digital learning environments is rich but "hard
to interpret due to their high dimensionality and granularity" (abstract). Turning a raw event
stream into a statement about *what learning strategy a student was using* normally takes bespoke
feature engineering per environment.

**What it does.** Feeds **raw clickstreams plus a list of candidate learning strategies** to a large
language model and has it **generate textual interpretations** of the student's behavior. Crucially,
it is **in-context / prompted — no fine-tuning.** The study compares **four prompting strategies**
and investigates a **self-refinement** step (the model critiquing/revising its own interpretation).

**What it ran on / how evaluated.** A digital learning environment's clickstream data with a defined
set of learning strategies; evaluation compares prompting strategies and the effect of
self-refinement on interpretation quality. (Specific dataset and metrics to be read from the full
paper — it is open on arXiv.)

**The key caveat for us.** It demonstrates **interpretation**, not reliable **detection.** "Explain
what this student was doing" is a softer task than "fire a low-false-alarm alert the instant
something interesting happens." ClickSight is an existence proof that a prompted frontier LLM can
read raw student logs usefully; it is *not* evidence of the precision/latency our detector needs.

**Why it matters for us.**
- It validates the **prompt-a-frontier-LLM-on-raw-logs** path as live and publishable **in
  education, right now** — the exact approach our spec calls the "LLM detector."
- Its **no-fine-tuning, list-of-target-patterns-as-input** design mirrors our "researcher describes
  the pattern in words" workflow almost exactly.
- The **self-refinement** idea is a concrete technique we could adopt to cut false alarms.

**Limitations / open questions it leaves.**
- Interpretation quality ≠ detection reliability; false-alarm behavior on rare events is unaddressed.
- Learning *strategies* in a structured environment may be easier than open-ended CLUE/AP work — a
  possible domain mismatch.
- Cost/latency of per-window LLM calls at classroom scale not the focus.

**Verification.** Title, all five authors, year, venue (AIED 2025), and arXiv ID **directly verified
by fetching the arXiv abstract**; confirmed it is an in-context/prompted (not fine-tuned) pipeline
over raw clickstreams with four prompting strategies and self-refinement. Higher confidence than the
scan-only citations.
