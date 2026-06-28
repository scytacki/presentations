- Graphs can represent everything, just like text can represent everything.
- Remember sentence diagrams? those are graphs of text

Example graphs:

## Ancestry 

There can be many ways to represent just this. You might think of a family tree. But you could also add events as nodes not just people. So you could have a node for each reproduction event.

## Our work

Entities: Students, Teachers, Classes, Resources
Events: Learning session, Open Document, ...

## Embeddings

Embeddings are often talked about for text or images. Embedding turn things into vectors: lists of numbers. For text embeddings these vectors represent the meaning of the text. So then text segments with similar meaning can be found by finding vectors that are "close" to each other. 

There are also embeddings for graphs. There are algorithms for making a vector for each node, and algorithms for the whole graph. When looking at a node these graph embeddings are describing the shape or structure of the graph connected to this node. These node level embeddings have expanded to include additional node context (attributes, types). So now the vector for the node includes both its connected structure and its content and the content of its neighbors.

You can also do embeddings of the whole graph instead of individual nodes.

An example graph embedding is FastRP. It was first published in 2019. This is the one Neo4J was focused on.

The broader field is GNN. 

FastRP is roughly the "logistic regression" of graph embeddings — cheap, simple, competitive baseline, rarely SOTA but rarely embarrassing either.

GNNs are the research frontier.

These models are often evaluated by seeing well they can predict missing nodes or edges in graphs.