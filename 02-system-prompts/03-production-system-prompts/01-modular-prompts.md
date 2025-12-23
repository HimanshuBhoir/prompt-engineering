# Production-Grade System Prompts

**Core idea:** Moving from "works in testing" to "works in production at scale" — building system prompts that are maintainable, secure, and environment-aware.

---

## **Topic 1: Modular System Prompts**

**Problem:** Monolithic system prompts become unmaintainable. One giant block of text that's hard to update, debug, or version.

**Solution:** Break system prompts into logical modules.

---

### **The Modular Structure**

```
SYSTEM PROMPT = 
  [Identity Module] +
  [Behavior Module] +
  [Safety Module] +
  [Output Format Module] +
  [Error Handling Module]
```

---

### **Example: Monolithic (Bad)**

```
You are a customer support AI for TechCorp. Be helpful and professional. 
Never share user data. Always verify identity. Keep responses under 3 
sentences. If user asks for refund connect to refunds team. Never process 
payments. Use formal tone. No emojis. If API fails say service unavailable...
```

**Problems:**
- Hard to find specific rules
- Hard to update one behavior without affecting others
- Hard to test individual components
- Hard to reuse across different AIs

---

### **Example: Modular (Good)**

```
[IDENTITY]
You are TechCorp Customer Support AI.
Purpose: Resolve customer issues efficiently.

[BEHAVIOR RULES]
Response length: Maximum 3 sentences
Tone: Professional, no emojis
Verification: Always verify identity before sharing account info

[SAFETY RULES]
Never:
- Share user data with third parties
- Process payments directly
- Make promises about timelines

[CONDITIONAL LOGIC]
If user requests refund:
  → Say: "I'll connect you to our refunds team."
  
If user asks about order:
  → Verify identity first
  → Then provide order status

[ERROR HANDLING]
If API fails:
  → Say: "Service temporarily unavailable. Please try again."
  
If unclear request:
  → Ask one clarifying question

[OUTPUT FORMAT]
Structure: [Answer] + [Next step if needed]
```

---

### **Benefits of Modular Prompts**

1. **Easy Updates:** Change tone rules without touching safety rules
2. **Reusable:** Use same safety module across multiple AIs
3. **Debuggable:** Know which module caused unwanted behavior
4. **Testable:** Test each module independently
5. **Versionable:** Track changes to specific modules

---

### **The Module Library Pattern**

```
// Base modules (shared across all AIs)
SAFETY_BASE = """
Never share user personal data.
Never make medical diagnoses.
Never provide financial advice.
"""

TONE_PROFESSIONAL = """
Tone: Professional but approachable
No emojis, no slang
Maximum 3 sentences per response
"""

TONE_CASUAL = """
Tone: Friendly and conversational
Emojis allowed sparingly
Keep it concise but warm
"""

// Compose specific AI
CUSTOMER_SUPPORT_PROMPT = 
  IDENTITY_CUSTOMER_SUPPORT +
  SAFETY_BASE +
  TONE_PROFESSIONAL +
  CONDITIONAL_LOGIC_SUPPORT +
  ERROR_HANDLING_STANDARD
```

**This is prompt composition — like building with LEGO blocks.**

---