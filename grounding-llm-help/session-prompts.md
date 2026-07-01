# Session Prompts — "Ground LLM responses…"

*This file is **meta** — a verbatim record of the user's prompts from the working session that
produced the grounding-llm-help documents (session `2c084b7a`, 2026-06-26 → 2026-06-30). It exists
so the reasoning and requests behind the docs are recoverable even though the docs themselves only
show the finished state. Only the user's own prompts are captured here (assistant responses,
tool-call notifications, and IDE auto-context are omitted). Text is reproduced as typed, including
original typos. Decisions made through Claude Code's multiple-choice UI (the `AskUserQuestion`
tool) are interleaved in place and marked **(multiple-choice)**; selected options are in bold and
free-text replies are quoted.*

---

## 2026-06-26 — kickoff & research

### 20:31 — the goal
> I want to do some research on how LLMs can be influenced by "facts". In our case we usually want
> these to be scientific facts. The context is that we have simulations and models that are embedded
> in activity systems. We want to use LLMs to help students working with these systems. But we want
> to restrict this help to be founded in scientific facts.
>
> I suspect this has been an area that has been researched. I'm also not that clear of what it means
> to "ground the responses".
>
> So to make a document that covers both aspects:
> - find papers that explore this area and review them
> - flesh out the different types of responses the LLM might provide and come up with scenarios where
>   the responses could be wrong.
>
> I'm doing this work here because the resulting document is kind of like a presentation. In this case
> it will be more a document than a presentation, but the audience and context is the same as these
> presentations.

### 20:33 — ran the research
Invoked the **deep-research** workflow with the research question passed as args:

> How can LLMs be grounded/restricted to scientific facts when helping students working with science
> simulations and models embedded in activity systems? Cover grounding techniques (RAG, knowledge
> grounding, retrieval-augmented generation, fact verification, hallucination mitigation), what
> "grounding responses" means, and research on factual reliability of LLM tutors in science education.

### 20:33 — scope-setting **(multiple-choice)**
- **What should the responses be grounded *to*?** → **all four**: the simulation's own model,
  curriculum/activity content, general scientific consensus, pedagogical correctness.
- **Where should the document put its weight?** → free-text: *"The lit review part can be done
  independently, however I think it might be best for us to discuss the response types and failure
  scenarios together first. At this point I think the document should balance both, but it might be
  best if we end up making more than one document to cover all of this."*
- **How will students interact with the LLM help?** → **all four**: asks questions, hints/nudges,
  feedback on actions, explaining results.

### 20:44 — framing & scenarios **(multiple-choice)**
- **Does the 'four truths that can conflict' framing match how you think?** → **Yes, build on it.**
- **How should we develop the scenarios?** → **Few, deep, in our sim** (4–6 fully developed with the
  projectile sim).

### 21:24 — model vs. consensus is not a bug; the "true but irrelevant" failure; mental model
> In the resposne-types doc, the conflict between the simulation's model and the scientific consensus,
> is not a bad thing. In most cases it is important for the user to understand the model is a
> simplification. So I wouldn't say the model is the primary ground truth, but the responses need to
> always take into account this simplification. The best response might be one that does differ from
> the simulation, but the response needs to be contextualized or framed with how it is different from
> the simulation.
>
> This simplification aspect, might also point to another kind of failure. The LLM can make a claim
> that is true but actually has no real affect on the "environment" being explored. In the case of the
> projectile sim, the student or LLM might start thinking about the affect of the moon or coriolis on
> projectiles. While these do have an affect, in the context of projectiles traveling a short distance
> they should have no noticable effect. If the student starts asking about something like this, it
> shouldn't be shutdown as having no effect because these are interesting advanced concepts the student
> is probably proud to have identified. So the LLM should identify the issue, and find a way to
> encorage the student but also find a way to bring them back to what matters. However if the LLM knows
> the student already understands what matters then these kinds of explorations could be allowed if the
> curriculum designers and/or teachers want to explore them.
>
> This is probably going into an area that is out of scope, but it seems good for you to have a full
> picture of what I have in mind. I believe the last bit above, means the LLM is building its own model
> of what the student understands. Something like "what is the mental model of the student". So to be
> useful this student understanding model should be combined with the "facts" when the LLM is figuring
> out how to respond.
>
> I haven't read the 5 detailed scenarios yet or the grounding-lit doc yet. My comments above are just
> based on the response-types doc before the scenarios.

### 21:44 — Socratic back-and-forth for scenario 1
> For the ideal answer of scenario 1, I would adjust it. But it is hard to keep this concise.
>
> Ideally if this was a project that wants a back and forth with the student and the LLM. The LLMs
> answer might initially be: "What is different about a feather compared to what you've been
> launching?". If they arrive at weight then the LLM could ask them to try that scenario. However it
> also needs to find a way to tell them the simulation might model it wrong since it is a
> simplification of reality. This could lead to a discussion about what might be missing from the
> simulation that makes it not model something like a feather correctly.
>
> Different projects probably will have different desires for how much back and forth there should be.
> What I described above would be one that lets the LLM use something like the socratic method to help
> the student discover things. Other projects might want to just use something similiar to your
> existing answer so the student gets an answer and moves on.

