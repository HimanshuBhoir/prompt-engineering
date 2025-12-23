## **Prompting Coding Models — Why “write code” fails**

Nothing else.

---

When you say “write code”, you’re issuing a **goal**, not a **spec**.

Coding models don’t infer intent like engineers do. They predict the most statistically common completion for the phrase *“write code”*. In training data, that phrase most often leads to full-file rewrites, verbose explanations, generic best practices, and safe boilerplate. So that’s what you get.

The model is not thinking: *“What is the minimal correct implementation?”*
It is thinking: *“What usually follows this request in my data?”*

That distribution is broad and noisy.

---

The second reason it fails is **scope explosion**.

“Write code” does not define:

* what exists already
* what must not change
* how much code is acceptable
* whether correctness, readability, or speed is prioritized

So the model fills the gaps. Filling gaps is exactly how hallucination and overengineering begin in coding tasks.

This is why you see:

* renamed variables
* rewritten files
* unnecessary abstractions
* style changes you didn’t ask for

Not because the model is careless — because the prompt left the solution space wide open.

---

The third reason is **trajectory lock-in**.

Once the model starts emitting tokens that look like a full rewrite, it becomes very hard for it to stop. Early tokens like `import`, `function`, or file headers bias the rest of the output toward “complete solution” patterns.

By the time you realize it went wrong, the probability path is already set.

---

A subtle but critical reason: **models don’t know what not to touch unless you say it**.

Humans assume immutability by default.
Models assume mutability by default.

If you don’t explicitly constrain change, the model treats the entire codebase as editable text.

---

Short example.

Bad prompt:
“Write code to fix this bug.”

Likely output:
Full function rewrite with new helpers and renamed variables.

Better intent (not technique yet, just understanding):
The difference is not politeness or clarity — it’s whether the model knows the **allowed change surface**.

---

The core mental model to internalize:

Coding models are **completion engines, not diff engines by default**.

“Write code” activates completion behavior.
Engineering work usually requires **surgical modification behavior**.

If you don’t specify which behavior you want, you’ll always get the wrong one.

---

One-line takeaway you should remember:

**“Write code” fails because it describes an outcome, not a constraint space.**

---

Micro-exercise (don’t answer yet unless you want feedback):

In one sentence, explain why “fix this code” often leads to refactors instead of fixes.

---