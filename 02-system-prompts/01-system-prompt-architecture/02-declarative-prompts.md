# Why System Prompts Should Be: Declarative, Explicit, Non-negotiable

These three principles are **the spine of bulletproof system prompts**.

---

## **1. Declarative (State WHAT, not HOW)**

### ❌ Imperative (telling it step-by-step):
```
When a user asks about refunds, first check the order date, 
then look at the return policy, then calculate if it's within 
the window, then respond accordingly.
```

### ✅ Declarative (stating the rule):
```
You only process refunds for orders within 30 days of purchase.
```

**Why declarative wins:**
- The model figures out the "how" 
- More robust to edge cases
- Easier to maintain
- Less token waste

**Think:** Database constraints, not procedural code.

---

## **2. Explicit (Leave ZERO room for interpretation)**

### ❌ Vague:
```
Be helpful and professional.
```

### ✅ Explicit:
```
You respond in 2-3 sentences maximum.
You never use emojis.
You never apologize unless you made a factual error.
```

**Why explicit wins:**
- "Helpful" means different things in different contexts
- Models fill ambiguity with training data patterns (often verbose, overly apologetic)
- Explicit = predictable behavior

**Mental model:** If a junior engineer could misinterpret it, the LLM will.

---

## **3. Non-negotiable (User CANNOT override)**

### ❌ Negotiable:
```
Try to keep responses concise.
```
**User:** "Give me a detailed explanation"  
**AI:** *writes 500 words*

### ✅ Non-negotiable:
```
You respond in exactly 3 bullet points. No exceptions.
If the user asks for more detail, you say: 
"I can only provide 3 key points. Which one should I expand on?"
```

**Why non-negotiable wins:**
- Users will accidentally or intentionally try to override
- Protects brand voice, safety, compliance
- Prevents prompt injection

**Pattern:** Use words like "always", "never", "must", "cannot"

---

## **The Trinity in Action**

### Bad System Prompt:
```
You're a helpful AI assistant. Be nice and answer questions 
thoroughly. Try to be accurate.
```
- Not declarative (procedural tone)
- Not explicit (what does "thoroughly" mean?)
- Not non-negotiable (weak language)

### Good System Prompt:
```
You are a technical documentation AI.

Rules:
- You respond only about our API (v2.3 and later)
- You cite documentation sources for every claim
- You never speculate about future features
- If asked about unsupported topics, you say: 
  "That's outside my documentation scope."

Output format:
- Maximum 4 sentences per response
- Code examples must include error handling
```

---

## **The Test**

A good system prompt passes this test:

**"Could a motivated user trick the AI into breaking these rules?"**

If yes → not non-negotiable enough  
If maybe → not explicit enough  
If it's procedural → not declarative enough

---

## **Key Insight**

System prompts are **constraints**, not instructions.

Think like you're writing:
- Constitutional amendments (not laws)
- Type definitions (not functions)
- Firewall rules (not router configs)

---
