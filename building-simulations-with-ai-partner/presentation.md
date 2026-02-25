---
marp: true
theme: default
paginate: true
style: |
  .emoji { font-size: 100px; text-align: center; }
---

# AI Is Changing How We Work Together

Here's what that looks like

<div class="emoji">🤖🤝🧑‍🔬</div>

<!--
This talk is about what happened when I built an ocean simulation with AI over two weeks — not a how-to, just what I experienced and what it might mean for how we work as a group.
-->

---

## You've all been using AI

Many of you have been building things with it too

Think about how you did it — because a lot has changed, even in the last few months

<div class="emoji">🏗️</div>

<!--
Acknowledge their experience. Don't explain what AI can do — they know. The point is to prime them to compare their own experience with what they're about to see, because the tools and what's possible have shifted quickly.
-->

---

## Two weeks ago, I started building a simulation

An ocean current simulation accounting for:
- Predominant winds
- Coriolis effect
- Pressure / sea surface height
- Land boundaries

<div class="emoji">🌊🌍</div>

<a href="https://models-resources.concord.org/ocean-explorer/branch/phase-4/index.html" target="_blank">Ocean Simulation</a>

<!--
Show the simulation briefly so people have context. This is a real project, not a demo. I want to walk through not what I built, but how the process of building it was different from what I expected.
-->

---

## What I expected: write code with AI help

## What actually happened: a document-driven process

<div class="emoji">💻 ➡️ 📝</div>

<!--
I thought AI would mainly help me write code faster. Instead, the biggest change was that it became worthwhile to create a whole set of planning documents — something I wouldn't have done working alone.
-->

---

## The documents I created — with AI

- **Science doc** — the physics behind ocean currents
- **Simulation guide** — general principles for this kind of simulation
- **Simulation notes** — specifics for this simulation
- **Roadmap** — phases for building it incrementally
- **Phase design docs** — detailed designs before building each phase
- **User guide** — evolved as each phase was completed

<div class="emoji">📚</div>

<!--
This is a lot of documents. Without AI, creating all of these would have taken days and I probably wouldn't have done it. The key insight: with AI, these documents were cheap to create AND directly useful — they guided the AI's code generation.
-->

---

## These documents served double duty

**For AI:** guided code generation — each phase was built from its design doc

**For people:** gave PIs and PMs real visibility into the work

<div class="emoji">🪙🪙</div>

<!--
I shared the science doc, roadmap, and user guide with PIs and PMs. Sometimes the phase design docs too. They read the docs and played with intermediate builds of the simulation. The documents weren't extra overhead — they were the actual medium of the work.
-->

---

## Along the way: Coriolis visualizations

- Started as a tool to help me understand the physics
- Became useful for explaining to other developers, PIs, and PMs

<div class="emoji">🌀</div>

<a href="https://models-resources.concord.org/ocean-explorer/branch/coriolis-reference-frame/doc/images/earth-rotation-frames.html" target="_blank">Coriolis Visualization</a>

<!--
Show the Coriolis visualizations here. These are another example of something that was cheap enough to build with AI that it was worth doing — and then turned out to be useful for collaboration too.
-->

---

## What this means beyond simulations

Not all work looks like this — adding a feature to an existing platform doesn't need a science doc

But the pattern adapts:
- Design documents that capture edge cases and decisions
- Documents that guide AI *and* inform collaborators

<div class="emoji">🧩</div>

<!--
The specific artifacts change depending on the work. A simulation needs a science doc. A platform feature might need a design doc covering edge cases. The underlying pattern is the same: AI makes it worthwhile to create richer planning documents because they serve both purposes.
-->

---

## What this opens up for the group

The same AI that made this document-driven process practical can also:
- Help **PIs/PMs** create their own prototypes before writing proposals or starting development
- Help **researchers** build custom tools for data analysis

And this way of working means:
- **Everyone** can engage with evolving documents and working prototypes — not just the finished product

<div class="emoji">🚪🔓</div>

<!--
The first two points are about AI capabilities beyond what I showed — but they're connected. If AI can help a developer create a science doc and simulation guide, it can help a PI create a prototype or a researcher create an analysis tool. The third point ties directly back to the talk: document-driven work creates more collaboration touchpoints. The next presentation will show a concrete example of cross-role collaboration.
-->

---

## Question for discussion

What kinds of planning documents would be most useful for your role?

<div class="emoji">💬❓</div>

<!--
A genuine open question. I don't have the answer — I've been doing this for two weeks. But I think it's worth discussing as a group.
-->
