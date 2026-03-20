# Semantic search

Great question — this is one of the most important concepts to understand before building a RAG system.The core difference is this: keyword search asks "do these words match?", while semantic search asks "do these *meanings* match?" Here's an interactive demo to make it concrete:Try "I feel anxious" — keyword search finds nothing (the word "anxious" doesn't appear in any document), while semantic search correctly surfaces results about anxiety, stress, and breathing exercises. That gap is the whole story.

**How keyword search works.** It scans documents for the exact words in your query. Classic examples are `LIKE` queries in SQL, or how `Ctrl+F` works. It's fast, deterministic, and great when you know the exact terminology — but it completely misses synonyms, paraphrasing, or conceptual similarity.

**How semantic search works.** Your query and every document are converted into embeddings (the high-dimensional vectors we covered earlier). Similar *meanings* end up geometrically close together in that vector space — so "automobile" and "car" land near each other even though they share no letters. Retrieval becomes a nearest-neighbour search: find the vectors closest to the query vector.

**Why this matters for RAG specifically.** In a RAG system, your users will ask questions in their own words. Your documents were written by someone else, possibly with different vocabulary. Keyword search would silently return nothing — or worse, return irrelevant documents that happen to share a word. Semantic search bridges that vocabulary gap, which is why it's the standard approach for the retrieval step in RAG.

**The practical trade-off** is that keyword search is dead simple to implement (just string matching) and extremely fast, while semantic search requires an embedding model and a vector database. Many production RAG systems actually use both together — this is called *hybrid search* — to get the best of both worlds.