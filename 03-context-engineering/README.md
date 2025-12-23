# Phase 3: Context Engineering

**Core concept shift:** 

> Prompting is static (instructions that don't change)  
> Context engineering is **dynamic intelligence** (information that changes per request)

---

## **The Mental Model**

```
PROMPT = The program (fixed logic)
CONTEXT = The data (variable information)

Bad approach: Put everything in the prompt
Good approach: Prompt defines behavior, context provides information
```

**Analogy:**
- **Prompt** = Your brain's operating system
- **Context** = Your working memory (what you're thinking about right now)

---

## **Why This Matters**

### **Example: Customer Support AI**

**Bad (everything in prompt):**
```
SYSTEM PROMPT:
You are support AI.
User John's order #12345 shipped on Dec 15.
User Sarah's order #12346 shipped on Dec 16.
User Mike's order #12347 is pending.
[... 10,000 more orders ...]
```

**Problem:** Doesn't scale, wastes tokens, static data

---

**Good (context separation):**
```
SYSTEM PROMPT:
You are support AI.
When user asks about orders, reference the order data provided in context.

CONTEXT (dynamic, injected per request):
User: John
Order: #12345
Status: Shipped Dec 15
Tracking: 1Z999AA1...
```

**Benefits:** Scales, efficient tokens, dynamic data

---


