## Advanced/Meta (When: System-level design)

Meta-prompting techniques are about prompts that create, optimize, or combine other prompts. This is system-level engineering—using AI to improve AI interactions.

### Meta Prompting

**Definition:** Meta prompting focuses on a structure-oriented approach rather than a content-driven approach, providing high-level frameworks that guide problem-solving without specific examples.

**When to use:**
- Teaching general problem-solving patterns
- When you want token efficiency over example richness
- For tasks where the model has innate knowledge
- Creating reusable prompt templates

**The key difference from few-shot:** Few-shot shows specific examples of input→output. Meta prompting shows the *structure* of how to approach a problem type.

**Example - Few-Shot vs Meta:**

**Few-Shot (content-driven):**
```
Example 1: 
Problem: "If x + 5 = 12, what is x?"
Solution: "Subtract 5 from both sides: x = 12 - 5 = 7"

Example 2:
Problem: "If 2y = 18, what is y?"
Solution: "Divide both sides by 2: y = 18/2 = 9"

Now solve: If 3z - 4 = 11, what is z?
```

**Meta Prompting (structure-driven):**
```
For algebraic equations, use this framework:

STRUCTURE:
1. Identify the variable to solve for
2. Identify operations performed on the variable
3. Apply inverse operations in reverse order
4. Simplify to isolate the variable

Now solve: If 3z - 4 = 11, what is z?
```

The meta prompt teaches the *method*, not just shows examples.

**Practical meta-prompting templates:**

**Template 1: Problem-solving structure**
```
When approaching [type of problem]:

FRAMEWORK:
1. UNDERSTAND: [What to identify first]
2. DECOMPOSE: [How to break it down]
3. SOLVE: [Step-by-step approach]
4. VERIFY: [How to check your answer]

Apply this framework to: [specific problem]
```

**Template 2: Writing structure**
```
For [type of writing], follow this structure:

OPENING: [Purpose and approach]
BODY: [How to organize main points]
CLOSING: [How to conclude]
TONE: [Guidelines for voice and style]

Now write: [specific request]
```

**Advantages:**
- Token efficiency - reduces the number of tokens required by focusing on structure rather than detailed content
- Provides fairer comparison between different models by minimizing influence of specific examples
- Can be viewed as a form of zero-shot prompting
- Teaches transferable patterns

**Limitations:**
- Assumes the LLM has innate knowledge about the specific task - performance might deteriorate with unique and novel tasks
- Less effective than few-shot for highly specific or unusual patterns
- Requires careful design of the meta-structure itself

**Applications:** Complex reasoning tasks, mathematical problem-solving, coding challenges, theoretical queries.

### Automatic Prompt Engineer (APE)

**Definition:** APE is a framework for automatic instruction generation and selection, treating the instruction as a "program" optimized by searching over a pool of instruction candidates to maximize a chosen score function.

**When to use:**
- You need optimal prompts but don't want to manually iterate
- Building production systems where prompt quality matters
- You have a dataset to evaluate prompt performance
- You want to discover prompts better than human-engineered ones

**How it works:**

APE automatically generates instructions for a task specified via output demonstrations, generates several instruction candidates (either via direct inference or recursive process), executes them using the target model, and selects the most appropriate instruction based on computed evaluation scores.

**The process:**
1. **Generate candidate prompts:** Use an LLM to generate many different ways to instruct the task
2. **Evaluate each prompt:** Run each prompt on a test set and score the outputs
3. **Select best prompt:** Choose the prompt with highest score
4. **Optional: Iterate:** Generate variations of the best prompts and re-evaluate

**Example:**

```
Task: Generate antonyms for words

Input examples:
- "happy" → "sad"
- "hot" → "cold"
- "big" → "small"

APE GENERATION STEP:
Generate 50 different instruction prompts for this task.

Generated candidates (examples):
1. "Provide the opposite meaning of the given word"
2. "What word means the reverse of [INPUT]"
3. "Give an antonym for the following word"
...
50. "Output a word with opposite meaning"

EVALUATION STEP:
Test each prompt on 100 word pairs.
Score based on correct antonyms produced.

SELECTION:
Prompt #23 ("Generate the antonym of this word:") scored highest at 94% accuracy.

RESULT:
Best prompt discovered: "Generate the antonym of this word:"
```

**Advanced: Iterative refinement**

Modern APE methods employ meta-prompts that instruct an LLM to emulate an expert prompt engineer, using structured templates with explicit diagnosis, reasoning chains, and refinement steps.

**Breakthrough finding:** APE discovered a better zero-shot CoT prompt than the human-engineered "Let's think step by step" - it found "Let's work this out in a step by step way to be sure we have the right answer" which improved performance on mathematical reasoning.

