# NewtBot — steering a chatbot's teaching stance with prompts alone

**Paper:** *Student Interaction with NewtBot: An LLM-as-tutor Chatbot for Secondary Physics
Education*
**Authors:** Anna Lieb, Toshali Goel
**Venue:** CHI 2024 Extended Abstracts (non-archival) · https://doi.org/10.1145/3613905.3647957

This is the **lightest-weight system in our scan** — though, to be fair, its *study* is more
carefully built than the system is. NewtBot itself is a plain GPT-3.5 chatbot whose tutoring
behavior is changed *only* by swapping its system prompt — no fine-tuning, no retrieval, no
connection to any simulation or model. It is worth reading not as a method to adopt but as the
**counter-example**: it shows cleanly what prompt-only steering does and, more importantly, what
it does not do. It changes the agent's *teaching stance* without touching the *truthfulness* of
what it says.

## The problem it addresses

The paper's framing is the practical one of giving secondary physics students a "personalized
tutor" chatbot, and asking which *kind* of tutoring behavior students actually respond to. It is
a usability-and-reception study of an LLM-as-tutor, not a grounding or reliability study. The
question it sets out to answer is "do students like this, and which configuration do they like
best?" — not "is the physics it tells them correct, and is it tied to anything they can verify?"

## What it actually does (step by step)

NewtBot uses a **modifiable GPT-3.5 backend** steered purely by **natural-language system
messages** into three different behavioral configurations:

1. **Baseline.** A general-purpose model — effectively identical to base GPT-3.5 with no special
   instruction. The control.
2. **Tutor.** A *setting-specific* configuration: the system prompt shapes the model into a
   general physics-tutoring stance.
3. **Feedback.** A *problem-specific* configuration: the system prompt orients the model toward
   giving feedback on a particular problem.

The only thing that changes between the three is the prompt. There is **no fine-tuning, no
retrieval-augmented generation, and no feed of simulation state or any external corpus.** All
three configurations answer from GPT-3.5's parametric memory; the prompt redirects *how* it
talks, not *what facts it is anchored to*. So whatever "grounding" exists here is grounding to a
desired conversational persona — nothing is tied to a model, a curriculum, or a source the
student could check.

## What it ran on

- **GPT-3.5**, with the same model behind all three configurations.
- A standalone chatbot interface. **It is not connected to a simulation** — there is no PhET-style
  sim, no live state, no computational model in the loop.

## How it was evaluated, and what they found

This is a more structured study than the "lightest-weight" label might suggest, and it is worth
stating the design precisely. **50 German secondary school students** (the count after dropping
incomplete responses) were **randomly assigned, between-subjects, to one of the three
configurations** — Baseline (n≈15), Tutor (n≈17), Feedback (n≈18). So each student saw *one*
configuration, not all three; the comparison is across groups, not within a student. The protocol
was a **pre-test → interaction → post-test** flow: a short instructional video (on magnetic
fields) plus multiple-choice physics comprehension questions, then eight NewtBot tasks, then a
post-survey built from **validated instruments** — the **UEQ-S** (User Experience Questionnaire,
short form: pragmatic and hedonic subscales), selected **UTAUT** performance-expectancy items for
acceptance, a **cognitive-load** scale, and an apprehensions section.

The reported findings are still essentially reception-level:

- Students reported **overall positive experiences using NewtBot.**
- The **tutor** (setting-specific) configuration received the **highest user-experience ratings**
  of the three.
- Self-reported UX items scored high — for example **response speed ≈ 4.7/5, ease of use ≈ 4.4/5,
  and a perceived-"accuracy" item ≈ 4.3/5.**
- **70% said they would use NewtBot for their physics schoolwork**, even though a majority (~72%)
  reported prior apprehensions about using a chatbot for school.

The crucial caveat: none of this is an **independent check of whether the physics NewtBot produced
was correct.** The "accuracy ≈ 4.3/5" figure is *students' own perception* of accuracy, not a
graded audit of the bot's statements. The comprehension questions measure what students answered,
not whether the chatbot's outputs were factually right or where it might have reinforced a
misconception. So the study **does not** report any objective measurement of factual correctness,
misconception handling, or hallucination rate in the chatbot's answers — and with one
configuration per student and no learning-gain analysis reported across the three, even the
"tutor rated highest" result is a user-experience preference, not a demonstrated learning or
correctness advantage.

