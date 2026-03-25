Deep thoughts:
- zero trust environments described before but haven't had use case
- kubernettries control plane for MCP

Keynote 1: quantum computing 

Keynote 3: health care NarxScore to decide if a patient is at risk for drug abuse. It is biased, and closed so people can't figure out why. Bad example.

Trews is a model to help find sepsis. Reduced mortality by 18%

Keynote 4: Luis IBM Decomposing AI 
- mellea creates prompts and verifies the created prompts actually work as expected.
- ibm granite speech model
- ibm has several specialized models they will release soon.
- new approach of having models "swap brains", like sub agents, but not going back to text.

Talk 1: MCP case study
Providing docs and api
Amazon icurol wraps skills
Had to make 8 tools inorder to actually be called when a user asks a question that it should answer 

Talk 2: Multi MCP with Stacklok: toolhive
Can run toolhive locally or remotely
vMCP
How does it handle multiple users?
How does it handle descripitions: adds find tool and call tool
How about speed vs frontier models?
Can it handle browser auth like Jira supports directly.
How about permissions within Claude like allowing read issue but not all call tool?

Composite tools declaratively group tools together.
It solves similar problems a task with sub agents running on small model.

Points in workflow where you need the llm to read and extract info from tool result 

All open source.

Talk 3: auth0 giving identity to agents
- law 1: who 
- law 2: what
- law 3: how, must protect its tokens

Token vault is that an auth0 thing?
How does human in the loop part of law 3 work?

Token vault is used on each call to get a token just for the the specific permission that the tool needs to do.

Assistant0 demo from Auth0 talk.   

Talk 4: Neo4j orchestrating multiple agents
https://graphacademy.neo4j.com/ 
- context engineering (rag, memory, tools and function calling, structure and order of context?, knowledge graph-augmented context)
- RAG & hybrid search (using keyword filtering), also chunking
- Memory Management - sliding window, dropping old context, "what can I forget?"
- Structure and Order Context: put most important stuff at the top
- Tools and Function Calling (MCP)
- Knowledge Graph Augmented Context: 
  - comparing knowledge graph to vector representation, where knowledge graph is both machine and human readable
- genai course covers both vector and and knowledge graph
- neo4j "cypher" graph query language   
- it seems like you can mix vector queries with neariest neighbor. 
- context graphs: agents are missing the "why", decision trace, like a traditional audit log
- https://context-graph-demo.vercel.app/
- graph embeddings are a thing: FastRP is one
- NODES AI 2025 (free online conference by Neo4j)