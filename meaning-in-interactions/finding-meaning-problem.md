# Finding Meaning in User Interactions — The Problem

> **This is a straw man.** It is written to be argued with. Two use cases are now specified —
> real-time detection of interesting moments (for researcher interviews) and real-time feedback to
> students — and both reduce to "find the meaningful pattern in a stream of interaction events."
> A **through-line has emerged**: the *compact-and-query* loop (decide what to summarize, what to
> read in full, which view of the stream makes a pattern legible) is shared between a researcher
> building a detector and a GenAI agent reading a student. The next rewrite should make **that
> general problem** — not the detector specifically — the center of gravity. Remaining candidate
> use cases are still listed, unfleshed, near the end.

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

### Use case 2 (specified): real-time feedback to students

Give a student, *while they work*, a piece of text feedback suggesting what to try next — the LLM
helper of the [grounding-llm-help](../grounding-llm-help/) series, now viewed from the
*interaction-reading* side. The consumer is the **student**, not a researcher, and it runs
continuously, so the bar is different from use case 1.

**It is really a conversation — a lopsided one.** The feedback is a suggestion to *act*. The student
can't reply in writing, but they **respond by re-running the model with different parameters** to
address what the suggestion said. So each suggestion and the student's next actions form an implicit
back-and-forth. That framing raises the stakes: a suggestion that is wrong, redundant, or badly
timed doesn't just miss — it reads as **annoying**, and the quality of the whole "conversation" is
capped by how well the system covers the situations that actually arise.

**When to speak is its own question.** Deciding the *moment* to offer feedback — not just what to say
— is a research problem in its own right, and "the student's affect changed" is only one candidate
trigger. [when-to-intervene.md](when-to-intervene.md) surveys the alternatives (impasses,
disengagement behaviors, the assistance dilemma, and the "Clippy effect" timing lesson from
software-help systems) and why over-eager intervention is the central failure mode here.

**Variant A — rule system + states (what we do now).** A rule engine maps the interaction stream
onto a list of **states**, and feedback is chosen by the student's current state. This is, at
bottom, the **detector approach of use case 1** — but with a much heavier detection burden. Instead
of a researcher hand-specifying a *handful* of interesting patterns, someone must enumerate **many**
cases per state to get usable coverage, and every gap surfaces immediately as bad feedback. Coverage
*is* the product.

**Variant B — a GenAI feedback agent (not yet built).** Replace the rule/state machine with a
generative model that both **detects meaning in the interactions** and **crafts the suggestion.**
The appeal:
- *Less annoying, more interesting* to work with than fixed rules.
- *Easier to extend* to a new simulation or a new goal for an existing one — instead of enumerating
  all the rules and states, we specify **what information the model needs** to behave the way we
  want.

The costs are the mirror image:
- *Non-determinism* — the same situation may not produce the same suggestion.
- *Not auditable by inspection* — an author/researcher can't read a rulebook to predict or vet the
  behavior, unlike Variant A. (This is the same auditability/grounding tension the
  [grounding-llm-help](../grounding-llm-help/) docs work through for the *content* of the help; here
  it recurs for the *reading of the interactions* that precedes the help.)

**Why this is the same problem, not a neighbor of it.** The GenAI agent can't simply be fed the raw
interaction log — these logs get large. It needs (a) a **compacted** representation of what the
student has done, and (b) a **tool to query** the full log, and probably *alternative
representations* of it, on demand. **That compact-and-query loop is exactly what a researcher does
when working out how to detect an event** (use case 1) — decide what to summarize, what to look at
in full, which view of the stream makes the pattern legible. So this use case doesn't just *use* the
detector research; it shares its core mechanism. (This is the through-line worth pulling on as this
straw man is rewritten around the general problem.)

### Other candidate use cases (named, not yet fleshed out)

Still placeholders — for the reader to confirm, cut, or replace:

- **Post-hoc research analysis** — mining a whole corpus of past sessions to characterize *how*
  students used something, or to find recurring trajectories worth studying. (No latency pressure,
  but far more data and a need for interpretable, aggregate patterns.)
- **Formative signal for teachers** — surfacing struggle / disengagement to the *teacher* during
  class, rather than to a researcher for an interview. *DDCI (Baker et al., 2024) explicitly proposes
  exactly this — notifying teachers of a disengaged student, a good strategy to praise, or an
  ineffective one to scaffold — so this candidate has direct research backing.*
- **Product / UX evaluation** — using interaction patterns to judge whether a feature of our software
  helps or hurts the learning goal, or to discover something students are trying to do that the
  software doesn't support. (The classic UX-analytics job, in [techniques.md](techniques.md).)

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
- **How to compact an interaction log and query it on demand** — the through-line shared by the
  detector and feedback use cases. What to summarize vs. read in full, which alternative
  representations make a pattern legible, and what a query tool over the raw stream should offer.
  This touches sessionization and compression ([techniques.md](techniques.md)) and LLM
  serialization / long-context / memory ([ai-architecture-question.md](ai-architecture-question.md)),
  and it echoes the source-compression and querying machinery in
  [../grounding-llm-help/grounding-in-practice.md](../grounding-llm-help/grounding-in-practice.md).

Where the evidence is thin, or applies only *by analogy* to K-12 classroom software, these
documents say so plainly. That honesty matters more than a tidy story.
