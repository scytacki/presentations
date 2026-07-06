# Research Directions

The **actionable** companion to the analytical docs. Where the others describe *what the research
says* ([techniques.md](techniques.md), [edtech-landscape.md](edtech-landscape.md),
[ai-architecture-question.md](ai-architecture-question.md)) and *what the problem is*
([finding-meaning-problem.md](finding-meaning-problem.md)), this one lists *what we could actually
build or test.* Each direction links back to the doc/paper that backs it rather than restating it.

Two groups, deliberately separated:

- **Group 1 — Well-defined and feasible.** Curated (the user's judgment). These are directions we
  believe are clearly scoped and buildable now. Kept small on purpose.
- **Group 2 — Proposed directions to explore.** Candidates surfaced during this work, not yet vetted
  for scope or priority. Raw material for promotion into Group 1 (or eventual rejection).

A future **Rejected ideas** section will record what we considered and chose not to pursue, and why.

*Structure for each entry: the question · why it matters · what's known / unknown · what it needs ·
feasibility · links.*

---

## Group 1 — Well-defined and feasible

### 1.1 Cross-application GenAI affect detector

**The question.** Can a **GenAI affect detector** — a prompt plus a log serialization, validated
against human affect labels on one application — be applied to a **different** application and still
work, *better than* feature+regression detectors (which provably don't transfer)?

**Why it matters.** We build many activities and simulations. A detector that only works in the one
app it was trained on doesn't scale to our situation. Cross-**application** transfer is the
recognized, largely-unsolved prize; if a GenAI approach closes it, it changes how we do
meaning-detection across everything we build — and it's the strongest argument for the GenAI path
over the classical one.

**What's known / unknown.**
- *Known:* feature+regression affect detectors are hand-crafted and **system-specific**, and
  cross-system transfer is "barely addressed" with no documented success (systematic review,
  arXiv:2310.13711; [edtech-landscape.md](edtech-landscape.md) §2c). Behavioral *gaming* detectors
  have transferred across systems, but affect is harder. Affect is also the **hard case** for
  log-only coding (Baker et al., 2006). The one near-neighbor GenAI result — GPT coding gaming from
  text replays — was **weak** and blamed **serialization** (Maier & Baker, 2025;
  [papers/text-replays-and-llm-coding.md](papers/text-replays-and-llm-coding.md)).
- *Unknown:* whether a GenAI reading a *semantic* representation generalizes across applications for
  affect. **No such study exists** — this would be novel.

**What it needs.**
- **Raw-log affect-labeled data from two applications.** The user reports existing relationships with
  both **ASSISTments** and the **Betty's Brain** researchers, so the datasets are likely obtainable
  *if each IRB permits* — this is the enabler that moves the direction into Group 1.
- **Raw logs, not just feature vectors.** The catch (see
  [papers-to-obtain.md](papers-to-obtain.md) → Datasets): affect datasets are often released as
  engineered features + labels; a GenAI-reads-the-log approach needs the raw event stream (or a
  text-replay rendering). Confirm raw-log availability early.
- A **serialization** per app (depends on 2.1), a **prompt**, and a way to **verify** — human
  affect labels (obtainable retrospectively via text-replay coding, 2.4, though affect is the hard
  case there).

**Feasibility.** Plausible now, given the data relationships. Main risks: (a) obtaining *raw* logs
rather than feature vectors; (b) IRB timelines on both sides; (c) serialization design (2.1) is a
real sub-problem; (d) affect labels from replay may be noisy. Because two labeled apps are in reach,
transfer can be tested **both directions**.

**Links.** [edtech-landscape.md](edtech-landscape.md) §2c ·
[ai-architecture-question.md](ai-architecture-question.md) (open questions) ·
[papers/text-replays-and-llm-coding.md](papers/text-replays-and-llm-coding.md) ·
[papers-to-obtain.md](papers-to-obtain.md) (Datasets).

---

## Group 2 — Proposed directions to explore

*Proposed, not yet curated. Order is rough, not a priority ranking.*

### 2.1 Serialization study — how to render an interaction stream for an LLM

**The question.** What representation of a CLUE (or any) event stream makes an LLM read it best — raw
log lines, the document-history diff, a rendered natural-language summary, a text-replay format, or a
hybrid?

**Why it matters.** Every LLM-on-logs direction (1.1, 2.3, 2.5) depends on it, and the evidence says
serialization is the single biggest lever — *"GPT's training data likely contains little that
resembles text replays"* (Maier & Baker, 2025). Human-readable ≠ LLM-legible.

**What's known / unknown.** *Known:* serialization matters a lot (Maier & Baker; LLM-time-series
work; LogLLM's long-context limits, [ai-architecture-question.md](ai-architecture-question.md)).
*Unknown:* the right rendering for open-ended document work like CLUE.

**What it needs.** A labeled task to measure against (could piggyback on 1.1's data), a few candidate
serializations, and an eval loop. **Feasibility:** cheap, high-leverage, low-risk — a natural first
step that de-risks the others.

### 2.2 LLM "interesting-moment" detector for CLUE (DDCI-style)

**The question.** Can a **prompted frontier LLM** reach usable *precision and latency* as a real-time
"interesting-moment" detector on CLUE document streams — not just interpret them after the fact?

**Why it matters.** This is the LLM variant of the detector in the
[CLUE-330 spec](https://github.com/concord-consortium/collaborative-learning/pull/2742), and the
spec's premise is "LLM detector first." We should test whether that premise holds on our data.

**What's known / unknown.** *Known:* ClickSight shows a prompted LLM can *interpret* raw clickstreams
([papers/clicksight-radmehr-2025.md](papers/clicksight-radmehr-2025.md)); DDCI
does *detection* with classical ML at ~30 s. *Unknown:* false-alarm rate on open-ended CLUE work —
interpretation ≠ reliable detection.

**What it needs.** CLUE logs, a concrete "interesting" construct, and labels (2.4). Depends on 2.1.
**Feasibility:** medium; directly extends existing engineering.

### 2.3 Text-replay labeling of CLUE data (human + GenAI-assisted)

**The question.** Can we get ground-truth labels for **past** CLUE data by replaying document
changes and coding them — and can a GenAI coder match human coders on that same replay?

**Why it matters.** Labels are the bottleneck for every trained/verified detector. Text replays let
us label *existing* data without re-running activities (validated method, ~5× faster than live).

**What's known / unknown.** *Known:* text replays are validated for behavior (Baker et al., 2006,
κ moderate but aggregate accuracy ≈ live); GenAI coding of replays is so far **weak** (Maier &
Baker, 2025). Affect is the hard case for replay coding. *Unknown:* how well either works on
CLUE's open-ended document changes.

**What it needs.** A replay renderer for CLUE documents, a construct, and human coders.
**Feasibility:** medium; **enables 1.1, 2.2, and any trained detector**, so high strategic value.

### 2.4 GenAI feedback agent + compact-and-query infrastructure

**The question.** Can a **GenAI feedback agent** replace the current rule-system-plus-states feedback
— detecting meaning in the interactions and crafting suggestions — and what shared
**compact-and-query** machinery does it need?

**Why it matters.** This is use-case 2, variant B in
[finding-meaning-problem.md](finding-meaning-problem.md): potentially less annoying, easier to extend
to new sims/goals, at the cost of non-determinism and lost auditability. The **compact-and-query**
loop it needs (summarize the stream + query the full log / alternative representations) is the
through-line shared with the detector work.

**What's known / unknown.** *Known:* the current rule+states approach; the `-REL/-IRR` abstraction as
a compaction precedent ([papers/differential-sequence-mining-kinnebrew-2013.md](papers/differential-sequence-mining-kinnebrew-2013.md));
grounding the *content* of help is worked in [../grounding-llm-help/](../grounding-llm-help/).
*Unknown:* precision/annoyance, and how to give authors/researchers auditability over a
non-deterministic agent.

**What it needs.** Log compaction + query tools, a target simulation, and the grounding machinery.
**Feasibility:** larger build; depends on 2.1 and overlaps grounding-llm-help.

### 2.5 Foundation model on unlabeled interaction logs

**The question.** Could **self-supervised pretraining on unlabeled** CLUE interaction logs yield a
reusable representation that makes downstream detectors cheap to build, sidestepping the labeling
bottleneck?

**Why it matters.** It's the structural answer to "we never have enough labels" — pretrain once on
abundant unlabeled logs, fine-tune per construct with few labels.

**What's known / unknown.** *Known:* log-anomaly transformers (e.g. LogBERT/LogLLM) do this for
system logs; no education-specific version is established
([ai-architecture-question.md](ai-architecture-question.md)). *Unknown:* whether education-scale
unlabeled logs are enough, and whether the representation captures *meaning* vs. surface patterns.

**What it needs.** A large unlabeled CLUE log corpus and pretraining infrastructure.
**Feasibility:** research-y, longer horizon; highest uncertainty.

### 2.6 Also latent — the other use cases

Lower-defined directions carried from [finding-meaning-problem.md](finding-meaning-problem.md) that
each reduce to finding meaning in interactions: **post-hoc corpus analysis** (mine past sessions for
recurring trajectories), a **teacher-facing formative signal** (DDCI explicitly proposes this), and
**product/UX evaluation** (does a feature help; what are students trying to do that we don't
support). Kept here as pointers until someone wants to promote one.

---

## Rejected ideas

*(None yet.)* Format when we add them: **the idea · why it was rejected · what would change our mind.**
