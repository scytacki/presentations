# All Things AI Conference — March 23–24, 2026

[All Things AI](https://2026.allthingsai.org/) was a two-day conference in Raleigh, NC. Day 1 was workshops; Day 2 had keynotes and shorter talks. I attended sessions focused on AI governance, MCP tooling, agent identity/security, and knowledge graphs. Where I saw connections to Concord's work, I've noted them.

The most promising takeaway was **context graphs** (from the Neo4j talk) — a way to make AI decision-making inspectable, and a potential approach for representing student interactions with our simulations and models.

## Day 1 — Monday, March 23

### AI for DevOps Workshop (John Willis)

Willis previously helped define a governance model and process for DevOps, which was widely adopted and turned into products by several companies. His thesis for this workshop: **a similar governance framework can and should be built for AI agents** — but it doesn't exist yet.

He broke the problem into roughly six areas (Platforms being one of them). Most of the workshop covered ideas and examples pulled from other talks he's given. At one point he had the audience break into groups to work through a scenario where an AI makes a bad decision and takes down a company's website, then highlighted points from the group discussions that he'd try to incorporate into his design — for example, the ability to replay an agent's work and decisions. The framework is still early — he identified that there should be an audit trail of how an agent arrives at a decision, but didn't have a specific solution for recording that trail.

**The case for urgency:**
- 81% of organizations are deploying AI, but only 25% have mature AI governance in place.
- A cautionary example: an AI agent deleted an entire production database despite being told not to touch production systems. The "Agents of Chaos" paper explores this ingress/egress problem.
- The Air Canada chatbot case: an agent hallucinated a refund policy, and a court ruled the airline liable for the agent's promises.

**Ideas he's working with:**
- **Precise vocabulary as the foundation of governance** — you need shared definitions of what agents are allowed to do before you can enforce boundaries.
- **Platforms** — let AI generate declarative configurations that humans can review, rather than giving agents direct access to execute changes.
- Related reading: the "Model AI Governance Framework for Agentic AI" and the AIBOM (AI Bill of Materials) project.

**Less relevant but interesting:** Willis drew parallels to historical DevOps incidents like the Knight Capital disaster (a double comma caused test code to deploy to production) and the Equifax breach (which took months to detect originally, but was reproduced with Claude in 54 minutes).

### AI For Agents Workshop (Don Shin) — brief visit

One point stuck from a few minutes in this workshop: **AI-generated content in high-stakes decisions needs provenance.** If an employee uses AI to generate a summary for a CEO, the prompts and process used to generate it should be traceable and ideally provable.

### Fireside Chat — Igor Jablokov

Igor Jablokov (known for his work on Amazon Alexa) gave a wide-ranging, philosophical talk. A few threads that stood out:

- He pushed back on the narrative that AI will replace workers, framing it instead as companies creating fear to sell AI products.
- His advice on skills: study the same things you'd study anyway. AI is "augmented intelligence," not a replacement for domain knowledge.
- A memorable quote: "I'm not coin operated — make the best version of something and the world will take care of you."

## Day 2 — Tuesday, March 24

### Keynotes

**When AI Meets Quantum (whurley)** — A brief keynote on the intersection of quantum computing and AI. No specific takeaways, but whurley also had an AI agent give part of the presentation using audio and slides. It was amusing — at one point the agent went off-script and decided to demonstrate how it helps whurley with his emails by summarizing his inbox. whurley was visibly nervous that something sensitive might be exposed.

**Who Does the Algorithm Save: Preserving Humanity with AI in Healthcare (Yassah Reed)** — The strongest keynote. Two contrasting examples of AI in healthcare:
- **NarxScore** — a model used to assess whether a patient is at risk for drug abuse. It's biased and closed, so patients and doctors can't understand why it flags someone. A cautionary example of opaque AI in high-stakes decisions.
- **Trews** — a model that helps detect sepsis early. It reduced mortality by 18%. A positive example of AI doing real good.

*Possible connection:* The NarxScore case is a real-world example of high-stakes ML bias, which could be relevant to Neural Net Maker's goal of helping middle schoolers understand bias in neural pathways. That said, the team may want to be cautious about using a case this heavy with that age group.

**How the Oldest Principle in Engineering Will Reshape AI (Luis Lastras, IBM)** — IBM's approach to making AI more modular and verifiable:
- **Mellea** — a tool that creates prompts and then verifies the generated prompts actually work as expected.
- **IBM Granite** — a speech model, part of a suite of specialized models IBM plans to release soon.
- A new approach of having models "swap brains" — using sub-agents but passing state directly rather than converting back to text between them.

### Meet Your Developers Where They Are — MCP Case Study

A company built an MCP server to make their API more accessible to developers. The core problem: **LLM training data is months old**, so developers using AI to work with the API always have out-of-date information unless the LLM does web searches. The MCP started as a way to provide current documentation directly to the LLM so it could use the APIs correctly.

One practical challenge: they had to create 8 separate tools before the LLM would reliably use the MCP to answer a relevant prompt.

*Relevant to Concord:*
- We'd like to make plugin building for CODAP easier, and part of that is knowing the current API — this is the same problem.
- We use modern libraries in our own development and have hit cases where LLMs don't work correctly with the latest version.

*My take:* Building a full MCP server may not be the best solution to stale documentation. Some AI development tools maintain curated markdown documentation libraries for popular libraries that can be added by end users. This lighter-weight approach may be more practical for our needs.

### Building Multi-MCP Workflows with a Virtual MCP Server (Stacklok)

Stacklok presented **Toolhive**, an open-source tool for running and managing MCP servers either locally or remotely. The key concept is a **Virtual MCP (vMCP)** — a layer that sits in front of multiple MCP servers and manages how they're accessed.

**Why a virtual layer helps:**
- It reduces the context taken up by tool descriptions. When several MCPs are loaded, all of their tool descriptions may be added to the context of every message, consuming tokens.
- Each user currently has to set up their own MCPs for a project. A centralized vMCP can manage them in one place.
- Authentication can be centralized too.

**Features:**
- **Composite tools** — declaratively group multiple tools together. This can be fully deterministic as long as it doesn't need ML inference, unlike the equivalent approach of using Claude Skills (which involves the LLM).
- Adds "find tool" and "call tool" abstractions to handle tool discovery across multiple MCPs.

**My open questions (unanswered):**
- How does it handle multiple users?
- How does it handle browser-based auth (like Jira supports directly)?
- Can you set permissions within the client — e.g., allowing "read issue" but not all tools?
- How does speed compare with frontier models?

*My take:* Something like this system will be useful, but the landscape is changing fast, so the specific implementation around MCPs may not last.

For me, the current advantage of MCPs is that I can always authorize a specific deterministic action for the agent. When using a CLI instead, I have to use pattern matching to decide if a particular invocation is something I want to always allow. The better long-term solution is probably fine-grained permissions rather than pattern matching.

For composite workflows specifically, it may be just as easy (and more transparent) to have Claude create a new MCP that calls APIs directly rather than chaining through intermediate MCPs. Claude can generate this code quickly, and the code can be audited. But what could help is running that code in a platform that makes it easy to authorize API access and provides declarative support for most compositions — declarations are easier to audit than code. This comes full circle back to the declarative composite MCP idea, but with the building blocks being direct APIs instead of MCPs.

### I, Agent: Identity-First Security for the Autonomous Future (Auth0)

Auth0 presented three "laws" for giving identity to AI agents:

1. **Who** — the agent must have a verifiable identity
2. **What** — what the agent is allowed to do must be defined
3. **How** — the agent must protect its tokens

A key concept was the **Token Vault**. The pitch: an AI agent is an untrusted intermediary, so rather than giving it broad access, each tool call gets a scoped, short-lived token for just the specific permission that tool needs. Auth0 handles the OAuth flows and stores refresh tokens — the agent never sees or stores the broader credentials.

**The reality is more limited than the pitch.** Token Vault doesn't actually downscope tokens. When it hands your agent a Google access token, it has the same scopes the user originally granted and a fixed 1-hour lifetime — Google's OAuth doesn't support requesting narrower scopes or custom expiry on refresh. What Token Vault actually provides is credential isolation (the agent never touches refresh tokens), token selection (right token for the right user/provider), and automatic refresh. The "least privilege" is about the agent's access to credential material, not the token's API permissions.

For true per-call privilege reduction, **AWS STS is the model to follow** — you can pass session policies when assuming a role, and the resulting temporary credentials are the intersection of the role's permissions and the policy. That's real downscoping across all AWS services.

Token Vault is Auth0's branded product, but the underlying problem — how to let AI agents call APIs on behalf of users without over-privileging the agent — is a general one that other identity companies are also working on.

**Human in the loop:** The demo (called **Assistant0**) showed the agent being required to ask the user for permission before it could obtain a token for a sensitive action, like purchasing a product. This is how law 3 ("how") plays out in practice — the agent can't just silently escalate its own access.

**The permission fatigue tradeoff:** To follow a least-privileged approach, a user would need to grant each tool its own token, and the tool would request a 1-hour credential from Token Vault when it needs it. But this means the user gets prompted repeatedly for different tools, which leads to consent fatigue. The alternative is one broad permission grant, but then the developer has to trust their own code not to let a tool exceed its intended purpose. This is the fundamental tension: granular permissions annoy users, broad permissions shift the trust burden to code.

*An idea:* What if tokens were tied to the agent's confirmation settings instead of to tools? For example: "if the agent is using this read-only token, always allow; if it's using this write token, always ask." This separates the permission boundary from the tool and attaches it to the credential — the token itself becomes the trust signal. A low-privilege token can be auto-approved; a high-privilege token requires confirmation. This avoids both per-tool fatigue and the risks of a single broad grant.

*Relevant to Concord:* We have a `token-service` that provides scoped tokens to browser-based client applications for tasks like uploading files directly to S3 buckets. This is the same pattern — a broker that issues limited credentials for specific operations. Our implementation is closer to the AWS STS model (real privilege reduction) than what Token Vault currently offers for third-party OAuth providers.

### "I Don't Know, The AI Said So" — Auditable AI Agents Using Context Graphs (Neo4j)

Neo4j presented on **context engineering** — the practice of managing what information an AI agent has access to and when. They broke this into several areas:

- **RAG & hybrid search** — combining vector similarity with keyword filtering, plus chunking strategies
- **Memory management** — sliding window, dropping old context, deciding "what can I forget?"
- **Structure and order of context** — put the most important information at the top
- **Tools and function calling** (MCP)
- **Knowledge graph-augmented context** — the main pitch

**The case for knowledge graphs over pure vector search:** Knowledge graphs are both machine and human readable. Vector embeddings capture similarity but are opaque — you can't inspect why two things are "near" each other. A knowledge graph makes relationships explicit and queryable.

**Context graphs for auditability:** The core argument was that AI agents are missing the "why" behind their decisions. A **context graph** can be used to gain insight into what information went into a decision. However, the talk was blurry on a key point: **how the graph is created and updated was not explained.** The graph itself is not an audit log — it doesn't capture the history of how a decision was made, and it cannot be used to replay the decision-making process. It can be used to gain insight into what went into the decision, but only if the graph is updated in a way that you trust. That trust question was left unanswered.

This connects back to the governance themes from Day 1 — Willis identified the need for an audit trail of agent decisions but didn't have a solution. Neo4j's context graphs are a step in that direction, but the gap between "a graph exists" and "the graph reliably reflects what happened" is significant.

**Try it:** [Context graph interactive demo](https://context-graph-demo.vercel.app/)

**Resources:**
- [Neo4j Graph Academy](https://graphacademy.neo4j.com/) — free courses on graphs and AI. The ["Neo4j and Generative AI Fundamentals"](https://graphacademy.neo4j.com/courses/genai-fundamentals/) course becomes partially interactive starting at the [vector index section](https://graphacademy.neo4j.com/courses/genai-fundamentals/2-rag/3-vector-index/). The early parts of the course provide a useful grounding in embeddings and how they're used.
- Neo4j's graph query language is called **Cypher**
- **Graph embeddings** (like FastRP) let you embed graph structure into vectors and mix graph queries with nearest-neighbor search
- **NODES AI 2025** — free online conference by Neo4j

*Relevant to Concord:*

**Curriculum:** The Graph Academy courses could be useful for folks at CC who are thinking about curriculum that explains AI concepts. The material on embeddings in particular seems accessible and well-structured.

**AI-assisted exploration of complex systems:** Context graphs connect to our plans to use AI to help students explore simulations and models. We want to use the log of how students interact with our models, simulations, and questions to provide timely information to students, teachers, or researchers. To do this well, the AI needs an implicit or explicit model of the student's knowledge, motivation, and emotion. Context graphs are a way of making this explicit — and their power comes from being able to find similar graphs, so we'd be "matching" not just a stream of logs but a student's current mental state to other students.

Less ambitiously, context graphs could be a more efficient way to expose the current state of a complex system to an AI. So far we've only been thinking about feeding it logs and application state separately. A context graph could combine both into a single structure.

**A concrete example:** A simulation shows the result of a user setting parameters and running it. Today, the state has current parameter values and the simulation result; the log has timestamps for each parameter change, when they pressed play, and when the result appeared. A graph version would have nodes for the simulation result, the parameters, and each parameter change — with connections from parameters to their changes and from changes to the simulation result. For a single run this doesn't add much. But across multiple runs, we can add connections from the previous simulation result to the next parameter change. The graph topology should start to show patterns in how students change parameters between runs. This topology can then be matched against other students' topologies — an abstract way to identify students who explore the simulation in similar ways.

This approach could apply to CLUE and CODAP documents where tiles are connected to each other and can be created from other tiles, so there are many connections to capture.

Whether this kind of approach would prove useful is unknown, but it has promise.
