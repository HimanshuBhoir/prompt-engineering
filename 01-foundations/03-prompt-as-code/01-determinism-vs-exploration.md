# Determinism vs Exploration

This is about **controlling the degree of randomness** in LLM outputs.

---

## The Core Trade-off

**Determinism:** Same input → Same output (predictable, repeatable)

**Exploration:** Same input → Different outputs (creative, varied)

**You cannot maximize both simultaneously.**

---

## Why LLMs Are Probabilistic

Remember: LLMs predict the next token based on **probability distributions**.

For the prompt "The capital of France is", the model sees:

```
"Paris" → 95% probability
"paris" → 3% probability  
"Lyon" → 1% probability
"France" → 0.5% probability
```

**The model samples from this distribution.**

---

## Temperature: The Control Knob

**Temperature controls how the model samples:**

### Temperature = 0 (Maximum Determinism)
- Always picks the highest probability token
- Same input → (almost) always same output
- **Use for:** Tasks requiring consistency

### Temperature = 0.7 (Balanced)
- Samples from probability distribution with moderate randomness
- Same input → varied but reasonable outputs
- **Use for:** General tasks

### Temperature = 1.0+ (Maximum Exploration)
- Flattens probability distribution
- Even low-probability tokens get picked
- Same input → highly varied outputs
- **Use for:** Creative tasks, brainstorming

---

## Common Misconception

❌ **"Temperature = creativity"**

**Wrong.** Temperature = randomness, not creativity.

High temperature can produce:
- ✅ Novel combinations
- ❌ Nonsense
- ❌ Hallucinations
- ❌ Inconsistency

**Creativity comes from prompt structure, not temperature.**

---

## When to Use Determinism

**Use cases for temperature ≈ 0:**

1. **Data extraction**
   ```
   Extract: name, email, phone from this text
   ```
   → You want the same extraction every time

2. **Classification**
   ```
   Classify this support ticket: BUG | FEATURE | QUESTION
   ```
   → Category shouldn't change between runs

3. **Code generation (production)**
   ```
   Generate SQL query for: get all users created in last 7 days
   ```
   → Query should be identical each time

4. **Structured output**
   ```
   Convert this to JSON with fields: id, name, status
   ```
   → Format must be consistent

5. **Testing/Debugging**
   - Need reproducible outputs to isolate issues

**Key principle:** If output variance = bad user experience → use determinism

---

## When to Use Exploration

**Use cases for temperature ≈ 0.7-1.0:**

1. **Brainstorming**
   ```
   Generate 10 unique marketing taglines
   ```
   → You want variety

2. **Creative writing**
   ```
   Write a story about...
   ```
   → Repetition is boring

3. **Rephrasing**
   ```
   Rewrite this in 5 different ways
   ```
   → Need actual differences

4. **Ideation**
   ```
   Suggest alternative approaches to this problem
   ```
   → Want diverse options

**Key principle:** If output variance = feature → use exploration

---

## The Determinism Illusion

**Important:** Even at temperature = 0, LLMs are NOT 100% deterministic.

**Why?**
- Floating-point rounding differences
- Hardware variations
- Model implementation details

**In practice:**
- Temperature = 0 → ~95% same outputs
- Small variations still possible

**Never assume absolute determinism.**

---

## Prompt Design for Determinism

Beyond temperature, prompt structure affects consistency:

### ✅ Deterministic Prompt Patterns

**1. Explicit format constraints**
```
Output format (exactly):
{
  "name": string,
  "age": number,
  "status": "active" | "inactive"
}
```

**2. Enumerated options**
```
Classify as one of: SUCCESS, FAILURE, PENDING
(Use exact spelling)
```

**3. Step-by-step process**
```
1. Extract all email addresses
2. Validate format
3. Return as comma-separated list
4. If none found, return "NONE"
```

**4. Examples (few-shot)**
```
Input: "Call me at 555-1234"
Output: {"phone": "555-1234"}

Input: "Email: test@example.com"  
Output: {"email": "test@example.com"}

Input: [user's input]
Output:
```

---

### ❌ Non-Deterministic Prompt Patterns

**1. Vague instructions**
```
Summarize this nicely
```
→ "Nicely" is subjective

**2. Open-ended requests**
```
Tell me about this topic
```
→ No constraints on length, depth, focus

**3. Creative directives**
```
Write something interesting
```
→ "Interesting" is undefined

**4. Implicit expectations**
```
Analyze this code
```
→ What kind of analysis? How detailed?

---

## Hybrid Approach: Constrained Exploration

**Best of both worlds:**

```
TASK: Generate 5 product taglines

CONSTRAINTS (deterministic):
- Each exactly 6-8 words
- Must include the word "innovative"
- Format: numbered list
- No punctuation at end

CREATIVITY (exploratory):
- Use varied emotional tones
- Try different angles
- Experiment with wordplay

TEMPERATURE: 0.8
```

**Result:** Controlled variance within boundaries

---

## Production Pattern

**For production systems:**

```python
# Deterministic pipeline
extract_data(temp=0) 
  → validate(temp=0) 
  → classify(temp=0)

# Exploratory pipeline  
generate_ideas(temp=0.9) 
  → filter(temp=0) 
  → rank(temp=0)
```

**Pattern:** Exploration for generation, determinism for processing

---

## Debugging Determinism Issues

**If outputs are inconsistent when you need consistency:**

1. ✅ Set temperature = 0
2. ✅ Add explicit output format
3. ✅ Use enumerated options
4. ✅ Add validation step
5. ✅ Provide examples (few-shot)
6. ✅ Remove ambiguous language

**If outputs are too repetitive when you need variety:**

1. ✅ Increase temperature (0.7-1.0)
2. ✅ Ask for "N different" outputs
3. ✅ Add diversity constraints ("avoid repetition")
4. ✅ Use seed variations (if API supports)

---

## Key Takeaways

| Need | Temperature | Prompt Style |
|------|-------------|--------------|
| Extraction | 0 | Structured, explicit |
| Classification | 0 | Enumerated options |
| Code generation | 0-0.3 | Examples, format |
| Summarization | 0.3-0.5 | Guidelines, length |
| Brainstorming | 0.7-1.0 | Open-ended |
| Creative writing | 0.8-1.2 | Minimal constraints |

---

## The Mental Model

```
DETERMINISM ←→ EXPLORATION
(Consistency)   (Variety)

Control via:
├─ Temperature (sampling randomness)
├─ Constraints (structural boundaries)  
└─ Examples (pattern anchoring)
```

**Choose based on use case, not preference.**

---

**Bottom line:** 

Temperature controls randomness in token selection.

Prompt structure controls randomness in output behavior.

---