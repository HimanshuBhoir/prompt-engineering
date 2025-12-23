## **Context Compression Techniques**

### **Technique 1: Summarization**

**Instead of full documents:**
```
CONTEXT:
[10K token meeting transcript]
```

**Use summaries:**
```
CONTEXT:
Meeting Summary (2024-12-10):
- Decided to prioritize feature X
- Budget approved: $50K
- Launch target: Q1 2025
- Blockers: Need design approval

Key quotes:
"We need this by end of quarter" - CEO
"Design team has capacity" - Design lead
```

---

### **Technique 2: Semantic Chunking**

**Instead of dumping everything:**
```
CONTEXT:
[Entire 50-page documentation]
```

**Use relevant sections only:**
```
User asks: "How do I authenticate?"

CONTEXT (retrieved relevant sections):
Section 2.1: Authentication
- API key in header: Authorization: Bearer {key}
- Keys expire after 90 days
- Get keys from dashboard

Section 4.3: Troubleshooting Auth
- Error 401: Invalid/expired key
- Error 403: Insufficient permissions
```

---

### **Technique 3: Hierarchical Compression**

**Layer 1: High-level summary (always included)**
```
This document covers: Authentication, Rate limits, Error codes
```

**Layer 2: Section summaries (include if relevant)**
```
Authentication section: 3 methods supported (API key, OAuth, JWT)
```

**Layer 3: Full details (include only if directly asked)**
```
[Full authentication documentation]
```

---

## **The Context Budget Pattern**

Treat context like a limited resource:

```
Total budget: 100K tokens

Allocation:
- System prompt:       1K  (1%)
- User query:          0.5K (0.5%)
- Critical context:    10K (10%)
- Retrieved docs:      30K (30%)
- Conversation:        5K  (5%)
- Response space:      3K  (3%)
- Reserve/buffer:      50.5K (50.5%)
────────────────────────────────
Total:                 100K
```

**Always leave buffer for:**
- Response generation
- Follow-up questions
- Unexpected context needs

---