## The one thing to keep straight: prompting changes stance, not truth

The whole value of NewtBot for us is the boundary it draws. Swapping system prompts changed which
configuration students *preferred* (tutor scored highest on user experience) — so **prompt-only
steering really does move the teaching stance**, at least as students experience it. But nothing
in the design or the evaluation touches *truthfulness*: there is no corpus, no model, no
retrieval, and no objective check of whether the physics is right — only a self-reported
perceived-accuracy rating, which is not the same thing. A nicer tutoring persona built on
ungrounded GPT-3.5 is still ungrounded GPT-3.5. That is the line this paper lets us draw sharply.

## Why it matters for us

- It is the **cheap option made concrete**, and it is the **baseline to contrast richer grounding
  against.** Prompt-only steering does **not** solve any of the grounding failures from
  [document 1](../response-types-and-failures.md): it does nothing for the sim-vs-reality
  collision, the invented mechanism, the phantom affordance, or the inert tangent, because none
  of those are fixed by a friendlier teaching voice.
- It is exactly the **"lighter-weight steering"** the lit review calls out under
  [the pedagogy anchor](../grounding-literature-review.md): tutor behavior can be
  shaped purely by system-prompt configuration into distinct pedagogical modes, "but prompting
  alone gives **no factual grounding** to any corpus or model. It changes the teaching stance,
  not the truthfulness."
- Reading it next to **SimPal** is instructive: both steer an agent with prompts, but SimPal
  grounds the *steering* to a simulation's variables and a teacher's goals, whereas NewtBot
  grounds nothing — it is the floor that shows why the heavier machinery in the content, curriculum,
  and science anchors is needed at all.

## Limitations (stated and evident)

- **No factual grounding of any kind** — no fine-tuning, no RAG, no simulation/model state. This
  is the defining limitation, not a footnote.
- **Not tied to a simulation**, so it speaks to our core setting only by contrast.
- **Evaluation is reception-centric** — validated UX/acceptance/cognitive-load instruments plus
  comprehension questions, but **no independent audit of the chatbot's factual correctness,
  misconception handling, or hallucination rate.** A self-reported perceived-accuracy item is the
  closest it comes, and that measures student impression, not ground truth.
- **Between-subjects design with a small cell size** — each of the three configurations was seen
  by only ~15–18 students, which limits the weight of the "tutor rated highest" comparison.
- Small, single-population sample (**50 German secondary students**) and built on **GPT-3.5**,
  now superseded.
- **Non-archival venue** (CHI Extended Abstracts), which caps how much weight the finding can
  carry.

---

*Verification: this write-up is at **MEDIUM confidence**. **The full-text PDF still could not be
retrieved**: the ACM Digital Library landing page, the `/doi/full` HTML view, the `/doi/pdf`
endpoint, and ResearchGate all returned 403/paywalled, and no open mirror (Semantic Scholar,
OpenReview, an author page, a non-ACM PDF) was found. What follows below is therefore assembled
from the abstract plus **indexed excerpts of the full text surfaced via web search** (dblp confirms
the venue and that it is an 8-page extended abstract, 614:1–614:8), not from a clean read of the
paper itself.*

*A second pass added detail that was NOT in the original scan and that sharpened the doc: the study
is **between-subjects with random assignment** (Baseline n≈15 / Tutor n≈17 / Feedback n≈18; the 50
is the post-filtering valid count), it used **validated instruments** (UEQ-S, UTAUT, a cognitive-
load scale, apprehensions) and a **pre/post comprehension** flow (magnetic-fields video + MC
questions, eight tasks), and there is a **self-reported "accuracy" UX item (~4.3/5)** alongside
response-speed (~4.7) and ease-of-use (~4.4). The earlier flat claim of "reception-only, no
accuracy measurement of any kind" was corrected: there is a *perceived*-accuracy rating and there
are comprehension items, but still **no independent audit of the bot's factual correctness** — the
grounding point holds. The specific sub-scores and the exact "70%/72%" figures come from search-
indexed excerpts and could not be cross-checked against the rendered paper, so treat the precise
numbers as approximate.*
