# Why "be helpful" is useless

This is where most people's prompts fall apart, and they don't even realize it.

---

## The Problem

Look at these common prompt patterns:

❌ "Be helpful"
❌ "Be creative"
❌ "Be thorough"
❌ "Provide a good answer"
❌ "Do your best"

**These are completely useless instructions.**

Why? Because they're **subjective, vague, and unactionable**.

---

## Why LLMs Can't Process Vague Instructions

Remember: **LLMs are prediction machines, not mind readers.**

When you say "be helpful," the model has to guess:
- Helpful to whom? (Beginner? Expert?)
- Helpful how? (Detailed explanation? Quick answer? Step-by-step?)
- Helpful in what context? (Learning? Debugging? Production?)

**The model will use statistical patterns from training data to guess what "helpful" means.**

This creates **output variance** - you get different interpretations every time.

---

## The Real Issue: Adjectives Without Operationalization

"Be helpful" is an **adjective without definition**.

It's like telling a chef: "Make it tasty"
- What does tasty mean?
- Sweet? Savory? Spicy?
- Tasty to a child or a sommelier?

**Vague adjectives force the model into a guessing game.**

---

## What Happens in Practice

**Prompt A:**
```
"Be helpful and explain how async/await works"
```

**Possible outputs:**
- 5000-word academic essay (model thinks "thorough" = helpful)
- ELI5 explanation (model thinks "simple" = helpful)
- Code example only (model thinks "practical" = helpful)
- Comparison with callbacks (model thinks "context" = helpful)

**You have ZERO control over which version you get.**

---

## The Fix: Replace Adjectives with Specifications

Instead of "be helpful," **specify the actual behavior you want:**

✅ **Make it concrete:**

**Instead of:** "Be helpful"
**Write:**
```
INSTRUCTION: Explain async/await

CONSTRAINTS:
- Assume reader knows callbacks but not promises
- Start with the problem it solves
- Include 2 code examples: before/after
- Total length: 300-400 words
- End with common pitfalls
```

Now the model knows EXACTLY what "helpful" means in your context.

---

## Common Useless Instructions & Their Fixes

### 1. "Be creative"
❌ Problem: Creative how? Poetry? Unconventional solutions? Humor?

✅ Fix:
```
Generate 5 unconventional solutions
- Each must challenge standard assumptions
- Include 1 high-risk, high-reward option
- Explain why each is non-obvious
```

---

### 2. "Be thorough"
❌ Problem: Thorough = 500 words or 5000? Cover every edge case or main points?

✅ Fix:
```
Cover these aspects:
1. Core concept
2. Common use cases (3 examples)
3. Limitations
4. Alternatives

Depth: Intermediate (assume reader has basic knowledge)
Length: 600-800 words
```

---

### 3. "Be professional"
❌ Problem: Professional = formal? Neutral? Corporate jargon?

✅ Fix:
```
Tone: Business casual
- No slang or emojis
- Use "we" not "I"
- Avoid hedging language ("maybe", "might", "possibly")
- Direct, confident statements
```

---

### 4. "Provide a good answer"
❌ Problem: This is literally meaningless. Good by what standard?

✅ Fix:
```
Answer must:
- Directly address the question in first sentence
- Provide evidence/reasoning
- Include 1 concrete example
- End with actionable next step
```

---

## The Underlying Principle

**Vague = Variance**
**Specific = Consistency**

When you use adjectives like "helpful," "good," "creative," you're introducing **randomness** into the system.

LLMs are already probabilistic. Don't make them MORE random by being vague.

---

## The Operationalization Test

Before writing any adjective in a prompt, ask:

**"If I gave this instruction to 5 different people, would they all do the same thing?"**

- "Be helpful" → NO (everyone has different ideas of helpful)
- "Provide 3 examples with explanations" → YES (clear, measurable)

**If the answer is NO, you need to operationalize it.**

---

## Why This Matters for Production Systems

In production:
- "Be helpful" = unpredictable outputs
- Unpredictable outputs = bad user experience
- Bad UX = users leave

**You cannot A/B test or debug vague instructions.**

If output quality drops, and your prompt says "be thorough," what do you fix?
You have no idea what "thorough" meant to the model.

But if your prompt says:
```
"Include: problem, solution, code example, explanation"
```

And the output is missing the code example, you know EXACTLY what broke.

---

## Real-World Example

**Vague Prompt (typical):**
```
You are a helpful AI assistant. Answer user questions well and be thorough when needed.
```

**What's wrong:**
- "helpful" - undefined
- "well" - meaningless
- "thorough when needed" - model decides when that is

**Result:** Inconsistent behavior across conversations.

---

**Specific Prompt (better):**
```
You are a technical documentation assistant.

When user asks a question:

1. Identify question type:
   - Conceptual → Explain principle + analogy + example
   - Procedural → Step-by-step with prerequisites
   - Debugging → Diagnostic questions first, then solution

2. Response structure:
   - Direct answer in first 2 sentences
   - Supporting detail
   - Code example (if applicable)
   - Edge cases or limitations

3. Length:
   - Simple questions: 100-200 words
   - Complex questions: 400-600 words
   - If answer requires more, break into sections

4. Never:
   - Apologize for knowledge limits
   - Use placeholder code
   - Make assumptions (ask clarifying questions)
```

**See the difference?**

Every subjective term has been replaced with:
- Concrete behaviors
- Measurable criteria
- Explicit decision trees

---

## The Core Lesson

**Stop using adjectives. Start using specifications.**

| Vague | Specific |
|-------|----------|
| Be helpful | Include 3 examples |
| Be creative | Generate 5 non-obvious solutions |
| Be thorough | Cover X, Y, Z |
| Be professional | Use formal tone, no contractions |
| Write well | Use active voice, max 20 words per sentence |

---

## Quick Check: Do You Understand?

Ask yourself:

**If I removed all adjectives from my prompts, could the model still know what to do?**

- If YES → Your prompt is specification-driven ✅
- If NO → You're relying on vague language ❌

---

**The takeaway:**

> Adjectives are wishes. Specifications are instructions.
> 
> LLMs don't grant wishes. They follow instructions.

---