# Tokenization
Tokenization is the process of breaking text into smaller pieces — called **tokens** — that a model can process numerically.Try typing any sentence below and watch it get tokenized live:Here's what's happening in those two steps:

**Step 1 — split into tokens.** The text is broken into chunks — usually words and punctuation marks. "don't" might become `don` + `'` + `t`, or it might stay as one piece depending on the tokenizer. This demo splits on words and punctuation naively.

**Step 2 — assign IDs.** Every unique token gets mapped to an integer from the model's vocabulary. This is the actual input the model sees — not text, but a list of numbers like `[1042, 3511, 2209, ...]`. The model learned what each number "means" during training.

A few things worth knowing for RAG specifically:

- **Why it matters for context windows.** LLMs have a limit measured in tokens, not characters or words. A common rule of thumb is ~1 token ≈ 4 characters in English. A 128,000-token context window fits roughly 100,000 words.
- **Sub-word tokenization.** Real models (like GPT or Claude) use algorithms called BPE (Byte Pair Encoding) or SentencePiece. These split rare words into sub-pieces — so "tokenization" might become `token` + `ization`. This lets the vocabulary stay small (~50k entries) while handling any word.
- **Tokens cost money.** When you call an LLM API, you're billed per token. In RAG, the chunks you retrieve and inject into the prompt all count — so chunk size directly affects cost.