Continuing.

## **Prompting Coding Models — Why models over-refactor (and why this is predictable)**

Models over-refactor because **refactoring is statistically rewarded** in their training data.

In public code, tutorials, blog posts, and reviews, “good code” usually appears after:

* renaming variables
* extracting helpers
* reorganizing logic
* improving style

So when the model detects *any* code task without hard constraints, it assumes the goal is **code improvement**, not **behavior preservation**.

From the model’s perspective, refactoring is the *safe* move.

---

There’s a deeper reason.

Models do not have a native concept of:
“this code is already correct, only change one thing.”

They only see text.
And text is always editable unless restricted.

Humans treat existing code as mostly immutable.
Models treat all visible tokens as equally mutable.

---

This is why phrases like:
“fix”
“improve”
“clean up”
“optimize”

are dangerous in coding prompts.

In training data, those words almost always precede refactors. You are accidentally activating a refactor-heavy probability path.

---

Another critical factor: **models optimize locally, not historically**.

They do not feel the cost of change:

* breaking downstream code
* invalidating tests
* increasing review complexity

A refactor that *looks* cleaner in isolation is statistically preferable, even if it’s harmful in a real codebase.

---

Short example.

Prompt:
“Fix the bug and improve readability.”

To a human: fix bug, minimal style tweak.
To a model: bug fix + naming changes + structure cleanup.

Not malice. Pattern completion.

---

The core insight to lock in:

Over-refactoring is not a mistake.
It is the **default behavior** when mutability is unconstrained.

If you don’t define what must remain stable, the model assumes nothing is sacred.

---

One-line takeaway:

**Models refactor because they don’t feel the cost of change — only the reward of plausibility.**

---