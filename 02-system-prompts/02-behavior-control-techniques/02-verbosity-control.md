# Oververbosity Control

**Core problem:** LLMs are trained on the internet, where longer = more helpful. This makes them **chronically verbose**.

Your job: **Make them concise without losing quality.**

---

## **Why Models Are Verbose**

1. **Training data bias**: Blog posts, tutorials, and articles pad content
2. **Hedging behavior**: "I think", "It's important to note", "Generally speaking"
3. **Over-explaining**: Assumes user knows nothing
4. **Filler phrases**: "Thank you for", "I'd be happy to", "Let me help you with that"

**Result:** 200 words when 20 would do.

---

## **The Core Techniques**

### **1. Set Hard Limits**

```
WEAK:
"Keep responses concise."

STRONG:
"Maximum 3 sentences per response."

STRONGER:
"Respond in exactly 50 words or less."

STRONGEST:
"Format: [Answer]. [One supporting fact]. [Next step]."
```

**Why this works:** Removes ambiguity. "Concise" is subjective. "3 sentences" is not.

---

### **2. Ban Filler Phrases**

```
SYSTEM PROMPT:
Never use these phrases:
- "Thank you for..."
- "I'd be happy to..."
- "It's important to note that..."
- "Generally speaking..."
- "In order to..."
- "Let me help you with that..."

Start responses with the actual answer.
```

**Example:**

❌ **With filler:**
```
"Thank you for your question! I'd be happy to help you understand 
variables. Generally speaking, a variable is a container that stores 
data. It's important to note that variables have names and values..."
```

✅ **Without filler:**
```
"A variable stores data with a name. Example: x = 5 stores the value 5 
in variable x."
```

---

### **3. Frontload the Answer**

```
SYSTEM PROMPT:
Always structure responses as:
1. Direct answer (first sentence)
2. One supporting detail (if needed)
3. Next action (if needed)

Never explain context before answering.
```

**Example:**

❌ **Context-first:**
```
"To understand this, we need to first talk about how databases work. 
Databases store information in tables, and tables have rows and columns. 
When you want to find something, you use a query. So to answer your 
question, yes, SQL is a query language."
```

✅ **Answer-first:**
```
"Yes, SQL is a query language. It retrieves data from database tables."
```

---

### **4. Remove Hedging**

```
SYSTEM PROMPT:
Never hedge with phrases like:
- "I think..."
- "It seems..."
- "Probably..."
- "In my opinion..."

State facts directly. If uncertain, say: "I don't have enough information."
```

**Example:**

❌ **Hedged:**
```
"I think Python is probably a good choice for beginners because it seems 
to have simpler syntax compared to other languages in my opinion."
```

✅ **Direct:**
```
"Python is good for beginners. Its syntax is simpler than Java or C++."
```

---

### **5. The Word Budget**

```
SYSTEM PROMPT:
Response budget: 30 tokens maximum.
Every word must earn its place.
If you need more words, you're over-explaining.
```

**Mental model:** Every token costs money. Treat them like scarce resources.

---

## **Advanced: Context-Aware Verbosity**

Not all situations need the same level of brevity.

```
SYSTEM PROMPT:
Verbosity rules:
- Explaining concepts → 2-3 sentences maximum
- Answering yes/no → 1 sentence
- Providing instructions → Numbered list, no elaboration
- Code explanations → Inline comments only

If user says "explain more" → Add ONE supporting example, no more.
```

---

## **The Anti-Pattern: Over-Compression**

### ❌ Too terse (loses helpfulness):
```
"Yes."
"No."
"Maybe."
```

### ✅ Balanced:
```
"Yes, React uses virtual DOM. This makes updates faster."
```

**The line:** Concise ≠ cryptic. Answer must still be **complete and useful**.

---

## **Real-World Example**

### **Task:** AI code reviewer

❌ **Verbose version:**
```
Thank you for submitting this pull request! I'd be happy to review it 
for you. After carefully examining your code, I've noticed that there 
are a few areas that could use some improvement. First of all, it's 
important to note that your function doesn't have any error handling, 
which could potentially cause issues in production...
```
(87 words)

✅ **Controlled version:**
```
Issues found:
1. No error handling in parseData()
2. Variable 'temp' is unused (line 45)
3. Missing JSDoc on exportResults()

Fix these before approval.
```
(23 words)

---

## **The Formula**

```
Oververbosity Control = 
  Hard Limits + Banned Phrases + Answer-First Structure + No Hedging
```

---

## **Testing Your Control**

Ask the model a simple question:  
**"What is a variable?"**

Good response length: **15-25 words**  
If it's 50+ words → your control is weak

---

## **Control Template**

```
VERBOSITY RULES:
- Maximum: [X words/sentences/tokens]
- Never use: [list banned phrases]
- Structure: [answer] → [one detail] → [action]
- No hedging words
- No filler phrases

Test: "What is X?" should be answered in under 20 words.
```

---