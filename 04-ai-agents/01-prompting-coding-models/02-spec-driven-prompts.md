Continuing.

## **Prompting Coding Models — Spec-driven vs intent-driven prompts**

When you prompt a coding model with *intent*, you rely on interpretation.
When you prompt with a *spec*, you eliminate interpretation.

Coding models fail under intent-driven prompts because intent is **underspecified** in text. Phrases like “fix”, “improve”, “optimize”, or “clean up” have no stable meaning at the token level. In training data, each of those words maps to dozens of different solution patterns. The model must choose one arbitrarily.

A spec collapses that ambiguity.

A spec answers, explicitly and mechanically:

* what exists
* what must change
* what must not change
* how success is judged

Without this, the model invents its own spec — and that spec almost never matches yours.

---

Here’s the critical distinction.

Intent-driven prompt:
“Fix the bug in this function.”

Spec-driven prompt:
“The function throws on empty input. Modify only the return logic so empty input returns null. Do not change function signature or variable names.”

The second prompt works not because it’s longer, but because it **narrows the probability space**. There are now very few valid continuations. The model is forced into the correct path.

---

Why this matters for agents.

Agents operate in loops. If the first step is intent-driven, the agent’s internal plan is already wrong. Every subsequent tool call compounds that error. Spec-driven prompts create stable plans because they remove degrees of freedom early.

This is why production coding agents feel “boring” when they work well. Boring means constrained. Constrained means correct.

---

A key mental shift:

Humans think in goals.
Coding models execute best under **contracts**.

If you don’t give a contract, the model writes one for you.

---

Short example.

Intent-driven:
“Refactor this to be better.”

Spec-driven:
“Extract duplicate logic into one helper. Do not introduce new abstractions. Keep behavior identical.”

Same task. Completely different outcome reliability.

---

One-line takeaway:

**Intent invites creativity; specs enforce correctness.**

That’s why professionals always prompt coding models with specs, never wishes.

---