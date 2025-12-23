# PHASE 5: EXPERT PATTERNS & AUTHORITY

## Context Provision (When: Task needs background)

Context Provision is about giving the LLM the right amount of examples to understand what you want. This is also called **In-Context Learning (ICL)** - the model learns from examples you provide in the prompt itself, without any training or fine-tuning.

Think of it like teaching someone a new task. Sometimes you can just describe it. Sometimes you need to show one example. Sometimes you need multiple examples to convey the pattern.

### The Core Principle

LLMs learn patterns. When you provide examples, you're showing the model the exact pattern you want it to follow. The model recognizes: format, style, tone, structure, logic, and output format from your examples.

**The Rule:** Start minimal. Add examples only when quality suffers.

### Zero-Shot Prompting

**Definition:** Give the model a task with NO examples. Rely entirely on the model's pre-trained knowledge.

**When to use:**
- Simple, straightforward tasks
- General knowledge questions
- Tasks the model was explicitly trained for (translation, summarization, sentiment analysis)
- Exploratory queries where you don't yet know the exact format you want
- When you have limited prompt space

**Example:**
```
Classify the sentiment of this review: "The movie was fantastic and I would watch it again!"
```

Model output: Positive

No examples needed. The model already understands "sentiment classification" from training.

**Another example:**
```
Translate to Spanish: "I am learning how to code."
```

Model output: Estoy aprendiendo a programar.

Again, no examples needed. Translation is a core capability.

**Strengths:**
- Fast - no time spent crafting examples
- Token-efficient - shortest prompt possible
- Works well for common tasks
- Good for exploration and prototyping

**Limitations:**
- Ambiguous for nuanced tasks
- Model might not understand your specific format requirements
- Can be inconsistent on edge cases
- Doesn't work for novel or domain-specific patterns

**Real-world scenario:**
You're building a customer support classifier. Zero-shot works fine for broad categories:

```
Classify this support ticket into: Technical Issue, Billing Question, or Feature Request

Ticket: "I can't log into my account, keep getting error 403"
```

Output: Technical Issue

This works because the categories are self-explanatory.

### Few-Shot Prompting

**Definition:** Provide 2-5 examples of input-output pairs before asking the model to complete your task.

**When to use:**
- You need a specific format or structure
- The task is slightly ambiguous from description alone
- You want consistent styling across outputs
- The pattern isn't obvious from just instructions
- Zero-shot produces incorrect or inconsistent results

**The magic number:** Research shows 3-5 examples work best for most tasks. More than that often provides diminishing returns unless the task is very complex.

**Example - Custom format extraction:**
```
Extract key information from job postings in this format:

INPUT: Software Engineer at TechCorp. 5+ years Python required. $90K-$120K. Remote. Apply by June 30.
OUTPUT:
Position: Software Engineer
Company: TechCorp
Experience: 5+ years
Skills: Python
Salary: $90,000-$120,000
Work Type: Remote
Deadline: June 30

INPUT: Marketing Manager for GlobalBrand. MBA preferred. 3-5 years in consumer goods. $75K-$95K. Hybrid. Close July 15.
OUTPUT:
Position: Marketing Manager
Company: GlobalBrand
Experience: 3-5 years
Education: MBA preferred
Industry: Consumer goods
Salary: $75,000-$95,000
Work Type: Hybrid
Deadline: July 15

INPUT: Data Scientist at AI Innovations. PhD required. 2+ years minimum. €60K-€80K. On-site Berlin. Apply by August 31.
OUTPUT:
```

The model now understands:
- The exact fields to extract
- The formatting (bullet list with colons)
- How to normalize salary formats
- How to handle variations in the input

**Example - Maintaining tone and style:**
```
Rewrite technical jargon into simple language for customers:

Technical: "The API endpoint experienced a 503 service unavailable error due to downstream dependency timeout"
Simple: "Our system couldn't connect to an external service it needs, so your request couldn't be completed"

Technical: "Your authentication token has expired. Please re-initialize the OAuth flow"
Simple: "Your login session expired. Please log in again"

Technical: "The database transaction was rolled back due to a foreign key constraint violation"
Simple: "We couldn't save your changes because they conflict with existing data in our system"

Technical: "Memory allocation failed. The process exceeded its heap size limit"
Simple:
```

The model learns: avoid jargon, be concrete, explain the impact to the user.

