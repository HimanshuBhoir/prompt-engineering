## **When Too Much Context Kills Accuracy**

### **The Problem: Information Overload**

Recent research (2024) shows:
- Models struggle with "needle in haystack" retrieval
- Accuracy drops with irrelevant context
- Middle of context gets "lost"
- Too much context → more hallucination risk

**Testing from Anthropic (2024):**
```
Context size:     Accuracy on specific fact:
10K tokens        95%
50K tokens        89%
100K tokens       82%
150K tokens       78%
```

**The more context, the harder it is to find specific information.**

---

### **The Distraction Effect**

**Bad (context dump):**
```
CONTEXT:
[User's entire email history: 50K tokens]
[All company policies: 30K tokens]
[Full product catalog: 40K tokens]

User question: "What's my order status?"
```

**Problem:** AI must search through 120K tokens to find order info

**Result:** Slower, less accurate, more expensive

---

**Good (filtered context):**
```
CONTEXT:
User: Sarah Chen
Active order: #45782
Status: Shipped Dec 15
Tracking: 1Z999AA10123456784

User question: "What's my order status?"
```

**Benefits:** Fast, accurate, cheap

---