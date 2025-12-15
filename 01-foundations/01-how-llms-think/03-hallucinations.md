## **Why models hallucinate**

No prevention techniques yet.
Only *why it happens*.

---

## 1️⃣ First, kill the wrong assumption

Hallucination is **not**:

* a bug
* the model lying
* the model being lazy

Hallucination is a **natural outcome** of how LLMs work.

---

## 2️⃣ The core cause (very important)

> **LLMs are forced to always predict a next token**

They are **never allowed to say nothing**.

So when:

* information is missing
* context is weak
* question is ambiguous

The model still must output **something that looks statistically correct**.

That “something” can be wrong.

---

## 3️⃣ Hallucination = confident guessing

The model optimizes for:

> **Plausibility, not truth**

If a sequence *sounds right* based on training data, it will generate it—even if it’s false.

There is **no internal fact-checking step**.

---

## 4️⃣ Three primary hallucination triggers

### 1. **Missing information**

You ask:

> *What did this function return?*
> (without providing the function)

The model fills the gap.

---

### 2. **Ambiguous prompts**

You ask:

> *Explain the issue in the system*

Which system?
Which issue?

The model picks **one plausible interpretation** and commits.

---

### 3. **Overly broad questions**

You ask:

> *Give all edge cases*

“All” creates an **impossible space**.
The model invents reasonable-sounding items until it stops.

---

## 5️⃣ Why hallucinations sound confident

Important insight:

> The model has **no uncertainty signal by default**

It doesn’t “know that it doesn’t know”.

Unless prompted otherwise, it:

* answers fluently
* uses authoritative tone
* fills gaps seamlessly

Confidence is just a **language pattern**, not belief.

---

## 6️⃣ Training data effect (subtle but real)

During training:

* Confident answers were rewarded
* Hedging was rare
* Clear completions were preferred

So the model learned:

> *Finish the answer cleanly, even if you must invent details*

---

## 7️⃣ Why asking “are you sure?” doesn’t fix it

Because:

* The model just predicts tokens for “being sure”
* It doesn’t re-evaluate facts
* It may double down or flip randomly

You are not triggering verification — just a new completion.

---

## 8️⃣ Critical mental model

Memorize this:

> **Hallucination happens when probability ≠ reality**

The model follows probability.
Reality is external.

---

## 9️⃣ What hallucination is NOT caused by

❌ Temperature alone
❌ “Creativity”
❌ Model attitude
❌ Lack of intelligence

Even low-temperature models hallucinate if forced.

---

## 🔟 Micro-exercise (3–5 minutes)

Answer this **in one sentence**:

> Why does an LLM hallucinate *even when it sounds very confident*?

No solutions. Just the cause.

---