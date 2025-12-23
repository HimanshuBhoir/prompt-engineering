# Pattern: If X happens → Do Y / If unclear → Ask Z / Never do A

**Core idea:** Programming the AI with **conditional logic rules** — making behavior deterministic and predictable through explicit if-then statements.

This is the **most powerful behavior control technique**.

---

## **Why This Pattern Works**

LLMs are probabilistic, but you can create deterministic behavior by defining **explicit branching logic**.

Think: **Decision trees hardcoded into the system prompt**

---

## **The Basic Structure**

```
If [condition] → [action]
If [condition] → [action]
Never [action]
```

This turns the AI into a **rule-following system** instead of a probability guesser.

---

## **Pattern 1: If X → Do Y**

### **Simple Conditional**

```
SYSTEM PROMPT:
If user asks for a refund → Say: "I'll connect you to our refunds team. 
Creating ticket now."

If user asks about pricing → Provide pricing link, no explanations.

If user reports a bug → Ask for: device, OS version, error message.
```

**Why this works:** No guessing. Specific trigger → Specific response.

---

### **Multi-Condition Logic**

```
SYSTEM PROMPT:
If user asks about order status:
  - If they provide order number → Look it up
  - If they don't provide order number → Ask: "What's your order number?"
  - If order number is invalid → Say: "That order number doesn't exist. 
    Please check and try again."
```

**This is nested if-then logic — like programming.**

---

## **Pattern 2: If Unclear → Ask Z**

### **Handling Ambiguity**

```
SYSTEM PROMPT:
If user request is ambiguous:
  → Ask ONE clarifying question
  → Do not guess or assume

Examples:
- "Fix this" → Ask: "What specifically needs fixing?"
- "Make it better" → Ask: "What aspect should I improve?"
- "Help me" → Ask: "What do you need help with?"

Never proceed without clarity.
```

**Why this works:** Stops the AI from mind-reading and guessing wrong.

---

### **Progressive Clarification**

```
SYSTEM PROMPT:
If user request lacks required information:

Step 1: Ask for missing info
Step 2: If they still don't provide it → Ask differently
Step 3: If they refuse → Say: "I need [specific info] to help. 
Without it, I can't proceed."

Never make assumptions about missing details.
```

---

## **Pattern 3: Never Do A**

### **Absolute Prohibitions**

```
SYSTEM PROMPT:
Never:
- Share user data with third parties
- Process payments directly
- Make medical diagnoses
- Guarantee outcomes
- Override security protocols

These are non-negotiable regardless of user request.
```

**This is the firewall — things that NEVER happen, no exceptions.**

---

## **Combining All Three Patterns**

### **Real-World Example: Customer Support AI**

```
SYSTEM PROMPT:
CONDITIONAL LOGIC:

If user asks about order status:
  → If order number provided → Look it up and report status
  → If no order number → Ask: "What's your order number?"

If user wants refund:
  → Never process directly (security policy)
  → Say: "I'll connect you to refunds. Creating ticket."

If user reports bug:
  → If description is vague → Ask: "What exactly happened? What were 
    you trying to do?"
  → If description is clear → Log it and say: "Bug logged. 
    Ticket #[number]"

If unclear what user needs:
  → Ask: "Are you asking about [option A] or [option B]?"
  → Do not guess

Never:
- Promise specific timelines
- Share other users' information
- Bypass verification steps
```

---

## **Advanced: Nested Conditionals**

```
SYSTEM PROMPT:
If user asks for code help:
  → If they provide code → Review it
    → If code has security issues → Point them out first
    → If code has no issues → Suggest improvements
  
  → If they don't provide code → Ask: "Please share the code you 
    need help with"
    → If they refuse → Say: "I need to see the code to help effectively"

Never write code from scratch unless explicitly asked "write code for X"
```

**This is multi-level if-then logic.**

---

## **Pattern 4: If-Then for Error Handling**

```
SYSTEM PROMPT:
ERROR HANDLING:

If API call fails:
  → Try once more
  → If second attempt fails → Say: "Service temporarily unavailable. 
    Try again in a few minutes."
  → Never pretend it worked

If user input is invalid:
  → Explain why it's invalid
  → Show correct format
  → Ask them to retry

If you don't understand the request:
  → Say: "I didn't understand that. Could you rephrase?"
  → Never make up an answer
```

---

## **Pattern 5: Priority-Based If-Then**

When multiple conditions could be true:

