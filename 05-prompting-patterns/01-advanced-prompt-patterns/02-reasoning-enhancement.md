Perfect! Now I have comprehensive, up-to-date information on Reasoning Enhancement techniques. Let me create the teaching content.

---

## Reasoning Enhancement (When: Multi-step logic needed / Reasoning needed)

Reasoning Enhancement techniques teach the model to think step-by-step instead of jumping to conclusions. These are critical for tasks that require logic, math, planning, or complex analysis - anything where the answer isn't immediately obvious.

**The Core Problem:** LLMs are trained to predict the next token. For simple questions ("What's the capital of France?"), this works fine. For complex reasoning ("If John has 3 apples and buys 4 packs of 5 apples each, then gives away half, how many does he have?"), predicting directly often fails. The model needs to show its work.

### Chain-of-Thought (CoT) Prompting - Controlled

**Definition:** Explicitly prompt the model to break down its reasoning into intermediate steps before giving a final answer.

**When to use:**
- Mathematics and arithmetic
- Logic puzzles
- Multi-step planning
- Debugging and troubleshooting
- Any task where you need to verify the reasoning process
- When the answer requires multiple facts or operations

**How it works:** Instead of Input → Answer, you get Input → Reasoning Steps → Answer. This intermediate reasoning dramatically improves accuracy on complex tasks.

**The breakthrough:** Research by Wei et al. (2022) showed that CoT enables complex reasoning capabilities through intermediate reasoning steps.

**Basic example:**
```
WITHOUT CoT:
Q: Roger has 5 tennis balls. He buys 2 more cans of tennis balls. Each can has 3 tennis balls. How many tennis balls does he have now?
A: 11

Model gets it wrong by guessing.

WITH CoT:
Q: Roger has 5 tennis balls. He buys 2 more cans of tennis balls. Each can has 3 tennis balls. How many tennis balls does he have now?
A: Let's think step by step.
- Roger starts with 5 tennis balls
- He buys 2 cans
- Each can has 3 balls, so 2 cans = 2 × 3 = 6 balls
- Total = 5 + 6 = 11 balls
The answer is 11.

Model shows work and gets it right.
```

**Why it works:** Breaking down the problem forces the model to process each step correctly instead of trying to "jump" to the answer. Each step builds on the previous one.

### Zero-Shot CoT

**Definition:** Get chain-of-thought reasoning WITHOUT providing any examples. Just add a trigger phrase to make the model reason step-by-step.

**The magic phrase:** "Let's think step by step"

**When to use:**
- You don't have good examples to show
- You need quick reasoning without crafting examples
- The task is novel and you haven't seen similar problems
- You want to test if a problem is solvable before investing in few-shot examples

**How it works:** Simply append "Let's think step by step" to your question. The model automatically breaks down the reasoning.

**Example:**
```
Q: I went to the market and bought 10 apples. I gave 2 apples to the neighbor and 2 to the repairman. I then went and bought 5 more apples and ate 1. How many apples did I remain with?

Let's think step by step.
```

Model output:
```
First, you started with 10 apples.
You gave away 2 apples to the neighbor and 2 to the repairman, so you had 6 apples left.
Then you bought 5 more apples, so now you had 11 apples.
Finally, you ate 1 apple, so you would remain with 10 apples.
```

**Alternative trigger phrases:** Research tested variations like "Let's solve this problem by splitting it into steps" or "Let's think about this logically," but found "Let's think step by step" most effective.

**Interesting finding:** Some researchers found "Take a deep breath and work through this step by step" worked well for some Google models.

**The two-stage process:** Zero-Shot CoT technically involves two prompts - one to generate the reasoning, then a second to extract the final answer. But in practice, modern models like Claude can do both in one go.

**Strengths:**
- No examples needed
- Works across diverse reasoning tasks
- Dramatically improves performance - research showed increases from 17.7% to 78.7% accuracy on arithmetic tasks with large models
- Reveals the model's reasoning process for debugging

**Limitations:**
- Not as effective as Few-Shot CoT for very complex reasoning
- Answer extraction may be task-specific
- Can generate reasoning that looks correct but is actually flawed

