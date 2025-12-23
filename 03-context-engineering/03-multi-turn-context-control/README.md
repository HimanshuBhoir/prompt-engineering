# Multi-Turn Context Control

**Core idea:** In long conversations, context and instructions degrade over time. You must actively manage this drift to maintain consistent behavior across 10, 20, 50+ turns.

**This is where most production AIs fail.**

---

## **The Multi-Turn Problem**

### **What Happens Over Time:**

```
Turn 1:  AI follows all rules perfectly ✓
Turn 5:  Minor tone drift
Turn 10: Instructions weakening
Turn 15: Context getting cluttered
Turn 20: Original behavior significantly degraded
Turn 30: Barely following system prompt
```

**Why this happens:**
1. **Context fills up** with conversation history
2. **Original instructions** get pushed further back
3. **User patterns** influence AI behavior
4. **Attention shifts** to recent exchanges
5. **Reinforcement learning** from conversation flow

---