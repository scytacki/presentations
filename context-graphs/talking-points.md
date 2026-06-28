# Context Graphs — Discussion Talking Points

Audience: ed-tech research group (PIs, PMs, researchers, developers). Goal: share what I learned at All Things AI about context graphs, then open a discussion about whether they fit any of our projects.

Time target: short presentation (~10–15 min) followed by open discussion.

## Framing / Hook

Two problems we already care about that context graphs might help with:

1. **Understanding students in depth.** When a student uses a simulation or model, we capture logs and application state. Pulling meaning out of those streams — "what is this student actually doing?" — is hard.
2. **Trusting what an AI tells us.** If an AI summarizes a student's progress, or flags a classroom pattern for a researcher, how do we know *why* it said that? This is the same "black box" problem NarxScore shows in healthcare.

Context graphs are one possible answer to both.

## What a Context Graph Is (plain language)

- A graph = nodes (things) connected by edges (relationships).
- A **context graph** is the structured information an AI agent uses to make a decision, represented as a graph instead of as a blob of text or a vector embedding.
- Key property: **machine-readable *and* human-readable.** You can look at it, query it, and audit it. A vector embedding you cannot — similarity is opaque.
- Neo4j's pitch: when an AI makes a decision, you can point at the graph and say "this is what it knew."

## The Honest Caveat

The Neo4j talk had a gap I want to name up front: **they didn't explain how the graph gets built and updated.** The graph itself is a snapshot, not an audit log. It tells you *what* was known, not *how the decision was reached*. If the graph isn't trustworthy, neither is the explanation. This matters because "auditable AI" is the whole pitch.

So: promising direction, not a finished solution.

## Where This Could Fit Our Work

### 1. Richer input to AI about a single student

Today we think about giving an AI two things: application state (current parameters, current view) and logs (timeline of actions). These are separate streams that the AI has to reconcile.

A context graph combines them: nodes for the student's actions, the artifacts they produced, and the state of the system — connected by relationships. One structure instead of two parallel streams.

**Concrete example — a simulation:**
- Nodes: simulation result, each parameter, each parameter change, each run.
- Edges: parameters → their changes; changes → the run that followed; run → next set of changes.
- Across several runs, the shape of the graph starts to show *how* the student is exploring: are they sweeping one variable, changing everything at once, backtracking?

For one run, a graph adds little. Across many runs, the topology becomes the story.

### 2. Finding students who explore similarly

Graphs can be embedded (FastRP and similar techniques), which means you can do similarity search over *graph shapes* — not just over logs or text.

This is the part that could matter most for research: "find me other students whose exploration pattern looked like this one." That's a different question than "find students who got the same answer" or "find students who clicked the same buttons." It's closer to asking about the *structure of their thinking*.

### 3. CLUE and CODAP documents

These already are graphs — tiles connected to other tiles, created from other tiles. A lot of the structure we'd need is implicit in the document model. Worth thinking about whether we've been flattening that structure when we give it to an AI.

### 4. Curriculum materials

Neo4j's free [Graph Academy](https://graphacademy.neo4j.com/) has solid intro material on embeddings and RAG. Probably useful for anyone on a project that's teaching AI concepts.

## Graph Embeddings

The other half of the "match graph topology across students" idea is being able to compare graphs numerically. That's what graph embeddings give you.

**FastRP (Fast Random Projection)** was the specific technique mentioned at Neo4j's talk. It produces a **fixed-size vector per node** — you set the dimension as a hyperparameter (commonly 128 or 256), and every node in the graph gets a vector of that size. It works by random projection of the graph's adjacency structure, which makes it much cheaper to compute than neural approaches like node2vec or GraphSAGE.

**What a node embedding gives you:** one point in vector space per node. You can then do nearest-neighbor search — "which other nodes are similar to this one?" — and mix that with regular graph queries. For student exploration patterns, this lets us ask "which students' `Run 7` node is closest to this student's `Run 7` node?"

**Node vs. graph embeddings:** FastRP and friends embed *nodes*. There are also graph-level embeddings that produce a single vector for a whole graph or subgraph — useful if we wanted to compare one student's *entire* exploration to another's as a single object rather than node-by-node.

**On "orientation":** a node embedding is just a point — it has no direction of its own. The related idea comes from **knowledge graph embeddings** (TransE, RotatE), where *relations* are embedded as transformations — translations or rotations — that move from one node's vector to another. The directionality lives on the relation, not on the node. That's probably more apparatus than we need for clustering exploration patterns, but it's relevant if we ever wanted to represent typed relationships between student actions and reason about them algebraically.

## Graphs and Frontier LLMs

A practical question: do the frontier LLMs (GPT, Claude, Gemini, Llama) have graph neural networks built in? **No.** As of 2026, frontier model differentiation is happening in other areas — Mixture-of-Experts, attention-efficiency tricks, multimodality, reasoning-depth controls. No native GNN component.

The industry pattern is **integration, not absorption**: the GNN (or graph database, or context graph) runs as a separate component, and its output gets fed to the LLM through retrieval or tool calls — e.g. GraphRAG with graph queries in the retrieval pipeline. Useful to know because it means the graph work we'd do is *outside* the model and portable across whichever LLM we use, not tied to a specific vendor's architecture.

## Questions I'd Like the Group's Take On

- For the researchers: if we could cluster students by the *shape* of their interaction graph, would that change what you'd ask of the data? What would you want a cluster to mean?
- For PIs: is "auditability of AI decisions" something you're already being asked about in proposals, or is that still over the horizon for NSF work?
- For PMs: on projects where we already have rich log data — are there active research questions where "similar students" would unblock analysis?
- For everyone: where would we try this first — small enough to learn from, concrete enough to evaluate?

## What I'm *Not* Proposing

- Not proposing we adopt Neo4j or any specific tool.
- Not proposing we rebuild our log infrastructure.
- This is: "here is a lens I think is worth holding up to our work, let's see if anything lights up."

## Resources

- Open document with my full conference notes: `all-things-ai-2026-03/summary.md`
- [Context graph interactive demo](https://context-graph-demo.vercel.app/)
- [Neo4j Graph Academy](https://graphacademy.neo4j.com/) — free, starts accessible
