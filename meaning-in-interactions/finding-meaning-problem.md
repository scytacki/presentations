# Finding Meaning in User Interactions — The Problem

> **This is a straw man.** It is written to be argued with. It centers the one use case we
> have already specified in detail — real-time detection of interesting student moments — but the
> real motivation is broader: we have *several* jobs that all reduce to "find the meaningful
> pattern in a stream of interaction events." The slots marked **[OTHER USE CASE]** below are
> deliberately empty for the reader to fill in. Once they are filled, this framing should be
> rewritten so the general problem, not the detector, is the center of gravity.

This is the first of four companion documents. This one states *what problem we are trying to
solve* — what we mean by "meaning in an interaction," and why it is hard. The others survey what is
already known:

- **[techniques.md](techniques.md)** — the domain-agnostic techniques for extracting meaning from
  interaction streams, drawing on the older and richer web-analytics, marketing, and UX/HCI
  literatures.
- **[edtech-landscape.md](edtech-landscape.md)** — what education technology and learning analytics
  have actually done to find meaning in *student* interaction logs.
- **[ai-architecture-question.md](ai-architecture-question.md)** — the forward-looking argument:
  whether modern transformers/LLMs detect interaction patterns better than the RNN-era methods the
  field relied on, and whether a pretrained frontier model suffices or we must train our own.

The audience and framing match our other presentations: people building and studying classroom
software. Examples are concrete and tied to real systems, not abstract claims about AI.

---

## What is an "interaction"?

Our software produces a continuous stream of events as a student uses it. In CLUE, for example,
this stream has three overlapping shapes:

- **Log events** — a timestamped record of *what the student did* (created an object, typed text,
  deleted a tile, switched tabs), each carrying context: who, which class and group, which
  investigation / problem / section, session, UI state. On the order of ~85 event types.
- **Document history** — the actual sequence of edits to the student's work, from which the full
  document state at any moment can be reconstructed.
- **Comments and derived state** — teacher/AI comments, document summaries, categorizations.

The same is true, with different specifics, of the Activity Player and of most software we build.
The raw material is always the same: **an ordered stream of events, most of them individually
trivial, that collectively encode what the student was doing and — we hope — something about
what they were thinking.**

## What is "meaning"?

"Meaning" is the gap between the raw events and the thing a human actually cares about. A log says
`objectDeleted, objectDeleted, objectDeleted`. A researcher cares about *"the student got
frustrated and wiped out their work"* — or *"the student realized their first approach was wrong
and started over,"* which is the opposite interpretation of the same three events. The events are
cheap and abundant; the meaning is scarce and is what we are trying to recover.

Three properties make this hard, and they recur in every use case below:

1. **The signal is sparse and buried.** The interesting moments are a tiny fraction of the stream.
   Most events are noise relative to any particular question.
2. **Meaning lives in *sequence and context*, not in single events.** No individual click is
   "confusion." Confusion is a *shape* in the trajectory — a hesitation, a repetition, a reversal —
   and often only relative to what this student, or this class, normally does.
3. **The same surface behavior supports opposite interpretations.** Deleting everything can be
   frustration or a fresh insight. Idle time can be disengagement or thinking. Recovering the
   meaning requires more than pattern-matching the surface.

This is why "find meaning in interactions" is a research problem and not just a logging feature.

---

## The use cases

### Use case 1 (specified): real-time detection of interesting moments

This is the one we have already worked out in detail, in the
[detector-driven notifications spec](https://github.com/concord-consortium/collaborative-learning/pull/2742).

**The job.** During a live class, notify a researcher within roughly 30 seconds when a student does
something "interesting," so the researcher can walk over and interview the student *while the moment
is still fresh in their memory.* The target latency and the interview motivation come from prior
research — the DDCI paper (Baker et al., 2024), whose detectors run "with a delay almost under 30 s"
(confirmed from the full text; see [edtech-landscape.md](edtech-landscape.md) and
[papers/ddci-baker-2024.md](papers/ddci-baker-2024.md)).

**Why it is hard, concretely.** "Interesting" is defined by a researcher's evolving intent, not by a
fixed rule. It differs per study. It is rare — a detector that fires on nothing is an expensive
failure, because classroom observation time is scarce and cannot be re-run. And it must be judged
*in real time, from a partial stream,* not in leisurely post-hoc analysis.

**The design already sketches three ways to build a detector**, which double as a map of the
technique space this research covers:

| Detector type | What it is | Where the research lives |
|---|---|---|
| **Rule-based** | explicit patterns, counts, sequences ("3+ deletes in a minute") | hand-built features / rules (§ in [techniques.md](techniques.md)) |
| **ML model** | a classifier trained on labeled historical interaction data | sequence models: HMM → RNN/LSTM → transformer ([techniques.md](techniques.md), [ai-architecture-question.md](ai-architecture-question.md)) |
| **LLM** | a frontier model prompted with the researcher's description of the pattern | pretrained-LLM-on-logs ([ai-architecture-question.md](ai-architecture-question.md)) |

The spec's own working assumption is that **the LLM detector is the starting point** for any new
pattern, and rule-based / ML detectors are *optimizations* for patterns that turn out to need to run
cheaply or at scale. Whether that assumption survives contact with the evidence is exactly what
[ai-architecture-question.md](ai-architecture-question.md) examines.

### Use case 2: **[OTHER USE CASE — to be supplied]**

*Placeholder.* We have other jobs that require identifying patterns in interaction data but are not
the real-time-interview case. Candidates the research below would also serve — for the reader to
confirm, cut, or replace:

- **Post-hoc research analysis** — not alerting during class, but mining a whole corpus of past
  sessions to characterize *how* students used something, or to find recurring trajectories worth
  studying. (Different constraint: no latency pressure, but far more data and a need for
  interpretable, aggregate patterns.)
- **Formative signal for teachers** — surfacing struggle / disengagement to the *teacher* during
  class as a dashboard, rather than to a researcher for an interview. (Different consumer, different
  tolerance for false positives.) *DDCI (Baker et al., 2024) explicitly proposes exactly this as a
  use of the same detector infrastructure — notifying teachers of a disengaged student, a good
  strategy to praise, or an ineffective one to scaffold — so this candidate has direct research
  backing.*
- **Product / UX evaluation** — using interaction patterns to judge whether a feature of our own
  software is helping or hurting the learning goal, or to discover something students are trying to
  do that the software doesn't support. (This is the classic UX-analytics job, covered in
  [techniques.md](techniques.md).)

### Use case 3: **[OTHER USE CASE — to be supplied]**

*Placeholder for a use case the reader has in mind that isn't captured above.*

---

## What this set of documents is for

To answer, honestly and with real citations: **for the job of finding meaning in interaction
streams, what has already been figured out — in education and outside it — and what does that imply
for how we should build.** In particular:

- What techniques exist, and which of our detector types they map to ([techniques.md](techniques.md)).
- What education specifically has tried, how well it worked, and on what architectures
  ([edtech-landscape.md](edtech-landscape.md)).
- Whether the modern-AI bet — transformers/LLMs over RNNs, and *pretrained* over *trained-from-
  scratch* — is supported by evidence yet, or is still an open question we would be taking on
  ([ai-architecture-question.md](ai-architecture-question.md)).

Where the evidence is thin, or applies only *by analogy* to K-12 classroom software, these
documents say so plainly. That honesty matters more than a tidy story.
