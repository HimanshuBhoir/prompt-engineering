## **Attention & Context Window**

---

## 1️⃣ Core Mental Model

An LLM does **not read everything equally**.

It uses **attention** to decide:

> *Which previous tokens matter right now?*

The **context window** is:

> The maximum number of tokens the model can *see* at once

Think of it as **working memory**, not long-term memory.

---

## 2️⃣ What “attention” actually means (simplified but accurate)

For every new token, the model:

* looks back at **all previous tokens**
* assigns each a **weight** (importance)
* uses weighted information to predict the next token

This is why:

* Some instructions are followed
* Some are ignored
* Some fade over time

---

## 3️⃣ Why context window size matters

If your prompt + conversation exceeds the window:

### What happens?

* Old tokens fall off
* Instructions disappear
* Behavior changes suddenly

This is **not a bug**.
The model literally **cannot see them anymore**.

---

## 4️⃣ The dangerous misconception

❌ “The model remembers everything in the chat”
❌ “System prompts always apply”

✅ Reality:

* Only tokens **inside the current context window** exist
* Everything else is gone

This is why:

* Long chats degrade
* Agents drift
* Rules get violated over time

---

## 5️⃣ Attention is **not equal** across tokens

Important truth:

> Tokens are not attended to equally, even if they’re still in context

Strong signals get more attention:

* Role definitions
* Structured sections
* Repeated constraints
* Explicit keywords

Weak signals:

* Long paragraphs
* Buried rules
* One-time instructions

---

## 6️⃣ Placement is a control lever

### Weak

> (Long explanation...)
> By the way, never change variable names.

### Strong

> **Critical rule:** Never change variable names.

Why?

* “Critical rule” primes attention
* Bold / headers increase salience
* Earlier placement = higher influence

---

## 7️⃣ Context competition (very important)

All tokens compete for attention.

If you add:

* too much code
* too much chat history
* unnecessary examples

Then:

* instructions lose weight
* output quality drops

This is why:

> **More context can make results worse**

---

## 8️⃣ Production-grade mental model

Think like this:

```
Context Window = Fixed budget
Attention = How you spend that budget
```

You must decide:

* What deserves attention
* What must be compressed or removed

---

## 9️⃣ Practical rules you’ll use later

1. Put rules before data
2. Put critical constraints at the top
3. Repeat important rules in short form
4. Structure beats verbosity
5. Never waste context tokens

---

## 🔟 Micro-exercise (5 minutes)

Given this situation:

> A 20-turn coding conversation where the model starts renaming variables even though you told it not to.

Answer **only this**:

* Why is this happening **in terms of attention & context window**?

Don’t fix it yet. Just explain the cause.

---

* Answer

> The instruction lost influence either because it fell out of the context window or because attention was dominated by large, high-signal tokens like code.
