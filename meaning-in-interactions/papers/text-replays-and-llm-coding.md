# Deep-Dive: Text Replays — Coding Behavior from Log Replay (and whether GPT can do it)

Two tightly-linked papers on **labeling student behavior from a replay of the log data instead of
live observation** — the method, and a recent test of whether an LLM can do the same coding.

> **Baker, R. S. J. d., Corbett, A. T., & Wagner, A. Z. (2006). Human Classification of Low-Fidelity
> Replays of Student Actions.** *EDM Workshop at ITS 2006*, 29–36. **Open** (educationaldatamining.org).
> Full text read.
>
> **Maier, C., & Baker, R. S. (2025). Can GPT Detect Gaming the System in Text Replays?** *ICCE
> 2025 (33rd Int. Conf. on Computers in Education)*. **Open** (upenn.edu). Full text read.

**Why it's here:** this is the literature on the exact thing we'd need to do — get ground-truth
labels for **past** data (CLUE document-change replays) **without** live BROMP observation — and on
whether a **GenAI** model could do that coding. Highly decision-relevant.

---

## Part 1 — Text replays (Baker, Corbett & Wagner, 2006): the method

**The idea.** Human observation is accurate but *expensive* (their prior gaming detector needed
>60 hours of observation + logistics). Instead, code from a **replay** of the log. The paper lays
out a **fidelity spectrum** of what a coder sees:

- **High:** live co-located observation, exact video replay (face, body, voice, screen).
- **Middle:** exact screen replay → *limited* screen replay (reconstructed screen from logs).
- **Low (this paper):** **text replays** — a purely textual rendering of an action sequence (time
  relative to clip start, action type, interface widget, input entered, system's assessment
  correct/incorrect/help). Generated **automatically from standard log files.** No face, no audio,
  no mouse movement, no partial responses.

**CLUE mapping:** replaying document changes is essentially a **limited screen replay / text
replay** — log-derived, no face or audio. So this is our regime.

**What they validated.** Coded **gaming the system** on ASSISTments-style tutor data; two coders,
909 text-replay classifications, compared against live observation and a gold-standard ML gaming
detector. Results (all from the paper):

- **Reliability is lower than live, but usable.** Text-replay inter-rater **Cohen's κ = 0.58**
  (moderate) vs. live co-located **κ = 0.83** (very high). Lower — as you'd expect from less
  information.
- **Aggregate accuracy is comparable.** Per-student gaming *frequency* from text replays correlated
  with the gold-standard detector **as well as live observation did** (text **r = 0.57/0.59** vs.
  detector; live **r = 0.54**). At the aggregate level, the low-fidelity method loses little.
- **~5× faster.** ~9 seconds per clip vs. ~1 minute live; ~15% of the time cost.
- **Works retrospectively.** Because text replays come from ordinary log files, they can be run on
  **existing corpora without a special observation study** — and clips can be *selected by criteria*
  (e.g. every attempt at one skill across a week), not just random windows.

**The limitation that matters most for us (their words):** low-fidelity coding works for behaviors
*expressible in the log*, but **"it is not clear to what degree low-fidelity observations will be
successful for detecting affect, as opposed to specific behaviors such as gaming. Detecting affect
may depend on subtleties... only capturable through higher-fidelity observation techniques."** Also:
it can't see anything *outside* the software (can't tell off-task talk from on-task talk with a
teacher). **So: behavior/strategy = codeable from replay; affect (bored/frustrated) = probably not,
without face/voice.** This directly confirms the intuition that screen-only replay is weaker for
affect than live observation.

*(Follow-on: Baker & de Carvalho, 2008, "Labeling Student Behavior Faster and More Precisely with
Text Replays," extended this to **text-replay tagging** — multiple tags per clip — now a standard
EDM labeling tool.)*

## Part 2 — Can GPT do the coding? (Maier & Baker, 2025)

The direct test of the GenAI extension: give **GPT-3.5 / GPT-4** the *same text replays a human
codes* and few-shot-prompt it to label **gaming**.

- **Setup:** public ASSISTments text-replay dataset, 1,060 human-labeled clips (64 gaming / 996 not),
  1,050 for test; six few-shot examples; optional in-prompt "gaming patterns" and optional
  explanation request.
- **Result — above chance, but weak, and worse than classical models.** Best **GPT-3.5 κ = 0.171**,
  best **GPT-4 κ = 0.13** (GPT-3.5 actually beat GPT-4 here). A prior **knowledge-engineered** model
  ported to the same data scored **κ = 0.256**, and hybrid ML models **0.173–0.248** — i.e. the LLM
  **lost to the non-LLM detectors.**
- **Prompt findings:** including explicit gaming *patterns* helped; asking GPT to *explain* its
  answer did **not** help (sometimes hurt); gains plateaued at ~6 examples.
- **The authors' explanation — and the key lesson for us:** *"GPT's training data likely contains
  little that resembles text replays, making it difficult for GPT to interpret interaction data
  formatted as text, even though it is equally readable for humans."* An earlier attempt (Zhang et
  al., 2024) that turned each attempt into a sentence also "did not perform better than a classic
  machine learning algorithm." → This is **direct evidence for the serialization problem** in
  [../ai-architecture-question.md](../ai-architecture-question.md): a human-readable log is *not*
  automatically an LLM-legible one.

**Why it matters for us.**
- **The replay-labeling strategy is sound and validated** — humans *can* code behavior from
  log/screen replay, retrospectively, on existing data. Good news for using CLUE document-change
  replays instead of re-running activities.
- **Temper expectations for affect and for GPT.** Affect is the hard case from replay; and a
  *frontier LLM prompted on raw text replays* was, as of 2025, **above chance but beaten by a
  purpose-built classical model** — largely a **data-format** problem, not (only) a reasoning one.
- **The lever is serialization**, plus possibly chain-of-thought / reasoning models — the authors'
  own "future work." If we pursue a GenAI coder, how we render the CLUE log for the model is likely
  the deciding variable, echoing the differential-sequence-mining
  [abstraction step](differential-sequence-mining-kinnebrew-2013.md).

**Verification.** Both PDFs read in full. Baker 2006: fidelity spectrum, κ = 0.58 vs 0.83, r = 0.57/
0.59 vs 0.54, ~9 s/clip, and the affect-limitation quote are from the primary text (pp. 1–8). Maier
& Baker 2025: κ values (GPT-3.5 0.171, GPT-4 0.13), the Paquette κ = 0.256 comparison, and the
"training data contains little that resembles text replays" quote are from the primary text
(Tables 1–2, §4–5). High confidence.