**Practical tip:** Start with Zero-Shot CoT. If it works, you're done. If not, upgrade to Few-Shot CoT with examples.

### Few-Shot CoT

**Definition:** Provide 2-5 examples of problems WITH their step-by-step reasoning, then ask the model to solve a new problem using the same reasoning pattern.

**When to use:**
- Zero-Shot CoT isn't accurate enough
- You have specific reasoning patterns you want the model to follow
- The domain has specialized logic (medical diagnosis, legal reasoning, etc.)
- You need consistent reasoning structure across many queries

**How it works:** Show complete examples of: Question → Step-by-step reasoning → Answer. The model learns the reasoning pattern from your examples.

**Example:**
```
Q: There are 15 trees in the grove. Grove workers will plant trees in the grove today. After they are done, there will be 21 trees. How many trees did the grove workers plant today?
A: There are 15 trees originally. Then there were 21 trees after some more were planted. So there must have been 21 - 15 = 6 trees planted. The answer is 6.

Q: If there are 3 cars in the parking lot and 2 more cars arrive, how many cars are in the parking lot?
A: There are 3 cars in the parking lot already. 2 more arrive. Now there are 3 + 2 = 5 cars. The answer is 5.

Q: Leah had 32 chocolates and her sister had 42. If they ate 35, how many pieces do they have left in total?
A: Leah had 32 chocolates and Leah's sister had 42. That means there were originally 32 + 42 = 74 chocolates. After eating 35, they would have 74 - 35 = 39 chocolates left. The answer is 39.

Q: Jason had 20 lollipops. He gave Denny some lollipops. Now Jason has 12 lollipops. How many lollipops did Jason give to Denny?
A:
```

The model learns: (1) Break down what you know, (2) Identify the operation needed, (3) Perform the calculation, (4) State the answer clearly.

**Recent research update:** A 2025 study found that with modern strong models, Few-Shot CoT exemplars primarily help align output format with expectations rather than improving reasoning itself. This means the examples teach the model HOW to structure its answer more than HOW to reason.

**Implication:** For newer models like Claude Sonnet 4, GPT-4, etc., you might not need Few-Shot CoT for raw reasoning power. But you still use it to control the OUTPUT format (how detailed, what structure, etc.).

**Strengths:**
- More accurate than Zero-Shot CoT for complex problems
- Controls the reasoning style and format
- Works for domain-specific reasoning patterns
- Research shows automated methods for Few-Shot CoT can beat manual exemplar creation

**Limitations:**
- Requires manual effort to create good examples
- Examples need to cover diverse scenarios
- Uses more tokens than Zero-Shot CoT
- Performance gains only appeared with models around 100B+ parameters; smaller models produced coherent-looking but incorrect reasoning

### Tree of Thoughts (ToT)

**Definition:** Instead of following a single chain of reasoning, explore MULTIPLE reasoning paths simultaneously, evaluate them, and choose the best one. Think of it as reasoning with backtracking.

**When to use:**
- Problems that require exploration (like search problems)
- Tasks where there are multiple valid approaches
- Situations where you might hit dead ends and need to backtrack
- Complex planning requiring evaluation of alternatives
- Creative tasks with many possible solutions

**How it works:** At each reasoning step, the model:
1. Generates multiple possible "thoughts" (next steps)
2. Evaluates each thought (which looks most promising?)
3. Explores the best thoughts further (branch)
4. Can backtrack if a path hits a dead end
5. Continues until finding the best solution

Tree of Thoughts operates on hierarchical text generation, where a central topic branches into subtopics, allowing for lookahead and tree search strategies.

**Concrete example - Game of 24:**

Task: Using four numbers (4, 9, 10, 13) and basic operations (+, -, ×, ÷), make 24.

**Chain-of-Thought would try one path:**
```
Let's try: (13 - 9) × (10 - 4) = 4 × 6 = 24 ✓
```
If this path fails, CoT is stuck.

