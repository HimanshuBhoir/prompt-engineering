## **First Topic: Context Is a Resource**

Let's start with understanding **what belongs in context vs prompt**.

Ready? 🚀

---

# Context Is a Resource

**Core idea:** Context (the AI's "working memory") is **limited and expensive** — you must manage it like RAM, database queries, or API rate limits.

---

## **The Context Window Reality** (Updated 2024-2025)

Current models (as of late 2024/early 2025):

```
Claude 3.5 Sonnet:    200K tokens (~150K words)
GPT-4 Turbo:          128K tokens (~96K words)
Gemini 1.5 Pro:       1M tokens (~750K words)
```

**Sounds like a lot, right?**

But in production:
```
System prompt:         ~1K tokens
Conversation history:  ~10K tokens (10 turns)
User query:            ~500 tokens
Retrieved documents:   ~50K tokens
Tools/functions:       ~2K tokens
Response generation:   ~2K tokens
────────────────────────────────
Total used:            ~65K tokens
Remaining capacity:    ~135K tokens
```

**Context fills up fast.**

---