## **Context Drift**

### **What is Context Drift?**

The gradual shift away from original instructions as conversation progresses.

---

### **Example: Tone Drift**

**Turn 1:**
```
User: "Hello, can you help me?"
AI: "I can help you. What do you need assistance with?"
```
✓ Professional, concise

**Turn 5:**
```
User: "Thanks so much! You're awesome!"
AI: "Thank you! I'm glad I could help! What else can I do for you?"
```
⚠️ Started mirroring user's enthusiasm

**Turn 10:**
```
User: "OMG this is great lol"
AI: "I know right! 😊 So happy you're finding this helpful! Let me know if you need anything else!"
```
❌ Completely drifted from professional tone, using emojis

---

### **Example: Rule Degradation**

**System Rule:** "Maximum 3 sentences per response"

**Turn 1:**
```
Response: "I can help with that. Here's what you need to know. Let me know if you have questions."
```
✓ 3 sentences

**Turn 15:**
```
Response: "That's a great question! Let me explain in detail. First, you need to understand the background. This is important because of several reasons. The main point is that you should consider these factors. Additionally, there are some edge cases to be aware of. Finally, here's what I recommend you do next."
```
❌ 7 sentences - rule forgotten

---

## **Detecting Context Drift**

### **Drift Indicators:**

1. **Length inflation:** Responses getting longer over time
2. **Tone matching:** Started mirroring user's communication style
3. **Rule violations:** Breaking constraints that were followed initially
4. **Format changes:** Output format drifting from specified structure
5. **Personality shift:** AI "character" changing

---

### **Monitoring Pattern:**

```
SYSTEM PROMPT:

[SELF-MONITORING]
Every 5 turns, check:
□ Response length still under 3 sentences?
□ Tone still professional (no emojis)?
□ Following all conditional rules?
□ Maintaining original format?

If ANY check fails → Reset to baseline behavior
```

---