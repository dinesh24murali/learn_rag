# Todo

## Prerequisites to learn

- [x] What are embeddings?
- [x] What is Tokenization?
- [x] What is semantic search?
- [x] What is Cosine similarity?
- [x] Vector DB
- [x] Approximate nearest neighbor search (ANN)
- [x] Text chunking
- [ ] 

## Tools to understand:

- [ ] Tools to parse documents
- [ ] Packages to create embeddings for the Vector DB?
- [ ] Chroma DB
- [ ] LangChain

## Prompt

```
I have a good grasp of the basics now. I want to do a POC. I want to pick a use case to work on. Here is what I am planning. You can correct me if I am missing anything:
* getting bunch of documents that I can use for my POC
* cleaning and chunking the text in the document. Creating the embeddings in the Vector DB
* checking whether the embeddings are generated fine (As of now, I am not sure how to do this)
* Choosing an LLM. I will probably go with Ollama for testing
* Creating the web server to handle the requests from the frontend.
* Creating the frontend to render the chat window
How I believe the API will work
* The APIs/socket server that handles the chat should first take the user's input, then create a vector with the input text.
* Query the Vector DB with the relevant content
* Supply the retrieved contents from the Vector DB to the LLM so that it will generate the output text
* return back the output text to the user

Am I missing anything?
```

## AI

Your plan is solid — you clearly understand the core RAG loop. Let me give you a complete picture with a few important additions.Here is your plan annotated with what's missing and what needs a small correction:Click any box for a deep-dive on that specific piece. Here's a summary of what you had right, what was missing, and one correction:

**What you had right — all the core pieces are there.** Documents → chunk → embed → vector DB → embed query → retrieve → LLM → return answer. That's the correct skeleton.

**What was missing:**

The biggest gap is the *prompt builder* step. Retrieval gives you chunks, but you can't just dump them raw into the LLM. You need to assemble a structured prompt string like:

```
System: You are a helpful assistant. Answer only using the context below.

Context:
[chunk 1 text]
[chunk 2 text]
[chunk 3 text]

Conversation so far:
User: [previous message]
Assistant: [previous answer]

User: [current question]
```

This assembly step is where most beginners get confused — the prompt is the glue between retrieval and generation.

The second missing piece is **conversation history**. A chat system needs to pass the last N messages alongside the retrieved chunks, otherwise the LLM has no memory of what was just said. For a POC, keeping the last 4–6 messages in memory server-side is enough.

The third is **streaming**. Ollama supports streaming responses token by token. Without it, your frontend will sit silent for 10–30 seconds then dump the whole answer at once, which feels broken. Use Server-Sent Events (SSE) from FastAPI — it's about 10 lines of code and makes the UX feel immediate.

**One correction — Ollama for embeddings too.** You mentioned Ollama for the LLM, which is great. But you'll also need an embedding model. Ollama can serve both — `nomic-embed-text` or `mxbai-embed-large` run locally via Ollama and are solid choices. The critical rule: the same embedding model must be used at ingestion time and at query time, otherwise the vectors live in different spaces and similarity search breaks.

**On verifying embeddings** (your open question): the simplest approach is to call `collection.query(query_texts=["your test phrase"], n_results=3)` in Chroma and read back the chunk text. If the returned chunks look relevant to your phrase, the embeddings are working. For a more visual check, you can export your vectors and plot them with UMAP — similar chunks cluster together visually.

For your use case selection, something like a "chat with a product manual" or "Q&A over a Wikipedia dump on a topic you know well" works great as a POC — you can immediately judge whether the answers are correct.

