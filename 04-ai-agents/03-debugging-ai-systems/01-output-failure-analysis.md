# PHASE 4 SECTION 3: DEBUGGING AI LIKE SOFTWARE

## Part 1: Why Output Failed

When an LLM produces wrong output, there's always a reason. It's not random. It's not the model being "stupid." Something in your prompt (or context) led to that specific failure. Your job is to find what.

### The Three Failure Categories

**Category 1: Instruction Conflict**
The model received contradictory instructions and chose one over the other.

Example: Your system prompt says "be concise" but later says "explain thoroughly with examples." When these conflict, the model picks based on recency, emphasis, or randomness. You get inconsistent behavior.

**Category 2: Instruction Invisibility**
The instruction exists but the model didn't see it as applying to this situation.

Example: You wrote "never use tool X for customer data" but the model used it anyway. Why? Because your instruction said "customer data" but the variable was labeled "user_info" in the actual call. The model didn't connect them.

**Category 3: Context Overload**
The model had too much information and lost track of critical instructions or facts.

Example: You provide 50 tools in the system prompt, then 20 messages of conversation history, then a complex user request. The model picks the wrong tool because your specific guidance about when to use tool Y was buried in the middle of the prompt, and it didn't resurface during reasoning.

### Forensic Analysis Process

When output fails, ask these questions in order:

**1. What did the model actually do?**
Describe the behavior factually. "Model called send_email tool with empty recipient field" not "model was dumb."

**2. What should it have done instead?**
Be specific. "Should have called get_contact_info first, then send_email with populated recipient."

**3. What instruction should have prevented this?**
Search your prompt. Is there an instruction covering this case? If yes, go to question 4. If no, you have an instruction gap.

**4. Why didn't that instruction apply?**
This is the key question. Possible reasons:
- Instruction was too vague ("be careful with emails" vs "never send email without verifying recipient field is not empty")
- Instruction was contradicted elsewhere in the prompt
- Instruction was buried and forgotten in long context
- Instruction used different terminology than the runtime situation ("customer" vs "user")
- Instruction had an implicit assumption that wasn't true ("after verifying account" but verification step was skipped)

**5. What's the minimal change to fix this?**
Don't rewrite everything. Target the specific failure point.

### Practical Example: Debugging a Failed Tool Call

**Failure:** Agent called `delete_account(account_id="12345")` immediately when user said "I want to cancel."

**What it did:** Called delete tool directly.

**What it should have done:** Ask user to confirm, explain consequences, verify identity, THEN delete.

**Search the prompt:** Found this instruction: "Before destructive actions, confirm with user."

**Why didn't it apply?** The instruction exists but "destructive actions" is vague. The model didn't categorize account deletion as "destructive" in the moment. It needed explicit enumeration.

**Minimal fix:**
```
Before these actions, you MUST get explicit user confirmation:
- delete_account
- cancel_subscription  
- remove_payment_method
- close_ticket (if ticket is still open)

Confirmation means: explain what will happen, ask "Are you sure?", wait for explicit yes.
```

Now the instruction is unambiguous. The model can't miss it.

### The "Instruction Audit" Technique

When debugging, do this:

1. Take the failed interaction
2. Go through your system prompt line by line
3. For each instruction, ask: "Did the model follow this?" Mark with ✓ or ✗
4. For each ✗, ask: "Why not?"

This reveals patterns. If the model consistently ignores instructions in the middle of your prompt, you have a positioning problem. If it ignores conditional instructions ("if X then Y"), you have a specificity problem.

### Common Root Causes

**Recency Bias:** Model pays more attention to recent instructions. If your important rule is at the top of a 3000-token system prompt and the user's message contradicts it subtly, the user message wins.

**Implicit Assumptions:** You wrote "after authentication, proceed with request." But your authentication check tool can return "partial authentication" and you never told the model what to do in that case. It improvises.

**Scope Ambiguity:** You said "never share personal data" but didn't define what counts as personal data. The model shares an email address because it doesn't consider that "personal data" in this context.

**Competing Priorities:** You told the model to be "helpful" and to "follow security protocols." User asks for something that requires bypassing a protocol. "Helpful" wins because it's more emotionally weighted.

**Token Distance:** Your critical safety instruction is 2000 tokens away from the point where the model needs to apply it. The model's attention has faded.

### The Fix Hierarchy

When you identify why something failed, fix it at the right level:

**Level 1: Clarify Language (easiest)**
Change vague terms to specific ones. "Be careful" → "Verify field X is not empty before proceeding."

**Level 2: Add Explicit Cases**
Enumerate the situations where a rule applies. Don't rely on the model to infer.

**Level 3: Restructure for Salience**
Move critical instructions closer to where they're needed. Use formatting to make them stand out.

**Level 4: Add Enforcement Mechanisms**
Add checks and balances. "Before calling tool X, first check condition Y. If Y is false, do not call X."

**Level 5: Simplify the Task**
Sometimes the task is too complex for a single prompt. Break it into multiple agents or steps.

Most failures are fixed at Level 1 or 2. Don't jump to Level 5 unless you've exhausted simpler fixes.

---

