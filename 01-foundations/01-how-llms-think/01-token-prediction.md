## **How LLMs Actually Think**

### **Token prediction (why wording matters)**

Nothing else.

---

## 1️⃣ Core Mental Model (this must click)

An LLM does **not**:

* understand meaning
* reason like a human
* know facts

An LLM **only does one thing**:

> **Given previous tokens, predict the most likely next token**

Everything you see—logic, code, explanations—is an **emergent effect** of this.

Think of it like this:

```
P(next_token | all_previous_tokens)
```

That’s it.

No memory. No intent. No truth-checking.

---

## 2️⃣ What is a *token* (important precision)

A token is **not a word**.

Examples:

* `"prompt"` → 1 token
* `"engineering"` → 1 token
* `"ChatGPT"` → 1 token
* `"unbelievable"` → might be `["un", "believable"]`
* `"functionName()"` → multiple tokens

👉 The model sees **token sequences**, not sentences.

---

## 3️⃣ Why wording changes output (deep reason)

Because **small wording changes change token probabilities**.

Example:

### Prompt A

> *Explain closures in JavaScript*

### Prompt B

> *Explain closures in JavaScript simply*

That single word **“simply”** shifts probabilities toward:

* shorter sentences
* fewer technical tokens
* analogy-style patterns

The model isn’t “trying to be simple”.
It’s following a **token distribution it learned from training data**.

---

## 4️⃣ Order matters more than you think

LLMs are **left-to-right** predictors.

This means:

* Earlier tokens influence **everything**
* Late constraints are weaker

### Bad

> Write code. Use TypeScript. Be concise.

### Better

> You are a TypeScript expert.
> Write concise code.

Why?

* “TypeScript expert” influences **every following token**
* Late instructions compete with already-formed probability paths

---

## 5️⃣ Why vague prompts fail

Example:

> *Write high-quality code*

Problem:

* “high-quality” maps to **many token clusters**
* No dominant probability path
* Model picks a generic average

Result:

* Overexplained
* Safe
* Boring
* Often wrong

LLMs prefer **specific, narrow probability spaces**.

---

## 6️⃣ Concrete example (token steering)

Prompt 1:

> *Fix this code*

Prompt 2:

> *Apply the minimal change needed to fix this bug.
> Output only the changed lines.*

Prompt 2 works better because:

* “minimal change” suppresses refactor-heavy tokens
* “only changed lines” collapses output space
* Fewer valid next tokens = more control

---

## 7️⃣ Key takeaway (memorize this)

> **Prompting is probability steering, not instruction giving**

You are not telling the model *what to do*.
You are **shaping which tokens are likely**.

---

## 8️⃣ 5 rules you’ll use everywhere

1. Early tokens matter more than later ones
2. Specific beats vague every time
3. Constraints reduce randomness
4. Output format controls thinking
5. Fewer valid outputs = better results

---

## 9️⃣ Micro-exercise (5 minutes, optional)

Take this prompt:

> *Explain React useEffect*

Rewrite it to **strongly bias** toward:

* short answer
* production insight
* no beginner explanation

Do **not** solve it now unless you want feedback.

---

**Answer Prompt**

```
You are a senior React engineer.
Explain React’s useEffect from a production perspective.
Focus on real-world pitfalls, dependency management, and performance implications.
Keep the explanation concise and avoid beginner-level definitions.
Limit the response to 5–6 bullet points.
```

**Reason**

* “Senior React engineer” biases token selection toward expert patterns
* “Production perspective” suppresses tutorial-style explanations
* Explicit focus areas narrow the probability space
* “Avoid beginner-level definitions” removes basic conceptual tokens
* Bullet-point and length constraints strongly control verbosity and structure