**Practical implementation:**
```python
# Simplified APE process

# Step 1: Generate candidate prompts
candidates = generate_prompts(task_description, examples, n=50)

# Step 2: Evaluate each
scores = []
for prompt in candidates:
    results = evaluate_prompt(prompt, test_set)
    scores.append(calculate_score(results))

# Step 3: Select best
best_prompt = candidates[argmax(scores)]

# Step 4: Optional iteration
variations = generate_variations(best_prompt, n=20)
# Repeat evaluation...
```

**Strengths:**
- In 19 out of 24 tasks, prompts generated by APE outperformed human-engineered prompts
- Discovers non-obvious phrasings that work better
- Systematic and reproducible
- Can optimize for different criteria (accuracy, truthfulness, informativeness)

**Limitations:**
- Requires a dataset for evaluation
- Computationally expensive (many LLM calls)
- May overfit to the evaluation set
- Still needs human judgment for final selection

**Use in production:** APE is particularly valuable when deploying LLM systems where small improvements in prompt quality translate to significant value (customer service, content generation, data extraction).

### Ensembling

**Definition:** Combine outputs from multiple models or multiple prompts to achieve better results than any single model/prompt alone. Like in computer vision where ensembling models achieves better accuracy, ensembling LLMs can break through the performance ceiling of the community.

**When to use:**
- Critical tasks where accuracy justifies extra cost
- When different models have complementary strengths
- To reduce variance in outputs
- For high-stakes decisions

**Three main approaches:**

**1. Prompt Ensembling:** Multiple prompts, one model

**Method: Majority Voting**
```
Prompt 1: "Classify sentiment: {text}"
Prompt 2: "Is this review positive or negative? {text}"
Prompt 3: "Rate sentiment (positive/negative): {text}"

Outputs: Positive, Positive, Negative
Majority vote: Positive
```

**Method: Ask Me Anything (AMA)**
AMA uses an LLM to generate multiple reformulations of the same question, analyzes responses, and calculates a weighted aggregation of results.

```
Original: "Does this animal live in North America?"

AMA reformulations:
1. "Is this species native to North America?"
2. "Can this animal be found in the North American continent?"
3. "Does the natural habitat of this creature include North America?"

Aggregate answers with weighting based on confidence.
```

**2. Model Ensembling:** Multiple models, same prompt

Large language models exhibit complementary strengths in various tasks, motivating ensemble approaches that fuse informative probability distributions at each decoding step.

**Simple approach: Output voting**
```
Send same prompt to GPT-4, Claude, and Llama

GPT-4: "Paris"
Claude: "Paris"
Llama: "Lyon"

Majority vote: "Paris"
```

**Advanced approach: Token-level ensembling**
Treat each generation step as a classification task, ensembling multiple models at the token level to achieve higher accuracy and prevent early errors from snowballing.

Instead of generating full responses then voting, ensemble at each token:
- Model A thinks next token is "The" (confidence: 0.8)
- Model B thinks next token is "This" (confidence: 0.6)
- Model C thinks next token is "The" (confidence: 0.7)
- Ensemble selects "The" (highest combined confidence)

**3. Hybrid Ensembling:** Multiple prompts AND multiple models

LLM-Synergy framework uses ensemble learning with two methods: Boosting-based weighted majority vote and Cluster-based Dynamic Model Selection, achieving superior performance across medical QA datasets.

**Implementation example:**
```
BOOSTING-BASED:
1. Run 3 different prompts on 3 different models (9 outputs total)
2. Assign weights to each model based on past performance
3. Weight votes accordingly
4. Select answer with highest weighted vote

DYNAMIC SELECTION:
1. Cluster queries by type
2. For each cluster, identify which models perform best
3. For new query, identify cluster
4. Use only the best-performing models for that cluster
```

**Practical considerations:**

**Cost vs. Benefit:**
- Single model call: $X, 85% accuracy
- 3-model ensemble: $3X, 92% accuracy
- 5-prompt + 3-model ensemble: $15X, 94% accuracy

Use ensembling when the accuracy gain justifies the cost multiplier.

**When to ensemble:**
- Medical diagnosis: YES (lives at stake)
- Customer service: MAYBE (depends on value)
- Creative writing: NO (subjective, expensive)
- Legal analysis: YES (consequences of errors high)
- Casual Q&A: NO (not worth the cost)

**Strengths:**
- Ensembling significantly outperforms single models on benchmarks involving subject examination, mathematics, reasoning, and knowledge QA
- Reduces impact of individual model failures
- Leverages complementary strengths
- More robust to adversarial inputs

**Limitations:**
- Expensive (N times the cost)
- Slower (N times the latency or requires parallel infrastructure)
- Diminishing returns after 3-5 models
- Requires good aggregation strategy