**Tree of Thoughts explores multiple paths:**
```
Step 1: Generate possible first operations
- Thought A: 13 - 9 = 4, leaving {4, 4, 10}
- Thought B: 13 - 4 = 9, leaving {9, 9, 10}
- Thought C: 10 + 4 = 14, leaving {9, 13, 14}

Evaluate: Thought A looks promising (small numbers)

Step 2: From Thought A {4, 4, 10}, generate next operations
- Thought A1: 4 × 4 = 16, leaving {10, 16}
- Thought A2: 10 - 4 = 6, leaving {4, 6}
- Thought A3: 10 × 4 = 40, leaving {4, 40}

Evaluate: Thought A2 {4, 6} looks good

Step 3: From Thought A2 {4, 6}
- Thought A2a: 4 × 6 = 24 ✓ SOLUTION FOUND
```

If A2 failed, ToT would backtrack to try A1 or A3, or even backtrack further to try Thought B or C.

**Implementation strategies:**

**1. Breadth-First Search (BFS):**
Explore all thoughts at current level before going deeper. Good for finding solutions quickly when you don't know which path is best.

**2. Depth-First Search (DFS):**
Follow one path all the way down before trying others. Good when you have strong heuristics about which paths to explore.

**3. Value-based selection:**
Assign a scalar value (rating 1-10) or classification (sure/likely/impossible) to each thought to indicate quality.

**Zero-Shot Tree of Thoughts:**
A clever single-prompt implementation:

```
Imagine three different experts are answering this question.
All experts will write down 1 step of their thinking, then share it with the group.
Then all experts will go on to the next step, etc.
If any expert realizes they're wrong at any point then they leave.
The question is... [your question]
```

This simulates multiple reasoning paths in one prompt.

**Strengths:**
- Substantially outperforms CoT on tasks requiring extensive planning or search
- Can recover from reasoning errors by backtracking
- Explores the solution space systematically
- Good for problems with multiple valid approaches

**Limitations:**
- Computationally expensive (many model calls)
- Requires more complex implementation than simple CoT
- Overkill for straightforward problems
- Needs good evaluation criteria for thoughts

**When to use what:**
- Simple arithmetic → Zero-Shot CoT
- Complex but straightforward reasoning → Few-Shot CoT
- Exploration/planning/search problems → Tree of Thoughts

### Self-Consistency

**Definition:** Generate multiple reasoning chains independently (using CoT), then take a majority vote on the final answer. Leverages the idea that correct reasoning is more consistent.

**When to use:**
- Critical decisions where accuracy matters more than cost
- Problems where occasional reasoning errors occur
- Tasks where multiple reasoning paths lead to the same answer
- Verification and quality control

**How it works:**
1. Run Chain-of-Thought prompting multiple times (5-10 times)
2. Each run produces a reasoning chain and answer
3. Collect all answers
4. Take the most common answer (majority vote)

This approach simply asks a model the same prompt multiple times and takes the majority result as the final answer.

**Example:**

Prompt (run 5 times):
```
Q: Leah had 32 chocolates and her sister had 42. If they ate 35, how many pieces do they have left in total?

Let's think step by step.
```

**Run 1:** 32 + 42 = 74, then 74 - 35 = 39. Answer: 39
**Run 2:** Total is 74. They ate 35. 74 - 35 = 39. Answer: 39
**Run 3:** They had 74 total chocolates. After eating 35, they have 39 left. Answer: 39
**Run 4:** 32 + 42 = 74. Subtract 35: 74 - 35 = 39. Answer: 39
**Run 5:** Start with 74 chocolates (32 + 42). Ate 35. Left with 39. Answer: 39

**Majority vote:** 39 appears 5/5 times → Final answer: 39

**Why it works:** Complex reasoning problems typically admit multiple different ways of thinking leading to the same unique correct answer. Errors are usually inconsistent, while correct reasoning converges.

**Practical consideration:** You can adjust the temperature to get more diverse reasoning paths. Higher temperature = more variation = better coverage of reasoning space.

**Performance gains:** Self-consistency has been shown to improve results even when regular Chain-of-Thought was found ineffective.

**Strengths:**
- More reliable than single CoT
- Catches occasional reasoning errors
- No additional prompt engineering needed (just run CoT multiple times)
- Works across arithmetic, commonsense, and symbolic reasoning

**Limitations:**
- Computationally expensive - typically limited to 5-10 paths due to costs
- Vulnerable if most CoT paths are consistently wrong
- Doesn't help if the model fundamentally doesn't understand the problem
- Only works for tasks with discrete answers that can be compared

