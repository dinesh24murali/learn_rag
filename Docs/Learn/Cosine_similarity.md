# Cosine similarity

Cosine similarity is the most common way to measure how "close" two embeddings are — and therefore how relevant a document is to a query.The intuition is simple: instead of measuring how far apart two vectors are, cosine similarity measures the *angle* between them. Same direction = same meaning.Drag the purple **Q** dot around the circle and watch the similarity scores update. Select different documents to highlight the angle between them.

**The core idea.** In the previous lessons we learned that embeddings are vectors — lists of numbers. When you embed text, similar meanings point in similar *directions* in that high-dimensional space. Cosine similarity is just the cosine of the angle θ between two vectors:

```
cos(θ) = (A · B) / (|A| × |B|)
```

The result always lands between -1 and +1. Angle of 0° → score of 1.0 (identical direction, identical meaning). Angle of 90° → score of 0.0 (unrelated). Angle of 180° → score of -1.0 (opposite meaning).

**Why angle instead of distance?** This is the key insight. Two documents about "car fuel efficiency" — one a short tweet and one a long article — will have embeddings of very different *lengths* (magnitudes), because longer text tends to produce larger vectors. But they point in roughly the *same direction*. Cosine similarity ignores magnitude entirely, so both documents score equally relevant to a query about cars. Euclidean distance (straight-line distance) would be fooled by the length difference; cosine similarity is not.

**How it's used in RAG.** When a user sends a query:

1. The query is embedded → a query vector
2. Every stored document chunk has a pre-computed embedding in your vector database
3. The vector DB computes cosine similarity between the query vector and all document vectors
4. The top-k most similar chunks (highest scores) are returned
5. Those chunks are injected into the LLM's prompt as context

This whole retrieval step typically takes milliseconds, even over millions of documents, because vector databases use approximate nearest-neighbour algorithms (like HNSW) to avoid comparing against every single vector.