---

## 2026-06-27 — trimming the response-types doc

### 20:31 — drop scenario 3
> For the audience and goal of this document Scenario 3 is unnecesary. It will be obvious that a stale
> state will cause problems. And the solution to that problem is clear.
>
> I'm not sure if that clarification changes other parts of this response-types doc

### 20:50 — context graphs are an implementation detail
> Hold until I've finished reading the response-types doc.
>
> Looking at the "beyond the four truths" section, it brings up context graphs. That is just one way
> to store the interaction history. context graphs could also be used to store the mental model itself.
>
> However, context graphs seem like an implementation detail that I don't think we should bring up
> here. The audience of this might not have any understanding of them, so would be distracted by the
> term.

### 20:54 — align the second doc
> OK I've finished the first doc, please update the second doc to align with the first.

---

## 2026-06-28

### 13:44 — commit
> OK please commit and push these documents to main. I do want to reivew the lit review more to clarify
> somethings, but the current state is good enough to share.

---

## 2026-06-29 — SimPal deep-dive & the sim-grounding thread

### 13:23 — expand SimPal, per-paper docs
> Can you expand on what SimPal actually does/did? Perhaps these more detailed summaries of research
> should go into separate documents per paper or group of papers.

### 13:26 — per-paper docs rollout **(multiple-choice)**
- **How to roll out per-paper detail docs beyond SimPal?** → **Key papers only** (~5–6 closest to our
  work).
- **Commit per-paper docs as you go?** → **Hold all uncommitted** (review alongside lit-review
  revisions).

### 13:47 — how does SimPal know the sim's variables?
> For SimPal. I don't understand the process. How does the LLM know the variables it can extract? Is it
> feed a description of the simulation? Or does it only get the information that the teacher provides to
> it during the conversation?
>
> The reason I'm asking this is because I wonder if the teacher starts talking about some variable the
> simulation doesn't actually have will this unsupported variable be extracted and used in the prompts
> SimPal generates. Or are these variables grounded in the actual sim somehow.
>
> I could imagine that the LLM analyzes the source of the sim, or explores it with a browser MCP.
> However given that they were using GTP-3.5 I doubt they had access to these capabilities.

### 14:10 — SimPal's predecessor
> For SimPal what is the original conversational agent that this work improves? Was it something
> provided by PhET or Golabz? Is there information about this original system?

### 15:04 — deployment reality check
> So SimPal was never actually used by teachers?

### 15:07 — apply to the other papers
> Yes please apply this to the other papers.

### 16:10 — referential validation against sim inputs/outputs; Truth C provenance
> Continue holding it.
>
> For "grounding to the simulation's own model and state" in the groudning-lit-reivew, I wonder if
> there examples of validating the LLM's response against the list of available inputs and outputs of
> the simulation. This is different than executing the model, and it it doesn't try to have the LLM run
> the simulation. It is a second pass over the response to see if it is refering to inputs or outputs
> that don't exist. Perhaps there is a more general version of this that has been studied.
>
> This seems like a modified version of what is briefly described in the "Truth C" section. In this
> current "Truth C" section I don't see the connections to the reviewed papers. Are there papers
> related to this "Truth C" section?

### 17:24 — GenAI building new simulations (marked out of scope)
> Regarding the alignment of the mcp-sim paper with our work: we have considered having the Gen AI
> actually build new simulations in order to work with the student. What would be different compared to
> the mcp-sim paper that the simulations are not built just to solve a problem. Instead they are visual
> and interactive, and are intended to get at a student mis conception or explore some tangent (if the
> project supports this tangent exploring).
>
> In some sense this is similar to the models that are being built by the MCP-SIM. We'd probably want
> to employ the same loop so that any models the GenAI builds converge. But then we'd need to add a few
> layers on top of that:
> - what do we expect a student do with this simulation and what would the outcome be when they do that.
> - is that expected outcome going to help them learn the intended concept?
> - is this new simulation grounded in the science consensus: it'd be possible to build a converging
>   simulation that doesn't have a basis in reality. That might actually be OK in some cases, but in most
>   cases we'd want any simulations created by the GenAI to model realistic things.
>
> While I think this direction of having GenAI build new simulations is interesting, I think it should
> be considered out of scope for this series of documents. So my suggestion would be to just mention it
> as another direction we are considering that aligns with what is demonstrated in this mcp-sim paper. I
> don't know if that is worth including in the grounding-lit-review doc, or just in the specific mcp-sim
> doc.

### 19:15 — is sim-description validation really unstudied?
> I wonder about the selected statement. It seems the description of the sims inputs and outputs is just
> another source the claims can be validated against. So I wonder if this area would really be
> unstudied. But perhaps you have something in mind that isn't just a source which is a description fo
> the sims inputs and outputs.

