I'll search for the latest information on Decomposition techniques in prompt engineering to provide you with current, accurate content.Now let me search for information on "Plan then Execute" prompting:Perfect! Now I have comprehensive, up-to-date information on all three Decomposition techniques. Let me create the teaching content.

---

## Decomposition (When: Task too complex for single step)

Decomposition is about breaking down complex problems into manageable pieces. When a task is too difficult to solve in one go, you teach the model to divide and conquer. This is fundamental for research, analysis, complex planning, and multi-step problem-solving.

**The Core Insight:** Some problems are inherently too complex for a single reasoning chain. The model needs to break them down systematically, solve each piece, and combine the results.

---

### Least-to-Most Prompting

**Definition:** Break a complex problem into a sequence of simpler subproblems, then solve them sequentially, with each solution building on previous ones .

**When to use:**
- Compositional problems where the answer depends on solving intermediate steps
- Tasks requiring progressive reasoning from simple to complex
- Problems where solving one part gives you the tools to solve the next
- Multi-step calculations or transformations
- When Chain-of-Thought struggles with problem complexity

**How it works:** Least-to-Most operates in two distinct stages :

**Stage 1 - Decomposition:** Prompt the model to break down the complex problem into a list of simpler subproblems

**Stage 2 - Sequential Solving:** Solve each subproblem in order, using previously solved subproblems and their answers as context for the next

**The key difference from CoT:** Unlike Chain-of-Thought where each step is independent, Least-to-Most uses the output of previous subproblems as input for the next.

**Example - Last Letter Concatenation:**

Task: Given words "think, machine, learning," output the concatenation of the last letters.

```
STAGE 1: Decomposition Prompt

Q: "jump around backward twice after walk opposite forward thrice" can be solved by:
A: "jump around backward twice after walk opposite forward thrice" can be solved by:
"walk opposite forward thrice", "jump around backward twice after walk opposite forward thrice"

Q: "think, machine, learning" can be solved by:
```

Model decomposes into:
- "think"
- "think, machine" 
- "think, machine, learning"

```
STAGE 2: Sequential Solving Prompt

Q: "think"
A: The last letter of "think" is "k". So "think" outputs "k".

Q: The last letter concatenation of "think" is "k". What is the last letter concatenation of "think, machine"?
A: The last letter of "machine" is "e". Concatenating "k" with "e" gives "ke". So "think, machine" outputs "ke".

Q: The last letter concatenation of "think, machine" is "ke". What is the last letter concatenation of "think, machine, learning"?
A:
```

Model output: The last letter of "learning" is "g". Concatenating "ke" with "g" gives "keg". So "think, machine, learning" outputs "keg".

**Why it works:** The model builds up the solution incrementally. Each step is simple enough to solve correctly, and the context from previous solutions guides the next step.

**Research findings:** Least-to-Most prompting achieves 99.7% accuracy on compositional generalization tasks, compared to only 16% with standard Chain-of-Thought. The technique enables models to solve problems more difficult than those seen in the prompts .

**Math reasoning example:**

```
Decomposition prompt:

Q: Elsa has 5 apples. Anna has 2 more apples than Elsa. How many apples do they have together?
A: Let's break down this problem:
1. How many apples does Anna have?
2. How many apples do Elsa and Anna have together?

Sequential solving:

Subproblem 1: How many apples does Anna have?
Anna has 2 more than Elsa. Elsa has 5 apples. So Anna has 2 + 5 = 7 apples.

Subproblem 2 (using answer from subproblem 1): How many apples do Elsa and Anna have together?
Elsa has 5 apples. Anna has 7 apples. Together they have 5 + 7 = 12 apples.

The answer is 12.
```

**Single-prompt variant:** For simpler tasks, you can combine both stages:

```
To solve this problem, first break it into smaller subproblems, then solve each one using the previous answers:

Problem: [complex problem]

Step 1: List the subproblems
Step 2: Solve subproblem 1
Step 3: Using the answer from step 2, solve subproblem 2
...
```

**Strengths:**
- Dramatically better than CoT on compositional problems
- Enables "easy-to-hard" generalization
- Each subproblem is simpler and less error-prone
- Can be combined with other techniques (CoT, self-consistency)

**Limitations:**
- Requires two separate prompts (decomposition then solving) unless simplified
- More token-intensive than single-shot approaches
- Decomposition quality determines final result
- Can suffer from error propagation if early subproblems are wrong

---

### Step-Back Prompting

**Definition:** Before solving a specific problem, step back to derive high-level concepts, principles, or abstractions that guide the reasoning process .