**Cost optimization:** Use self-consistency to create better training data for fine-tuning, then the fine-tuned model achieves better accuracy in single runs.

### Pattern Recognition: When to Use What

```
START: Need to solve a reasoning task

Is it simple arithmetic or straightforward logic?
├─ YES: Try ZERO-SHOT CoT ("Let's think step by step")
│   ├─ Works? → DONE
│   └─ Inconsistent? → Add SELF-CONSISTENCY (run 5-10 times)
│
└─ NO: Is it complex multi-step reasoning?
    ├─ YES: Use FEW-SHOT CoT (2-5 examples with reasoning)
    │   ├─ Works? → DONE
    │   └─ Still errors? → Add SELF-CONSISTENCY
    │
    └─ NO: Does it require exploration/search/planning?
        └─ YES: Use TREE OF THOUGHTS
            ├─ Works? → DONE
            └─ Still fails? → Problem may need decomposition (next section)
```

**The golden rule by task type:**

| Task Type | Technique | Why |
|-----------|-----------|-----|
| Math problems | Zero-Shot or Few-Shot CoT | Clear steps, one answer |
| Logic puzzles | Few-Shot CoT | Benefits from seeing reasoning patterns |
| Planning | Tree of Thoughts | Needs exploration of options |
| Debugging | Few-Shot CoT | Show debugging process |
| Critical calculations | CoT + Self-Consistency | Verify through redundancy |
| Complex analysis | Few-Shot CoT → Self-Consistency if needed | Build up reasoning, verify |
| Creative brainstorming | Tree of Thoughts | Explore multiple ideas |

### Practical Implementation Tips

**1. Start simple, add complexity only when needed:**
```
Iteration 1: No CoT → 60% accuracy
Iteration 2: Zero-Shot CoT → 82% accuracy (STOP HERE if good enough)
Iteration 3: Few-Shot CoT → 89% accuracy
Iteration 4: CoT + Self-Consistency → 94% accuracy
```

Don't jump to expensive techniques if simpler ones work.

**2. Combine with Context Provision:**
```
Few-shot examples + Chain-of-thought reasoning:

Example 1: [problem with reasoning steps]
Example 2: [problem with reasoning steps]
Example 3: [problem with reasoning steps]

Now solve: [your problem]
Let's think step by step.
```

**3. Control reasoning depth:**

For simple problems:
```
Let's solve this step by step.
```

For complex problems:
```
Let's solve this carefully step by step. For each step:
1. State what we know
2. Identify what operation is needed
3. Perform the calculation
4. Verify the result makes sense
```

**4. Extract and verify:**
When using CoT, structure your output:
```
<reasoning>
[Step-by-step thinking here]
</reasoning>

<answer>
[Final answer here]
</answer>
```

This makes it easy to parse and verify.

### Common Mistakes

**Mistake 1: Using CoT for simple tasks**
Don't overcomplicate. "What's 2+2?" doesn't need "Let's think step by step."

**Mistake 2: Not controlling output format**
If you need a specific answer format, specify it: "End with: The answer is [number]."

**Mistake 3: Ignoring when reasoning fails**
If CoT produces wrong reasoning, don't trust the answer. Upgrade to Few-Shot or check your problem formulation.

**Mistake 4: Over-relying on self-consistency**
If 5 runs all give the same wrong answer, self-consistency won't help. The problem is upstream.

**Mistake 5: Using Few-Shot CoT with bad examples**
One wrong example can teach the model wrong reasoning patterns. Verify all examples.

---

**Summary:**
- **Zero-Shot CoT:** Add "Let's think step by step" - instant reasoning for free
- **Few-Shot CoT:** Show 2-5 examples with full reasoning - teaches specific patterns
- **Tree of Thoughts:** Explore multiple paths, backtrack, find best - for complex planning
- **Self-Consistency:** Run CoT multiple times, majority vote - for critical accuracy
- **Pattern:** Math/logic/planning/debugging = always use some form of CoT

Reasoning Enhancement is your second major tool after Context Provision. Together they handle ~90% of prompting challenges: Context Provision shows the format, Reasoning Enhancement shows the thinking process.