**Critical insight from research:** The format matters more than the accuracy of individual examples. Even with random labels, as long as the structure is consistent, the model performs significantly better than zero-shot.

**Example selection tips:**
1. **Diverse:** Cover different variations of your input (short/long, simple/complex, different edge cases)
2. **Representative:** Choose typical examples, not outliers
3. **Correct:** While format matters most, wrong answers can mislead
4. **Balanced:** If doing classification, include examples from each category

**Strengths:**
- Dramatic improvement over zero-shot for specific formats
- Reduces ambiguity - the model sees exactly what you want
- Handles domain-specific patterns
- More consistent outputs

**Limitations:**
- Uses more tokens (costs more, slower)
- Requires manual effort to create good examples
- Still struggles with complex multi-step reasoning
- Limited by what patterns can be shown in 3-5 examples

**Real-world scenario:**
You're building a sentiment analyzer for product reviews, but you want specific categories:

```
Classify product reviews into: Enthusiastic, Satisfied, Neutral, Disappointed, or Angry

Review: "This is the best purchase I've made all year! Absolutely love everything about it!"
Sentiment: Enthusiastic

Review: "It does what it's supposed to do. No complaints, but nothing special either"
Sentiment: Neutral

Review: "Works fine for basic use. Wish it had more features but it's decent for the price"
Sentiment: Satisfied

Review: "Completely stopped working after 2 weeks. Total waste of money"
Sentiment: Angry

Review: "Expected more based on reviews. It's okay but probably not worth the cost"
Sentiment: Disappointed

Review: "Arrived quickly and works perfectly! My kids use it every day"
Sentiment:
```

Without these examples, the model might use "positive/negative/neutral" or struggle to distinguish "Satisfied" from "Enthusiastic".

### Many-Shot Prompting

**Definition:** Provide dozens, hundreds, or even thousands of examples. This leverages models with large context windows (100K+ tokens).

**When to use:**
- Complex reasoning tasks that few-shot can't handle
- Learning high-dimensional patterns
- Overriding model's pretraining biases
- Domain-specific tasks with lots of variation
- When you have abundant example data
- Tasks requiring nuanced understanding

**The breakthrough:** Recent research from Google's 2024 paper showed that many-shot ICL (hundreds of examples) can match or exceed fine-tuning performance on certain tasks.

**How many is "many"?** Studies tested prompts with 100-1000 examples (up to 85K tokens). Performance continues improving with more examples, unlike few-shot which plateaus.

**Example - Machine translation with rare language pairs:**
Google's research showed that 997-shot prompting for Kurdish-English translation outperformed previous state-of-the-art systems. The model learned translation patterns from hundreds of example sentence pairs.

**Example - Complex mathematical reasoning:**
```
[100 examples of step-by-step math problem solving, each showing the full reasoning process]

Problem 101: A train travels 240 miles in 3 hours...
```

With many examples, the model learns not just the format but the actual reasoning strategies.

**Variants of Many-Shot:**

**1. Reinforced ICL:**
Instead of using human-written examples, use model-generated rationales. Generate answers with chain-of-thought, filter for correct ones, use those as examples. This works when you have correct answers but not detailed explanations.

**2. Unsupervised ICL:**
Provide many questions without answers. The model learns the domain and style from questions alone. Surprisingly effective for complex reasoning.

**Strengths:**
- Can override pretraining biases that few-shot cannot
- Learns high-dimensional patterns and complex relationships
- Performance comparable to fine-tuning without training
- Handles substantial task variation
- Works for long-context tasks (document analysis, codebase understanding)

**Limitations:**
- Requires large context window models (Claude 3+, GPT-4, Gemini 1.5)
- Expensive - more tokens = higher cost
- Slower inference due to processing many examples
- Requires access to large example datasets
- Diminishing returns after a certain point
- Order of examples can affect performance

**Real-world scenario:**
You're building a medical diagnosis assistant. The domain is complex, with subtle variations:

```
[300 examples of symptom descriptions → diagnosis reasoning]

Patient 301: "I've had a persistent cough for 3 weeks, night sweats, and unexplained weight loss of 10 pounds..."
```

Many-shot lets the model learn medical reasoning patterns that few-shot couldn't capture. The model sees how different symptom combinations point to different conditions, learns medical terminology in context, and understands the reasoning process.