**When to use:**
- Domain-specific reasoning (physics, chemistry, law)
- Knowledge-intensive question answering
- Multi-hop reasoning requiring background knowledge
- Problems where first principles matter
- When direct reasoning leads to errors due to missing foundational understanding

**How it works:** Step-Back prompting has two stages: First, prompt LLMs to derive high-level abstractions such as concepts and principles for a specific example. Second, leverage the reasoning ability to reason on top of the high-level concepts and principles.

**The key insight:** Sometimes you need to zoom out to the general case before solving the specific instance. Think "what's the underlying principle?" before "what's the answer?"

**Example - Physics Problem:**

```
WITHOUT Step-Back (direct approach):

Q: Estella's ball fell 200 feet from the roof. How long did it take to fall to the ground?

A: Using the formula d = 1/2 * g * t^2... [proceeds with calculation, but might get the wrong formula or units]
```

```
WITH Step-Back:

Step 1: Abstraction prompt:
Q: Estella's ball fell 200 feet from the roof. How long did it take to fall to the ground?

What physics principles or formulas are relevant to this problem?

A: This is a free-fall problem involving:
- Kinematics equations for uniformly accelerated motion
- The relevant equation is: d = v₀t + (1/2)gt²
- Where g = 32 ft/s² (gravitational acceleration)
- Initial velocity v₀ = 0 (dropped, not thrown)

Step 2: Reasoning with principles:
Now using these principles, solve: Estella's ball fell 200 feet from the roof. How long did it take to fall to the ground?

A: Using d = (1/2)gt² with v₀ = 0:
200 = (1/2)(32)t²
200 = 16t²
t² = 12.5
t = 3.54 seconds

The ball took 3.54 seconds to fall.
```

**The abstraction question format:**

For different domains:
- Physics: "What are the relevant physical principles?"
- Chemistry: "What chemical concepts or laws apply here?"
- History: "What historical context is relevant?"
- Math: "What mathematical theorems or properties are needed?"

**Performance gains:** Step-Back prompting improves PaLM-2L performance on MMLU Physics and Chemistry by 7% and 11% respectively, TimeQA by 27%, and MuSiQue by 7%.

**Advanced: Combining with RAG:**

```
Step 1: Generate abstraction question
Q: [Specific question about a historical event]
Abstraction: What general historical trends or context are relevant?

Step 2: Retrieve based on abstraction
[System retrieves relevant historical context using the abstraction as the query]

Step 3: Reason with both abstraction and facts
Using the historical context [retrieved docs] and the general principles [abstraction], answer: [specific question]
```

**Real-world example - Medical diagnosis:**

```
Patient case: "55-year-old male presents with chest pain, shortness of breath, and sweating for 30 minutes."

Step-Back prompt:
What are the key medical principles for evaluating acute chest pain?

Model abstracts:
- Differential diagnosis for chest pain includes cardiac, pulmonary, GI causes
- Time-sensitive conditions: MI, PE, aortic dissection require immediate evaluation
- Key features: onset, character, radiation, associated symptoms
- Risk factors: age, gender, cardiovascular history

Now, using these principles, evaluate: [original case]

The model now reasons within the framework of established medical principles rather than jumping to conclusions.
```

**Implementation tip:** Use few-shot examples showing the abstraction step:

```
Example 1:
Question: [Specific chemistry problem]
Step-back: What are the relevant chemical concepts?
Answer: [Principles: stoichiometry, conservation of mass, etc.]
Solution: [Using those principles...]

Example 2:
Question: [Another specific problem]
Step-back: What are the relevant concepts?
Answer: [Principles...]
Solution: [Using those principles...]

Now you try:
Question: [Your problem]
Step-back: What are the relevant concepts?
```

**Strengths:**
- Corrects 20.5% of errors that direct prompting makes 
- Particularly effective for STEM and knowledge-intensive tasks
- Brings in relevant background knowledge
- Reduces reliance on memorized patterns