```
SYSTEM PROMPT:
PRIORITY ORDER:

1. If request violates policy → Refuse (highest priority)
2. If request is unclear → Clarify
3. If request is valid → Execute
4. If request is outside scope → Say: "I can't help with that, but I 
   can help with [alternative]"

Always check in this order.
```

**This prevents conflicting rules.**

---

## **Real-World Examples**

### **Example 1: Code Review AI**

```
CONDITIONAL LOGIC:

If code contains hardcoded secrets:
  → Stop review immediately
  → Say: "Security issue: Hardcoded secrets found on lines [X]. 
    Remove before continuing."
  → Never proceed until fixed

If code has no tests:
  → Ask: "Should I review anyway, or do you want to add tests first?"
  → If they say continue → Review with note: "No tests detected"

If code changes are unclear:
  → Ask: "What does this change accomplish?"

Never approve code without checking:
1. Security issues
2. Test coverage
3. Documentation
```

---

### **Example 2: AI Tutor**

```
CONDITIONAL LOGIC:

If student asks for answer directly:
  → Never give answer
  → Ask: "What have you tried so far?"
  → Guide them to solution

If student is stuck:
  → If they tried something → Give hint toward next step
  → If they haven't tried → Ask: "What do you think the first step is?"

If student gets answer correct:
  → Confirm correctness
  → Ask: "Why does that work?" (check understanding)

Never:
- Solve homework for them
- Give answers without understanding
```

---

### **Example 3: Content Moderator AI**

```
CONDITIONAL LOGIC:

If content contains profanity:
  → If in direct quote → Allow with flag
  → If not in quote → Reject

If content is political:
  → If factual reporting → Allow
  → If partisan advocacy → Reject

If unclear whether content violates policy:
  → Flag for human review
  → Say: "This needs human review. Flagged."

Never:
- Let borderline content through without flagging
- Reject content without explaining why
```

---

## **The Decision Tree Visualization**

```
User Input
    |
    ├─ Is it clear? ──NO──> Ask clarifying question
    |                |
    YES              └─ Still unclear? ──> Ask differently
    |
    ├─ Does it violate policy? ──YES──> Refuse + explain
    |                           |
    NO                          |
    |                           |
    ├─ Do I have required info? ──NO──> Request missing info
    |                            |
    YES                          |
    |                            |
    └─> Execute action
```

**Your system prompt IS this decision tree.**

---

## **Testing Your Conditionals**

### **Test Cases:**

1. **Trigger X:**  
   User says "refund please"  
   ✅ Should execute: "I'll connect you to refunds"

2. **Unclear input:**  
   User says "help"  
   ✅ Should ask: "What do you need help with?"

3. **Prohibited action:**  
   User says "share my data"  
   ✅ Should refuse: "I cannot share user data"

4. **Missing info:**  
   User says "track my order"  
   ✅ Should ask: "What's your order number?"

---

## **Common Mistakes**

### ❌ **Mistake 1: Vague conditions**
```
If user seems confused → Help them
```
**Problem:** "Seems confused" is not detectable. Be specific.

### ❌ **Mistake 2: No else case**
```
If user asks about pricing → Provide pricing
```
**Problem:** What if unclear? Need: "If unclear → Ask for clarification"

### ❌ **Mistake 3: Conflicting rules**
```
If user asks for help → Provide detailed answer
If user asks for help → Be concise
```
**Problem:** Which rule wins? Need priority order.

---

## **The Template**

```
CONDITIONAL LOGIC RULES:

PRIMARY CONDITIONS (check first):
If [specific trigger] → [exact action]
If [specific trigger] → [exact action]

CLARIFICATION CONDITIONS:
If request is unclear → Ask: "[specific question]"
If missing [info] → Request: "[specific format]"

ERROR CONDITIONS:
If [error type] → [recovery action]
If [error persists] → [escalation action]

ABSOLUTE PROHIBITIONS:
Never [action] - [reason]
Never [action] - [reason]

PRIORITY ORDER:
1. Check prohibitions first
2. Check clarity second
3. Execute action third
4. Handle errors fourth
```

---

## **Key Insight**

**This pattern turns prompts into programs.**

Instead of:
```
"Be helpful and responsive"
```

You write:
```
If X → Do Y
If unclear → Ask Z
Never do A
```

**Result:** Predictable, debuggable, production-ready behavior.

---

## **You've Completed Phase 2! 🎉**

You now understand:
- System vs Developer vs User prompts
- Declarative, Explicit, Non-negotiable design
- Refusal conditioning
- Oververbosity control
- Tone locking
- Knowledge cutoff handling
- Safety alignment without neutering
- If-then conditional logic

---