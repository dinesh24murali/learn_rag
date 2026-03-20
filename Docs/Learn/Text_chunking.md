# Text chunking

Chunking is one of the most impactful decisions you'll make when building a RAG system — it directly determines what context the LLM receives.Try each strategy and drag the size slider — watch how the chunk count, token estimates, and the highlighted text all change together.

**Why you can't just embed the whole document.** A single 50-page PDF would produce one enormous embedding vector. When a user asks a specific question, that vector represents the average meaning of the entire document — it won't match closely to any specific query. And even if it did match, stuffing the whole document into the LLM's context window would be expensive, slow, and likely exceed the token limit. Chunking solves both problems: each chunk is small enough to embed precisely, and only the relevant chunks get sent to the LLM.

**The four strategies in the demo:**

*Fixed size* — split every N characters, ignoring sentence or word boundaries. Fast and simple, but can cut a sentence in half mid-thought, creating confusing chunks. Good for quick prototypes.

*Sentence* — split on punctuation (`.`, `?`, `!`). Each chunk is a complete thought, which usually embeds well. The downside is uneven chunk lengths — one sentence might be 20 words, another 5.

*Paragraph* — split on blank lines. Great for documents where paragraphs are coherent units (articles, wiki pages). Chunks can get very large if paragraphs are long.

*Fixed + overlap* — the most commonly used approach in production. Chunks have a fixed size, but each one shares N characters with the next. The amber-highlighted zones in the demo are the overlapping regions. This prevents information from falling through the cracks at chunk boundaries — if a key sentence straddles two chunks, overlap ensures at least one chunk captures it fully.

**The core tension.** Smaller chunks = more precise embedding (the vector is focused on one topic), but less context for the LLM to work with. Larger chunks = richer context for the LLM, but the embedding vector is murkier and matches fewer queries well. A practical starting point for most RAG systems is chunks of around 256–512 tokens with a 10–15% overlap. For your first build, try 300 characters with 50 characters of overlap — then tune based on whether your answers feel too vague (go smaller) or too disconnected (go larger).
