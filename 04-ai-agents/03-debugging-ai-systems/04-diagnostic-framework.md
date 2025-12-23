## Part 4: The Diagnostic Framework - Debugging Prompts Systematically

This is the methodology. When any prompt fails, follow this framework.

### The 7-Step Diagnostic Process

**Step 1: Reproduce**
Can you make the failure happen consistently? If yes, proceed. If no, you need more data - the failure might be due to randomness (temperature >0) or external factors.

**Step 2: Isolate**
Remove everything except the failing component. Test with minimal prompt and minimal context. Does it still fail? This tells you if the failure is inherent to the instruction or caused by interactions.

Example: Agent fails to validate email addresses.

Isolation test:
```
MINIMAL PROMPT:
"You are an assistant. Before calling send_email, verify the 'to' parameter contains an '@' symbol. If not, refuse to send."

TEST INPUT:
"Send email to john.smith"

RESULT:
[Model calls send_email with invalid address]
```

Failure persists in isolation. The instruction itself is ineffective.

Alternative isolation test:
```
MINIMAL PROMPT:
"You are an assistant. Before calling send_email, verify the 'to' parameter contains an '@' symbol. If not, refuse to send."

TEST INPUT:
"Send email to john.smith@example.com"

RESULT:
[Model correctly validates and sends]
```

Wait, now it works? The instruction is fine. The failure in the full prompt must be due to interference from other instructions.

**Step 3: Vary**
Change one variable at a time:
- Temperature (does randomness cause it?)
- Prompt length (does context size matter?)
- Instruction position (does location matter?)
- Wording (does phrasing matter?)

Example: Test instruction position
```
TEST A: Validation rule at top of prompt → fails 80% of time
TEST B: Validation rule at bottom of prompt → fails 20% of time
TEST C: Validation rule right before tool definition → fails 5% of time
```

Conclusion: Position matters. Keep critical rules close to where they're needed.

**Step 4: Inspect**
Look at the model's intermediate outputs. What is it thinking?

Add debug outputs:
```
"Before each action, output your reasoning:
<debug>
What instruction am I following: [instruction]
Why I'm taking this action: [reason]
What I expect to happen: [expectation]
</debug>"
```

This reveals the model's mental state. You see which instruction it thinks it's following and why.

**Step 5: Compare**
Find a similar scenario where it works correctly. What's different?

Working scenario: Model correctly validates email when user says "send email to john@example.com"
Failing scenario: Model doesn't validate when user says "email john"

Difference: In failing case, the email isn't explicitly formatted. The model needs to extract/construct it first. Your validation instruction doesn't account for this.

**Step 6: Hypothesize**
Form a theory about why it's failing.

Hypothesis: "The model validates email addresses when they're explicitly provided, but skips validation when it has to construct the address from partial information (like 'john' → needs to look up full email). The validation instruction only fires on explicit email strings, not on addresses retrieved from tools."

**Step 7: Verify**
Test your hypothesis with targeted experiments.

```
TEST 1: User provides full email explicitly
Input: "send email to john@example.com"
Prediction: Validation happens
Result: ✓ Validates correctly

TEST 2: User provides partial info, agent looks up email
Input: "send email to john"
Model action: Calls get_contact_info("john") → returns "john@example.com"
Prediction: Validation skipped
Result: ✓ Skips validation, hypothesis confirmed

FIX: Update instruction
"Before calling send_email, verify the 'to' parameter contains an '@' symbol. This applies whether the email was provided by the user OR retrieved from a tool like get_contact_info. Always validate the final 'to' value before calling send_email."
```

Hypothesis confirmed and fixed.

### The Diagnostic Decision Tree

When debugging, follow this flowchart mentally:

```
Output failed?
├─ Does failure happen 100% of the time?
│  ├─ YES: Deterministic bug → focus on instruction logic
│  └─ NO: Probabilistic bug → check temperature, instruction ambiguity
│
├─ Does it fail in isolated test?
│  ├─ YES: Instruction itself is broken → rewrite instruction
│  └─ NO: Instruction is overridden/interfered with → check for conflicts
│
├─ Is the instruction even relevant to the failure?
│  ├─ YES: Instruction exists but not working → make it more explicit
│  └─ NO: Missing instruction → add new instruction
│
└─ Can you identify a pattern in failures?
   ├─ YES: Systematic issue → architectural fix needed
   └─ NO: Random failures → increase constraint strength or reduce task complexity
```

### The Comparison Matrix

When debugging complex agents, create a matrix:

| Scenario | Expected | Actual | Instruction | Status |
|----------|----------|--------|-------------|--------|
| Valid email provided | Validate and send | Validates and sends | "Verify @ symbol" | ✓ |
| Invalid email provided | Refuse to send | Sends anyway | "Verify @ symbol" | ✗ |
| Email from tool lookup | Validate and send | Skips validation | "Verify @ symbol" | ✗ |
| Empty email field | Refuse to send | Sends anyway | "Verify @ symbol" | ✗ |

This reveals patterns. The instruction works for scenario 1 but fails for 2, 3, 4. They all involve non-explicit email values. The pattern: instruction only works on explicit user-provided emails.

### The "Minimal Reproduction" Principle

Always reduce a failing case to its simplest form:

```
ORIGINAL FAILING INTERACTION:
- 50 messages of conversation history
- User uploads document
- Complex multi-step request
- Agent fails at step 7

MINIMAL REPRODUCTION:
- System prompt with just step 7 instructions
- Single user message: "Do step 7"
- Expected behavior vs actual behavior

If minimal case works, the bug is in context accumulation.
If minimal case fails, the bug is in the instruction for step 7.
```

### Root Cause Categories

After diagnosis, classify the root cause:

**1. Missing Instruction**
You never told the model to handle this case. Add instruction.

**2. Vague Instruction**
You told it, but not specifically enough. Clarify instruction.

**3. Conflicting Instructions**
You told it to do X and also Y, which are mutually exclusive. Resolve conflict with priority or condition.

**4. Hidden Assumption**
Your instruction assumes something that isn't always true. Make assumption explicit or handle both cases.

**5. Context Loss**
Instruction exists but is forgotten by the time it's needed. Reposition or repeat instruction.

**6. Scope Mismatch**
Your instruction covers case A, but this is case A.1 which is slightly different. Broaden instruction scope.

**7. Enforcement Gap**
Instruction is advisory but needs to be mandatory. Add enforcement mechanism.

### The "Before/After" Documentation

Document every fix:

```
BUG REPORT #23
Date: 2024-12-21
Symptom: Agent sends emails to invalid addresses
Frequency: ~30% of email sends

DIAGNOSIS:
- Isolated issue to validation instruction
- Found instruction only validated explicit user-provided emails
- Skipped validation for emails retrieved from tools
- Root cause: Instruction scope too narrow

FIX APPLIED:
BEFORE:
"Before calling send_email, verify the 'to' parameter contains '@'"

AFTER:
"Before calling send_email, ALWAYS verify the 'to' parameter contains '@'.
This applies to:
- Emails provided directly by user
- Emails retrieved from get_contact_info or other tools
- Emails constructed from user data
Verify the final value in the 'to' parameter, regardless of its source."

VERIFICATION:
✓ Tested with explicit email → works
✓ Tested with tool-retrieved email → works  
✓ Tested with constructed email → works
✓ Regression test: other functions still work
```

This creates a knowledge base for future debugging.

---