# Instructions vs Context vs Constraints

Let's break down the three fundamental building blocks that most people confuse when writing prompts.

## The Core Distinction

Think of building a prompt like giving directions to someone:

- **Instructions** = What to DO
- **Context** = What to KNOW
- **Constraints** = What NOT to do / How to operate

Most beginners dump everything into "instructions" and wonder why the output is messy.

---

## 1. INSTRUCTIONS (The Task)

**Definition:** The explicit action you want the LLM to perform.

**Characteristics:**
- Verb-driven (analyze, write, extract, summarize)
- Specific outcome-focused
- Answers: "What should the model produce?"

**Examples:**
- ❌ Bad: "Help me with this code"
- ✅ Good: "Refactor this function to use async/await"

**Key principle:** Instructions should be **atomic** - one clear action per instruction block.

---

## 2. CONTEXT (The Knowledge)

**Definition:** Background information the model needs to make informed decisions.

**Characteristics:**
- Declarative (states facts, provides data)
- Enables better reasoning
- Answers: "What does the model need to know?"

**Examples:**
- User's skill level ("You're helping a beginner")
- Project requirements ("This is for a healthcare app with HIPAA compliance")
- Historical information ("Previous attempts failed because X")
- Domain knowledge ("In our codebase, we use factory pattern for...")

**Key principle:** Context is **passive information** that shapes HOW instructions are executed.

---

## 3. CONSTRAINTS (The Boundaries)

**Definition:** Rules, limits, and boundaries that control behavior.

**Characteristics:**
- Restrictive/prescriptive
- Prevents unwanted outputs
- Answers: "What are the limits and rules?"

**Types of constraints:**

**Format constraints:**
- "Output must be valid JSON"
- "Maximum 3 sentences"
- "Use markdown headers"

**Behavioral constraints:**
- "Never use external libraries"
- "Do not make assumptions; ask for clarification"
- "Avoid technical jargon"

**Logical constraints:**
- "Only suggest solutions that work in Python 3.8+"
- "Stay within 100ms execution time"

**Key principle:** Constraints are **non-negotiable rules** that define the operational boundary.

---

## Why This Matters

**Without separation, prompts become ambiguous:**

❌ **Confused prompt:**
```
"Write good code for a sorting algorithm that's fast and explain it well"
```

What's the instruction? What's the constraint? What context is missing?

---

✅ **Clear prompt with separation:**

```
CONTEXT:
- Target audience: Junior developers learning algorithms
- Environment: Python 3.10+
- Use case: Teaching material for bootcamp

INSTRUCTION:
Implement a quicksort algorithm

CONSTRAINTS:
- Include inline comments explaining each step
- Use descriptive variable names (no single letters)
- Keep function under 30 lines
- Time complexity must be O(n log n) average case
```

See the difference? The model now knows:
- WHO it's writing for (context)
- WHAT to do (instruction)
- HOW to operate (constraints)

---

## Common Mistakes

### Mistake #1: Hiding constraints in instructions
❌ "Write a summary but keep it short"
- "Write a summary" = instruction
- "keep it short" = constraint (but vague)

✅ Better:
```
INSTRUCTION: Summarize the article
CONSTRAINT: Maximum 50 words
```

### Mistake #2: Putting context in constraints
❌ "Don't assume the user knows React" (This is context about the user!)

✅ Better:
```
CONTEXT: User is new to React
CONSTRAINT: Explain every React-specific term you use
```

### Mistake #3: Treating everything as instructions
❌ "You are an expert Python developer. Write clean code. Make it efficient. Use best practices. Create a function that..."

This mixes role (context), quality expectations (constraints), and task (instruction).

---

## The Mental Model

```
CONTEXT → Shapes understanding
    ↓
INSTRUCTION → Defines output
    ↓
CONSTRAINTS → Controls boundaries
```

**Flow:**
1. Load context into "working memory"
2. Execute instruction using that context
3. Apply constraints to the output

---

## Practice Exercise

I'm going to give you a messy prompt. Your job: **Separate it into Instructions, Context, and Constraints.**

**Messy prompt:**
> "I need help writing an email to my boss about the project delay. I'm a junior developer and this is my first big project. Make it professional but not too formal. Don't make excuses. Keep it under 150 words. Explain that the API integration took longer than expected."

**Your turn:** Break this down into:
- Context: ?
- Instruction: ?
- Constraints: ?