### 19:27 — description covers behaviors, not just referents; Truth C has drifted
> A description of the sim would not just be a list of inputs and outputs. It would have some
> description of what happens when the inputs are changed and generally how the sim behaves. It is very
> likely this description would not be able to cover every case or combination. So saying it would only
> catch "referential" checks is incorrect. It could also check the behaviors that were documented in the
> description. But since not all behaviors can be checked, this is why we likely need to actually execute
> the sim.
>
> However now that I'm looking at this "Truth C" section it seems like we've gotten off track somehow.
> We are talking about grounding in the sim, but this section is about grounding to scientific consensus.

### 21:57 — the docs over-focus on simulations *(interrupted, then retyped — see 22:13)*
> As I'm looking through the grounding-lit reivew doc I realize it (and probably other documents) are
> focusing too much on the simulations. The simulations are a big part of our content, but often the
> content does have text and images, and questions. The questions can be open respose, or drawing
> question or multiple choice.
>
> I noticed this when reading the Truth C section. So when the LLM or GenAI is helping the student or
> teacher with the non sim content, then the scientific consensus grounding seems like what is required
> with no sim fallback. The content itself is a source to be grounded with, but there will need to be
> additional sources since we won't want students to have to read all of these sources.

### 22:13 — same point, expanded (the version that stuck)
> As I'm looking through the grounding-lit reivew doc I realize it (and probably other documents) are
> focusing too much on the simulations. The simulations are a big part of our content, but often the
> content does have text and images, and questions. The questions can be open respose, or drawing
> question or multiple choice. So this content is more like a digital text book.
>
> I noticed this when reading the Truth C section. So when the LLM or GenAI is helping the student or
> teacher with the non sim content, then the scientific consensus grounding seems like it would be
> primary. If there is a simulation in the content the grounding system should take that into account
> because we still would want to point the user at the sim or know that the user will be tackling the
> sim in the near future. The content itself is a source to be grounded with, but there will need to be
> additional sources since we won't want students to have to read all of these sources.
>
> Here are some questions that I now have:
> 1. Where do we get these non-content sources? A student could potentially ask about many things we
>    don't anticpate or don't have the time to anticipate.
> 2. With the post-hoc approaches in the Truth C section, what is done when they find something wrong?
>    Do they modify the response themself? Do they provide feedback to the LLM and ask it to correct the
>    problem, and then check again?
> 3. Are there other approaches to managing or storing these sources or facts? I guess I'm thinking of
>    something like RAG except done in some way that is more concise. The goal would be to have GenAI
>    make a list of "facts" that the author of the content could review and vet for correctness.
> 4. More generally has anyone tried preprocessing the sources to remove redundant and unnecessary
>    information across sources? Or does that just cause more problems than it solves? This seems like it
>    might be part of the research on RAG.
> 5. Because our content is often multiple page, there is another dimension to the context of the
>    responses. That is knowing what page the student is on so the GenAI can know what they should have
>    read, and what will be comming up next. This way the GenAI can say that is a great question, we'll
>    be getting into it soon. Or remember when we covered that back on page X.

### 22:19 — digital-textbook reframe **(multiple-choice)**
- **How to handle the digital-textbook reframe across the docs?** → **Framing fix + new 3rd doc**
  (generalize Truth A to "content the student works with", sims = executable case; add a new non-sim
  doc).

---

## 2026-06-30 — fitting in the new material & wrap-up

### 18:43 — the new doc doesn't fit the existing division
> This new document doesn't fit very well with the existing document division and names. It seems to be
> a lit review itself. And is also about grounding. I think the content of the new document is good, I'm
> just trying to figure out how to fit it in better.

### 18:45 — reconciling the third doc **(multiple-choice)**
- **How to reconcile the third doc with the grounding lit review?** → **Two-part review, rename doc 3**
  (doc 2 = foundations + four truths; doc 3 = its operational pair).

### 19:50 — "Truth A / Truth B" naming feels off
> What do you think about this Truth A, Truth B, ... terminology? When I read it it always kinds of
> trips me up. They kind of seem like types of grounding. Their division seems useful, I'm just not sure
> about naming them Truths.

### 19:51 — replacing "the four Truths (A/B/C/D)" **(multiple-choice)**
- → free-text, rejecting the offered options: *"Can you come up with some more options. References
  isn't good because it conflicts with \"paper references\". Standards isn't good because it conflicts
  with education standards. Grounds just doesn't read right to me: \"the four grounds\"."*

### 19:56 — fresh umbrella term **(multiple-choice)**
- **Which umbrella term reads best for the four?** → **Anchors** (ties to the "grounding" theme;
  "anchors" reads naturally). *This is the origin of the "anchors" terminology now used in the docs.*

### 20:04 — commit
> Yes please commit and push the current changes.

### 20:07 — hand off to a new session
> I'm going to start a new session to continue working on this. If there is context the new session
> should know which is not captured by the docs in the `grounding-llm-help` folder, please add a new
> document with this context.
