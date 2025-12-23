## **Reinforcement Prompts**

### **Pattern 1: Periodic Reinforcement**

Inject reminders at regular intervals to re-assert system instructions.

```
SYSTEM PROMPT:

[BASE INSTRUCTIONS]
You are a professional support AI.
Tone: Formal
Max response: 3 sentences
Never share passwords

[REINFORCEMENT SCHEDULE]
Every 5 turns, internally remind yourself:
"I am a professional support AI. Tone: Formal. Max 3 sentences. Never share passwords."

Every 10 turns, verify:
- Last 3 responses were under 3 sentences?
- Tone remained formal?
- No security violations?
```

---

### **Pattern 2: Invisible Reminders**

Insert system instructions into context periodically without user seeing them.

```
Conversation:

Turn 1: [System prompt active]
Turn 2: User message → AI response
Turn 3: User message → AI response
Turn 4: User message → AI response
Turn 5: User message → AI response
Turn 6: [INVISIBLE REINFORCEMENT]
        System reminder: "Maintain professional tone. Max 3 sentences."
        User message → AI response
Turn 7: User message → AI response
...
```

**Implementation:**
```javascript
if (turnCount % 5 === 0) {
  contextMessages.push({
    role: "system",
    content: "[REMINDER] Maintain all original rules: Professional tone, 3 sentence max, security protocols.",
    invisible: true // Not shown to user
  });
}
```

---

### **Pattern 3: Context Compression + Reinforcement**

When context gets full, compress old messages but keep system instructions fresh.

```
Turn 1-10: Full conversation history
Turn 11: Compress turns 1-5 into summary
         + Re-inject system prompt at top
Turn 12-20: Recent history (6-20)
Turn 21: Compress turns 6-15 into summary
         + Re-inject system prompt at top
```

**Result:** System instructions always "recent" in context

---

### **Pattern 4: Rule Echo in Responses**

Have AI explicitly acknowledge rules periodically.

```
SYSTEM PROMPT:

Every 10 turns, include in your response:
"[As a reminder: I maintain professional tone and never share passwords]"

This keeps rules active in both your and the user's mind.

Example:
Turn 10 response: "I can help with that password reset. Here's the process. [As a reminder: I maintain professional tone and never share passwords]"
```

---