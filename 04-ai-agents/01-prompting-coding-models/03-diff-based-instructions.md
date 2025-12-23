Continuing.

## **Prompting Coding Models — Why diff-based instructions outperform full rewrites**

Coding models are trained heavily on **full code completions**. When you ask for code without constraining the output shape, the highest-probability behavior is to emit a complete solution, not a modification.

This clashes with real engineering work, which is almost always **incremental**.

A diff-based instruction works because it changes the model’s job from *author* to *editor*.

An author invents.
An editor preserves.

---

Here’s the key mechanism.

When the model believes it is writing a full solution, it optimizes for:

* coherence of the whole file
* stylistic consistency
* “best practice” patterns

When the model believes it is editing, it optimizes for:

* local correctness
* minimal change
* alignment with existing structure

That shift happens purely through prompting.

---

Why full rewrites are dangerous.

Once the model starts a rewrite, it:

* re-evaluates naming
* reinterprets architecture
* reorders logic

Not because it’s wrong — but because that’s what *good full solutions* look like in training data.

You didn’t ask for a better solution.
You asked for a **change**.

---

Why diff-based framing works.

When you say “output only changed lines” or “return a patch”, you collapse the output space. Most tokens are now invalid. The model can’t safely introduce large refactors because they wouldn’t fit the required shape.

This forces the model into **surgical reasoning**.

---

Short example.

Prompt without diff framing:
“Fix the null pointer bug.”

Likely behavior:
Function rewritten with guards, renamed variables, extra helpers.

Prompt with diff framing:
“Fix the null pointer bug. Output only the modified lines.”

Now the model is biased toward:

* smallest viable fix
* local reasoning
* minimal disruption

Same model. Different behavior.

---

Why this matters for agents.

Agents that apply full rewrites are unstable. They drift, regress, and fight version control. Agents that operate on diffs compose well with tools, tests, and human review.

This is why production coding agents almost always operate in **patch mode**, not generation mode.

---

Core mental model to lock in:

Coding models default to **creation**.
Engineering work requires **preservation + change**.

Your prompt decides which mode the model enters.

---

One-line takeaway:

**If you don’t constrain the output shape, the model will choose the largest one.**

---