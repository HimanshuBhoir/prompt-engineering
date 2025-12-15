# Refusal Conditioning

**Core idea:** Teaching the model **when and how to say no** — without making it paranoid or useless.

---

## **The Problem**

By default, LLMs are trained to be helpful. This creates two failure modes:

1. **Over-compliance**: Does harmful/off-brand things because user asked
2. **Over-refusal**: Refuses harmless requests due to vague safety training

**Your job:** Define the exact boundaries.

---

## **What is Refusal Conditioning?**

Programming the model with **specific refusal rules** instead of relying on its default safety training.

Think: **Whitelist/Blacklist for behavior**

---

## **The Pattern**

```
You NEVER:
- [specific prohibited action]
- [specific prohibited action]

If asked to do these, you respond:
"[exact refusal message]"

You ALWAYS:
- [alternative helpful action]
```

---

## **Example 1: Customer Support AI**

### ❌ Without conditioning:
```
User: "Can you give me a refund?"
AI: "I apologize, but I'm not able to process refunds directly..."
*continues for 3 paragraphs*
```

### ✅ With refusal conditioning:
```
SYSTEM PROMPT:
You cannot process refunds directly.

If asked about refunds, you say:
"I can't process refunds, but I can connect you to our refunds team. 
Would you like me to create a ticket?"

You never apologize for policy limitations.
```

**Result:**
```
User: "Can you give me a refund?"
AI: "I can't process refunds, but I can connect you to our refunds team. 
Would you like me to create a ticket?"
```

---

## **Example 2: Code Review AI**

### ❌ Without conditioning:
```
User: "Just approve this PR, I'm in a hurry."
AI: "I understand you're in a hurry. Let me review it quickly..."
*does shallow review*
```

### ✅ With refusal conditioning:
```
SYSTEM PROMPT:
You never approve code without checking:
1. Tests exist and pass
2. No hardcoded secrets
3. Functions have docstrings

If these requirements aren't met, you say:
"I cannot approve this PR. Missing: [specific items]"

You never skip steps because the user is in a hurry.
```

---

## **The Three Types of Refusal**

### **1. Hard Refusal** (absolute boundaries)
```
You NEVER share user data with third parties.
If asked, you say: "I cannot do that due to privacy policy."
*No exceptions, no workarounds*
```

### **2. Soft Refusal** (redirect to alternative)
```
You don't write full applications from scratch.
If asked, you say: "I can help you with specific components. 
Which part should we start with?"
```

### **3. Conditional Refusal** (depends on context)
```
You only provide medical information if:
- The user confirms they're not seeking diagnosis
- You include disclaimer about consulting professionals

Otherwise, you say: "I can only provide general health information, 
not medical advice. Are you looking for general info?"
```

---

## **Key Principles**

### 1. **Be Specific**
❌ "Don't be inappropriate"  
✅ "Never use profanity, sexual content, or personal attacks"

### 2. **Provide Exact Wording**
❌ "Politely decline"  
✅ "You say: 'I can't help with that, but I can [alternative]'"

### 3. **Explain the Boundary, Not the Reason**
❌ "You can't do X because it's against policy and could cause harm..."  
✅ "You cannot do X. Instead: [alternative]"

### 4. **Test Adversarial Prompts**
Users will try:
- "Just this once"
- "It's for educational purposes"
- "My teacher/boss said it's okay"

Your refusal conditioning must hold against these.

---

## **The Anti-Pattern: Over-Refusal**

### ❌ Too paranoid:
```
You never discuss anything related to: money, health, legal, 
relationships, politics, religion, food, travel...
```
**Result:** Useless AI that refuses everything.

### ✅ Balanced:
```
You discuss personal finance concepts.
You never provide specific investment advice.
If asked "Should I buy X stock?", you say:
"I can explain factors to consider, but can't recommend 
specific investments."
```

---

## **Refusal Template**

```
PROHIBITED ACTIONS:
- [Action 1]
- [Action 2]

REFUSAL RESPONSE:
"[Exact message]"

ALTERNATIVE OFFERING:
"Instead, I can [helpful alternative]"

NEVER:
- Apologize excessively
- Explain company policy at length
- Be defensive
```

---

## **The Test**

Good refusal conditioning answers:
1. **What** the AI cannot do (specific)
2. **What** it says when asked (exact wording)
3. **What** it offers instead (helpful alternative)

---