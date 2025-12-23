# Knowledge Cutoff Handling

**Core problem:** LLMs have a training cutoff date. They don't know what happened after. Without handling this, they either **hallucinate recent events** or **refuse to answer** anything time-sensitive.

Your job: **Make the AI honest about what it knows and doesn't know.**

---

## **The Three Failure Modes**

### **1. Confident Hallucination**
```
User: "Who won the 2024 Olympics basketball gold?"
AI: "Team USA won the gold medal in Paris 2024."
```
*(Could be right, could be wrong — model is guessing based on patterns)*

### **2. Paranoid Refusal**
```
User: "What's the capital of France?"
AI: "I don't have information after my cutoff date, so I can't answer."
```
*(This is timeless knowledge, refusal is wrong)*

### **3. Outdated Confidence**
```
User: "Is Elon Musk still CEO of Twitter?"
AI: "Yes, Elon Musk is CEO of Twitter."
```
*(True at training time, might be false now)*

---

## **The Solution Framework**

Teach the model to **differentiate between**:
1. **Timeless knowledge** (safe to answer)
2. **Time-sensitive facts** (needs verification)
3. **Unknown unknowns** (post-cutoff events)

---

## **Pattern 1: Explicit Cutoff Declaration**

```
SYSTEM PROMPT:
Your knowledge cutoff is January 2025.

For questions about events AFTER January 2025:
You say: "I don't have information past January 2025. This may have 
changed since then."

For timeless knowledge (math, history, established science):
Answer normally without mentioning cutoff.
```

**Why this works:** Sets clear boundary without paranoia.

---

## **Pattern 2: Time-Sensitivity Detection**

```
SYSTEM PROMPT:
Timeless topics (answer confidently):
- Historical events before 2025
- Scientific principles
- Programming concepts
- Geography/capitals
- Mathematical facts

Time-sensitive topics (caveat required):
- Current job titles ("CEO of X")
- Product versions ("latest version of Y")
- News events
- Policy/law status
- Living person's status

For time-sensitive topics, add: "As of January 2025, [answer]. 
This may have changed."
```

---

## **Pattern 3: Uncertainty Indicators**

```
SYSTEM PROMPT:
When answering time-sensitive questions:

Certain (before cutoff):
"As of January 2025, X was true."

Uncertain (near cutoff):
"In late 2024, X was true. This may have changed."

Unknown (after cutoff):
"I don't have information past January 2025 for this."

Never guess about post-cutoff events.
```

**Example:**
```
User: "Is Biden still president?"
AI: "As of January 2025, Joe Biden was president. Given the recent 
election cycle, this may have changed."
```

---

## **Pattern 4: Redirect to Search/Tools**

```
SYSTEM PROMPT:
For questions about:
- Events after January 2025
- Current status of positions/roles
- Latest product versions
- Recent news

You say: "I can search for current information. Should I look this up?"

Never fabricate post-cutoff information.
```

**Why this works:** Turns limitation into capability.

---

## **Real-World Examples**

### **Example 1: Tech Support AI**

```
SYSTEM PROMPT:
Your training data includes product docs through January 2025.

For version questions:
- Version 3.2 and earlier → Answer directly
- Version 3.3 and later → "I have information up to v3.2. For later 
  versions, check the changelog at [URL]"

Never guess about features in versions you weren't trained on.
```

---

### **Example 2: News Analysis AI**

```
SYSTEM PROMPT:
Knowledge cutoff: January 2025

For news queries:
- Before Jan 2025 → Provide information
- After Jan 2025 → "I don't have information past January 2025. 
  I can search recent news if you'd like."

Never speculate about recent events.
```

---

### **Example 3: Educational AI**

```
SYSTEM PROMPT:
Scientific knowledge cutoff: January 2025

For established science (physics, chemistry, math):
Answer without mentioning cutoff.

For cutting-edge research:
"As of January 2025, the research showed [X]. New studies may have 
emerged since."

Never present training-time research as current consensus.
```

---

## **The Honesty Spectrum**

```
┌─────────────────────────────────────────────────────────┐
│  TIMELESS ────────── DATED ────────── POST-CUTOFF      │
│  (answer)         (caveat)          (admit unknown)     │
└─────────────────────────────────────────────────────────┘

"What's 2+2?"     "Who was CEO      "Who won the 2026
                   in Jan 2025?"      election?"
   
Answer: 4         Answer with       "I don't have
                  "As of Jan 2025"  information past
                                    January 2025"
```

---

## **Advanced: Probabilistic Confidence**

For edge cases where model might know:

```
SYSTEM PROMPT:
If unsure whether information is pre or post-cutoff:

High confidence pre-cutoff:
"[Answer] (as of January 2025)"

Low confidence:
"I'm uncertain if this is current. As of my last update in January 
2025, [answer]."

No knowledge:
"I don't have information on this."
```

---

## **Common Mistakes**

### ❌ **Mistake 1: Always mentioning cutoff**
```
User: "What's the capital of France?"
AI: "My knowledge cutoff is January 2025, but Paris is the capital 
of France."
```
*(Unnecessary — capitals don't change often)*

### ❌ **Mistake 2: Guessing with confidence**
```
User: "Is X still CEO of Y?"
AI: "Yes, X is still CEO."
```
*(Post-cutoff role — should caveat or admit unknown)*

### ❌ **Mistake 3: Paranoid refusal**
```
User: "How does photosynthesis work?"
AI: "I can't provide information after January 2025."
```
*(Timeless science — should answer)*

---

## **The Test Questions**

Good cutoff handling passes these:

1. **Timeless:**  
   "What's Newton's first law?"  
   ✅ Answer directly, no cutoff mention

2. **Dated but known:**  
   "Who was US president in 2024?"  
   ✅ "Joe Biden was president in 2024"

3. **Potentially changed:**  
   "Is X still CEO of Y?"  
   ✅ "As of January 2025, X was CEO. This may have changed."

4. **Post-cutoff:**  
   "What happened in March 2025?"  
   ✅ "I don't have information past January 2025"

---

## **The Template**

```
KNOWLEDGE CUTOFF: [Date]

HANDLING RULES:

Timeless knowledge:
- [List of categories]
- Answer directly, no cutoff mention

Time-sensitive information:
- [List of categories]
- Format: "As of [cutoff date], [answer]. This may have changed."

Post-cutoff queries:
- Response: "I don't have information past [cutoff date]."
- Offer: "I can search for current information if needed."

NEVER:
- Guess about post-cutoff events
- Mention cutoff for timeless questions
- Present training data as current fact for time-sensitive topics
```

---

## **Key Insight**

**The goal isn't to hide the cutoff.**  
The goal is to be **selectively honest** — acknowledging limitations without being useless.

Good cutoff handling makes the AI:
- **Trustworthy** (doesn't hallucinate)
- **Useful** (doesn't refuse everything)
- **Clear** (user knows what to trust)

---