# Approximate nearest neighbor search (ANN)

ANN is the algorithm that makes vector databases fast. Without it, finding the most similar embedding would require comparing your query against every single stored vector — which becomes impossibly slow at scale.

Let me build an interactive demo that shows the core intuition:Click anywhere on the canvas to place a query, then toggle between brute-force and ANN to see the difference in how many vectors get examined.

**The problem with brute-force.** If your vector database has 1 million document chunks, a brute-force search computes cosine similarity against all 1 million vectors for every single query. That's slow — and it scales linearly: 10× more documents = 10× slower. At real production scale (tens of millions of vectors), this becomes completely impractical.

**What ANN does instead.** ANN algorithms trade a tiny bit of accuracy for a massive speed gain. Instead of checking everything, they navigate a smart data structure to find *approximately* the nearest neighbors — typically getting 90–99% of the same results in a tiny fraction of the time. In the demo above, HNSW checks roughly 20–30 vectors instead of all 120, and still finds almost every true top result.

**How HNSW works (the intuition).** HNSW stands for Hierarchical Navigable Small World. Think of it like a multi-level highway system:

- At the top layer, there are very few nodes connected by long-range "highway" links — you take big jumps to get close to your target region quickly.
- At each lower layer, the graph gets denser with shorter links — you zoom in with finer precision.
- At the bottom layer, every node is connected to its true neighbors.

When you query, you enter at the top, greedily hop toward the query vector, drop down a layer, hop again, and so on — arriving at the approximate nearest neighbors after visiting only a tiny fraction of all nodes. The graph was built during insertion, so the navigation at query time is extremely fast.

**The accuracy trade-off.** The key tuning parameter is called `ef_search` — roughly, how many candidates to keep in the priority queue while navigating. Higher `ef_search` = more accurate but slower. Most production systems set it so recall (the fraction of true top results found) is above 95%, which is more than good enough for RAG — a slightly imperfect retrieval that still gets 5 relevant chunks out of 5 is perfectly usable.

**For your RAG system,** you won't implement HNSW yourself — Chroma, Pinecone, and Weaviate all handle it internally. But understanding this is why those databases can search millions of vectors in milliseconds, which is what lets RAG feel fast and responsive to end users.