**Production consideration:**
Many-shot is best for:
- Batch processing where latency is acceptable
- High-value tasks where accuracy justifies cost
- Scenarios where fine-tuning isn't feasible (proprietary data, rapidly changing patterns)

### Pattern Recognition: The Decision Tree

Here's how to choose:

```
START: Need to complete a task

Is the task standard/common?
├─ YES: Try ZERO-SHOT first
│   ├─ Works well? → DONE
│   └─ Quality issues? → Proceed to FEW-SHOT
│
└─ NO: Is it a custom format or nuanced task?
    ├─ YES: Use FEW-SHOT (3-5 examples)
    │   ├─ Works well? → DONE
    │   └─ Still struggling? → Check reasoning complexity
    │       ├─ Simple task, just inconsistent? → Add more diverse few-shot examples
    │       └─ Complex reasoning needed? → Consider MANY-SHOT or Chain-of-Thought
    │
    └─ NO: Is it extremely complex with lots of variation?
        └─ YES: Use MANY-SHOT (50-1000 examples)
            ├─ Works? → DONE
            └─ Still fails? → Fine-tuning might be needed
```

**Quick reference:**

| Task Type | Start With | Upgrade If... |
|-----------|-----------|---------------|
| Translation, summarization, general Q&A | Zero-shot | Need specific style |
| Custom formatting, specific tone | Few-shot (3-5) | High variability |
| Classification with clear categories | Zero-shot | Categories are nuanced |
| Domain-specific extraction | Few-shot (3-5) | Many edge cases |
| Complex reasoning, rare domains | Many-shot (50+) | Few-shot insufficient |
| Novel patterns unseen in training | Few-shot (3-5) | Needs more pattern exposure |

### Practical Implementation Tips

**1. Start cheap, scale up:**
```
Iteration 1: Zero-shot (0 examples)
Cost: $X, Quality: 70%

Iteration 2: Few-shot (3 examples)
Cost: $2X, Quality: 88%

Iteration 3: Few-shot (5 examples)
Cost: $2.5X, Quality: 90%

Iteration 4: Many-shot (50 examples)
Cost: $15X, Quality: 94%
```

Stop when quality meets requirements. Don't over-optimize.

**2. Test example selection:**
Not all examples are equal. If your few-shot prompt isn't working:
- Try different examples (more diverse, more representative)
- Check if examples cover the actual variation you're seeing
- Verify examples are correct

**3. Format consistency is critical:**
```
BAD (inconsistent format):
Input: "This is great" → Output: Positive
"This is terrible" → Negative sentiment
Input: "It's okay" Output is: Neutral

GOOD (consistent format):
Input: "This is great"
Output: Positive

Input: "This is terrible"
Output: Negative

Input: "It's okay"
Output: Neutral
```

The model needs clear, repeating structure.

**4. For many-shot, consider prompt caching:**
With Claude's prompt caching, the first 50K+ tokens can be cached. Structure your many-shot prompt so examples are in the cacheable prefix. This reduces cost on subsequent calls.

### Common Mistakes

**Mistake 1: Jumping to few-shot too quickly**
Always try zero-shot first. You might be surprised how well it works.

**Mistake 2: Too few diverse examples**
Three examples of similar inputs don't help. Show variation.

**Mistake 3: Ignoring example quality**
One wrong example can derail the whole pattern. Verify examples are correct.

**Mistake 4: Not structuring for the context window**
If using many-shot, verify your examples fit within the model's context limit.

**Mistake 5: Using many-shot for simple tasks**
Don't waste tokens and money. Many-shot is for genuinely complex tasks.

### Advanced: Combining with Other Techniques

Context provision is often the first step. You'll often combine it with other patterns:

```
Few-shot + Chain-of-thought:
Show examples with reasoning steps

Few-shot + XML structure:
Use examples to teach XML formatting

Many-shot + Reinforced learning:
Generate examples, filter good ones, use as many-shot prompt
```

We'll cover these combinations in the next sections.

---

**Summary:**
- **Zero-shot:** No examples, rely on model knowledge. Start here.
- **Few-shot:** 2-5 examples to show format and pattern. Use for custom tasks.
- **Many-shot:** 50-1000+ examples for complex patterns. Use when few-shot insufficient.
- **Decision rule:** Start minimal, add examples only when needed.

Context provision is your first tool in the prompt engineering toolkit. Master when to use each level, and you'll solve 80% of prompting challenges.