**Limitations:**
- Requires two reasoning steps (abstraction, then application)
- Not all problems benefit from abstraction (simple lookups don't need principles)
- Principle generation itself can sometimes be wrong
- More token-intensive than direct prompting

---

### Plan-and-Solve (Plan then Execute)

**Definition:** First devise a plan by dividing the entire task into smaller subtasks, then execute the subtasks according to the plan .

**When to use:**
- Complex problems requiring multiple distinct steps
- Tasks where you need to understand the full scope before starting
- When Zero-Shot CoT produces missing-step errors
- Multi-tool workflows in agentic systems
- Long documents requiring structured analysis

**The problem it solves:** Zero-Shot Chain-of-Thought suffers from three pitfalls: calculation errors, missing-step errors, and semantic misunderstanding errors. Plan-and-Solve specifically addresses the missing-step problem.

**How it works:** Two-stage prompting where planning happens first, execution second:

**Basic Plan-and-Solve (PS):**

```
Prompt: Let's first understand the problem and devise a plan to solve it. Then, let's carry out the plan to solve the problem step by step.

Problem: [Your problem]
```

**Enhanced Plan-and-Solve (PS+):**

```
Prompt: Let's first understand the problem, extract relevant variables and their corresponding values, and devise a complete plan. Then, let's carry out the plan, calculate intermediate results (pay attention to calculation and common sense), solve the problem step by step, and show the answer.

Problem: [Your problem]
```

**Example - Math Word Problem:**

```
WITHOUT Plan-and-Solve (Zero-Shot CoT):

Q: A juggler can juggle 16 balls. Half of the balls are golf balls, and half of the golf balls are blue. How many blue golf balls are there?

A: Let's think step by step.
There are 16 balls total.
Half are golf balls, so 16/2 = 8 golf balls.
The answer is 8.

[WRONG - missed the "half of golf balls are blue" step]
```

```
WITH Plan-and-Solve:

Q: A juggler can juggle 16 balls. Half of the balls are golf balls, and half of the golf balls are blue. How many blue golf balls are there?

Let's first understand the problem and devise a plan to solve it. Then, let's carry out the plan step by step.

A: 
PLAN:
1. Calculate how many balls are golf balls (half of 16)
2. Calculate how many golf balls are blue (half of golf balls)

EXECUTION:
Step 1: Half of 16 balls are golf balls → 16/2 = 8 golf balls
Step 2: Half of the 8 golf balls are blue → 8/2 = 4 blue golf balls

The answer is 4 blue golf balls.

[CORRECT - the plan ensured no steps were skipped]
```

**Research performance:** PS+ outperforms Zero-Shot CoT on symbolic reasoning, common sense, and math reasoning datasets. On challenging math problems, PS+ achieves comparable performance to 8-shot Chain-of-Thought prompting.

**Agent workflow application (LangGraph style):**

```
PLANNING NODE:

Given this user request: "Research our competitor's pricing and recommend adjustments"

Generate a plan:

PLAN:
1. Identify main competitors (search_web)
2. Extract their pricing from websites (web_scrape)  
3. Get our current pricing (database_query)
4. Analyze pricing differences (reasoning)
5. Generate recommendations (synthesis)
6. Format report (formatting)

Dependencies: Steps 1→2, steps 2+3→4, step 4→5, step 5→6
```

```
EXECUTION NODES:

Execute step 1: [search_web tool call]
Result: Found 3 main competitors

Execute step 2: [web_scrape tool call for each]
Result: Competitor pricing data collected

Execute step 3: [database_query tool call]
Result: Our pricing retrieved

Execute step 4: [reasoning without tools]
Result: Analysis complete

Execute step 5: [synthesis without tools]
Result: Recommendations generated

Execute step 6: [formatting]
Result: Final report ready
```

**RE-PLANNING: Adaptive Plan-and-Solve:**

```
After executing step 2, if web scraping fails:

REPLAN:
Original step 2 failed. New approach:
2a. Search for pricing in public reports (search_web with specific query)
2b. Check pricing aggregator sites (web_scrape alternative sources)

Continue with steps 3-6 as planned.
```

**PEARL variant for long documents:** PEARL (Prompting Large Language Models to Plan and Execute Actions Over Long Documents) uses three stages: action mining, plan formulation, and plan execution for complex document reasoning.

**Practical implementation tips:**

**1. Make plans explicit and structured:**

```
PLAN:
- Subtask 1: [description] → Tool: [which tool] → Expected output: [what you'll get]
- Subtask 2: [description] → Tool: [which tool] → Depends on: Subtask 1
- Subtask 3: [description] → Tool: [which tool] → Depends on: Subtasks 1, 2
```

**2. Separate planning from execution completely:**

Don't mix planning and execution in the same output. Force the model to complete the entire plan before executing step 1.

**3. Track execution progress:**

```
PLAN: [list of 5 steps]

EXECUTION PROGRESS:
✓ Step 1: Complete - [result]
✓ Step 2: Complete - [result]
→ Step 3: In progress...
  Step 4: Pending
  Step 5: Pending
```

**4. Enable plan revision:**

```
After Step 3 failure:

REVISED PLAN:
✓ Step 1: [Already complete]
✓ Step 2: [Already complete]
✗ Step 3: [Failed - tool unavailable]
→ Step 3-ALT: [Alternative approach]
  Step 4: [Modified based on 3-ALT]
  Step 5: [Original]
```

**Strengths:**
- Reduces missing-step errors dramatically
- Provides clear roadmap before execution
- Enables better error handling (revise plan if step fails)
- Natural fit for agentic workflows
- Can represent complex dependencies

**Limitations:**
- Requires model capable of good planning (works better with larger models)
- Two-stage process adds latency
- Plans can be overly rigid if not allowed to adapt
- Not necessary for simple single-step tasks

---

## Pattern Recognition: When to Use Which Decomposition Technique

```
START: Task too complex for single-step reasoning

Does the problem build progressively from simple to complex?
├─ YES: Use LEAST-TO-MOST
│   - Each step depends on previous steps
│   - Example: Letter concatenation, step-by-step construction
│
└─ NO: Does the problem require domain knowledge or principles?
    ├─ YES: Use STEP-BACK
    │   - Need to understand underlying concepts first
    │   - Example: Physics problems, medical diagnosis, legal reasoning
    │
    └─ NO: Is it a multi-tool workflow or complex planning task?
        └─ YES: Use PLAN-AND-SOLVE
            - Multiple distinct steps with dependencies
            - Example: Research tasks, agent workflows, document analysis
```

**Quick reference table:**

| Problem Type | Technique | Why |
|---|---|---|
| String manipulation progressing through list | Least-to-Most | Sequential building |
| Math word problems with multiple steps | Plan-and-Solve | Prevent missing steps |
| Physics/Chemistry calculations | Step-Back | Need formulas/principles first |
| Multi-tool agent workflow | Plan-and-Solve | Complex dependencies |
| Legal case analysis | Step-Back | Requires understanding precedents |
| Compositional generalization tasks | Least-to-Most | Build from simple patterns |
| Knowledge-intensive Q&A | Step-Back | Need background context |
| Long document reasoning | Plan-and-Solve (PEARL) | Structure complex analysis |

---

## Combining Decomposition with Other Techniques

**Least-to-Most + Chain-of-Thought:**

```
For each subproblem, use CoT reasoning:

Subproblem 1: [problem]
Let's think step by step: [CoT reasoning]
Answer: [result]

Subproblem 2 (using answer from subproblem 1): [problem]
Let's think step by step: [CoT reasoning]
Answer: [result]
```

**Step-Back + Few-Shot:**

```
Show examples of stepping back:

Example 1:
Q: [Specific problem]
Step back: What principles apply?
A: [Principles]
Solution using principles: [Answer]

Example 2: [Similar structure]

Your turn:
Q: [New problem]
Step back: What principles apply?
```

**Plan-and-Solve + Self-Consistency:**

```
Generate 5 different plans for the same task
Execute each plan
Compare results
Take majority answer or best-quality result
```

---

## Common Mistakes

**Mistake 1: Using decomposition for simple tasks**  
Don't decompose "What's 2+3?" - just solve it directly. Decomposition adds overhead.

**Mistake 2: Wrong decomposition boundaries**  
Least-to-Most works when steps build on each other. If steps are independent, use Plan-and-Solve instead.

**Mistake 3: Forgetting to pass context forward**  
In Least-to-Most, each subproblem MUST include previous solutions. Don't make the model start from scratch each time.

**Mistake 4: Over-abstracting in Step-Back**  
"What are the relevant principles?" can be too vague. Be specific: "What physics formulas apply to free-fall problems?"

**Mistake 5: Not allowing plan revision**  
Plans fail. Build in mechanisms to detect failures and revise plans mid-execution.

---

## Summary

**Decomposition techniques:**

- **Least-to-Most:** Break into sequential subproblems, solve progressively using previous answers
- **Step-Back:** Abstract to principles first, then reason from first principles
- **Plan-and-Solve:** Create complete plan upfront, then execute step-by-step

**Pattern:** Research/analysis/complex coding/multi-step workflows = decompose

**Selection rule:** If problem builds sequentially → Least-to-Most. If needs domain knowledge → Step-Back. If has complex dependencies → Plan-and-Solve.

Decomposition is your third major technique after Context Provision and Reasoning Enhancement. Together, these three handle the vast majority of complex prompting challenges.