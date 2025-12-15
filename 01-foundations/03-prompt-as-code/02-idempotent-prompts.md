# Idempotent Prompts

**Idempotency:** Running the same operation multiple times produces the same result.

(Borrowed from computer science/API design)

---

## The Concept

**Idempotent prompt:**
- Same input → Same output (every time)
- No hidden state changes
- No dependency on previous runs
- Predictable and testable

**Non-idempotent prompt:**
- Output varies between runs
- Depends on conversation history
- Relies on implicit context
- Unpredictable

---

## Why This Matters

**In production, non-idempotent prompts = chaos:**

- User reports a bug → You can't reproduce it
- A/B testing → Results are meaningless (outputs drift)
- Caching → Impossible (same input ≠ same output)
- Debugging → Nightmare (which run was wrong?)

**Idempotent prompts = reliable systems**

---

## What Breaks Idempotency

### 1. **Conversation History Dependency**

❌ **Non-idempotent:**
```
User turn 1: "Analyze this code"
User turn 5: "Now check for security issues"
```

The model relies on context from turn 1.

**Problem:** Running turn 5 alone gives different results.

---

✅ **Idempotent:**
```
Analyze this code for security issues:
[full code here]

Focus on: authentication, input validation, SQL injection
```

**Everything needed is in one prompt.**

---

### 2. **Implicit References**

❌ **Non-idempotent:**
```
"Update the function we discussed earlier"
```

**"Earlier" where?** Model has no stable reference.

---

✅ **Idempotent:**
```
Update this function:
[paste full function]

Changes needed:
- Add error handling
- Use async/await
```

---

### 3. **Time-Dependent Instructions**

❌ **Non-idempotent:**
```
"Summarize today's news"
```

"Today" changes. Same prompt → different output tomorrow.

---

✅ **Idempotent (if needed):**
```
Summarize news from December 14, 2025 about [topic]
```

Or accept that this prompt is inherently non-idempotent (which is fine for that use case).

---

### 4. **Random/Creative Directives Without Constraints**

❌ **Non-idempotent:**
```
"Generate a creative solution"
```

"Creative" = high variance.

---

✅ **Idempotent:**
```
Generate 3 solutions following this format:
1. [Approach name]: [Description in 20 words]
2. [Approach name]: [Description in 20 words]
3. [Approach name]: [Description in 20 words]

Use temperature=0
```

**Constraints + deterministic sampling = idempotency**

---

### 5. **Unspecified Output Format**

❌ **Non-idempotent:**
```
"List the issues in this code"
```

Sometimes you get bullet points, sometimes paragraphs, sometimes numbered lists.

---

✅ **Idempotent:**
```
List issues in this format (exactly):

ISSUE #1
Line: [number]
Type: [bug|style|performance]
Description: [one sentence]

ISSUE #2
...
```

---

## The Idempotency Checklist

**Before deploying a prompt, verify:**

✅ **Self-contained:** All needed information is in the prompt
✅ **No pronouns:** No "this", "that", "earlier", "above" (unless referring to content IN the prompt)
✅ **Explicit format:** Output structure is defined
✅ **Deterministic sampling:** Temperature = 0 (for tasks requiring consistency)
✅ **No time references:** Unless accepting non-idempotency
✅ **Testable:** Can run 10 times and verify consistency

---

## Practical Pattern: The Self-Contained Prompt

```
ROLE: [Who is doing this]

TASK: [What to do - specific, measurable]

INPUT:
[All data needed - no references to "above" or "previous"]

CONSTRAINTS:
- [Boundary 1]
- [Boundary 2]

OUTPUT FORMAT:
[Exact structure]

EXAMPLE:
Input: [sample]
Output: [expected result]
```

**Everything needed is HERE. No external dependencies.**

---

## When Non-Idempotency Is Acceptable

**Some use cases don't need idempotency:**

1. **Conversational assistants**
   - Multi-turn chat by design
   - Context is a feature, not a bug

2. **Creative content**
   - You WANT variety
   - "Generate 5 blog post ideas" should give different ideas each time

3. **Real-time data**
   - "What's the current stock price"
   - Non-idempotent by nature

**Key:** Know which category your prompt belongs to.

---

## Key Takeaways

**Idempotent prompts are:**
- Self-contained (no external dependencies)
- Explicit (no ambiguity)
- Deterministic (same input → same output)
- Testable (can verify consistency)

**Build idempotent prompts by:**
1. Including all needed data in the prompt
2. Avoiding references to conversation history
3. Specifying exact output format
4. Using deterministic sampling (temperature=0)
5. Eliminating ambiguous language

**Test by running multiple times and comparing outputs.**

---

**Mental model:**

> If you copy-paste this prompt into a fresh session, does it still work?
> 
> If NO → not idempotent.

---