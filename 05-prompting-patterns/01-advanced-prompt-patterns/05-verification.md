## Verification (When: Accuracy is critical)

Verification techniques are about double-checking outputs when correctness matters. In high-stakes scenarios—fact-checking, medical advice, financial decisions, legal analysis—you can't afford hallucinations or errors. Verification prompting builds reliability into your system.

### ReAct (Reasoning + Acting)

**Definition:** ReAct synergizes reasoning and acting by generating both reasoning traces and task-specific actions in an interleaved manner. The model reasons about what to do, takes an action (like calling a tool or searching), observes the result, then reasons again based on what it learned.

**When to use:**
- Multi-step tasks requiring external information
- Problems where you need to verify facts from external sources
- Tasks that benefit from iterative refinement based on observations
- Scenarios where internal knowledge might be insufficient or outdated

**How it works:** ReAct prompts LLMs to generate verbal reasoning traces and actions for a task, allowing the system to perform dynamic reasoning while interacting with external environments.

The pattern: **Thought → Action → Observation → Thought → Action → ...**

**Example:**
```
Question: What is the elevation range for the area that the eastern sector of the Colorado orogeny extends into?

Thought 1: I need to search Colorado orogeny, find the area that the eastern sector extends into, then find the elevation range of that area.

Action 1: Search[Colorado orogeny]

Observation 1: The Colorado orogeny was an episode of mountain building in Colorado and surrounding areas.

Thought 2: It does not mention the eastern sector. I need to look up eastern sector.

Action 2: Lookup[eastern sector]

Observation 2: The eastern sector of the Colorado orogeny extends into the High Plains.

Thought 3: High Plains is the area. I need to search High Plains and find its elevation range.

Action 3: Search[High Plains]

Observation 3: The High Plains are a subregion of the Great Plains, mostly in the Western United States. The elevation ranges from 1,800 to 7,000 feet.

Thought 4: The elevation range is 1,800 to 7,000 feet.

Answer: 1,800 to 7,000 feet
```

Notice how reasoning guides what action to take next, and observations from actions inform the next reasoning step.

**Why it's verification:** ReAct solves tasks with interpretable and factual trajectories by grounding reasoning in external environments rather than relying solely on limited internal knowledge, which helps overcome misinformation.

**Practical implementation:**
```
You are a research assistant. For complex questions, use this ReAct pattern:

1. THOUGHT: Reason about what information you need
2. ACTION: Specify what tool to use and with what query
3. OBSERVATION: I will provide the result
4. THOUGHT: Analyze the observation and decide next step
5. Repeat until you can answer confidently

Available actions:
- search[query]: Search for information
- lookup[term]: Find specific term in the last search result
- finish[answer]: Provide final answer when confident

Question: {user_question}
```

**Strengths:**
- Outperforms reasoning-only and acting-only approaches by enabling models to ind uce, track, and update action plans while gathering additional information
- Provides interpretable, step-by-step problem-solving trajectories
- Reduces hallucination by grounding answers in retrieved facts
- Can handle exceptions and update plans dynamically

**Limitations:**
- Recent research shows brittleness - exemplar selection significantly affects performance, and 40% of thoughts don't translate to valid actions in some models
- Requires multiple model calls (more expensive and slower)
- Needs well-designed tools and clear action specifications
- Can propagate errors if early actions retrieve wrong information

**Best combined with:** Research shows the best results come from combining ReAct with Chain-of-Thought, using both internal knowledge and externally obtained information.

### Verification Prompting

**Definition:** Explicitly ask the model to verify its own output by checking it against known facts, logic, or constraints.

**When to use:**
- Factual claims that can be verified
- Mathematical or logical reasoning
- Code that needs to match specifications
- Any output where you can define verification criteria

**Basic pattern:**
```
Step 1: Generate answer
Step 2: Verify answer against criteria
Step 3: If verification fails, revise and reverify
```

**Example - Mathematical verification:**
```
Problem: If a train travels 120 miles in 2 hours, then slows down and travels 80 miles in 2 hours, what's the average speed for the entire trip?

SOLVE:
First portion: 120 miles in 2 hours = 60 mph
Second portion: 80 miles in 2 hours = 40 mph
Average speed: (60 + 40) / 2 = 50 mph

VERIFY:
Wait, average speed is total distance / total time, not average of speeds.
Total distance: 120 + 80 = 200 miles
Total time: 2 + 2 = 4 hours
Average speed: 200/4 = 50 mph
Verification: Correct!

Answer: 50 mph
```

**Implementation:**
```
Solve the problem step by step, then verify your answer.

SOLVE:
[Your solution here]

VERIFY:
- Check each calculation
- Ensure logic is sound
- Confirm answer makes sense in context
- If any check fails, identify the error and correct it

FINAL ANSWER: [After verification]
```

**Sophisticated verification - Multiple perspectives:**
```
Claim: "Electric cars produce zero emissions."

VERIFICATION FROM MULTIPLE ANGLES:

1. Literal interpretation: 
   TRUE - Electric cars have no tailpipe emissions during operation.

2. Full lifecycle perspective:
   PARTIALLY TRUE - Zero *direct* emissions, but electricity generation and battery production create emissions.

3. Context-dependent:
   DEPENDS - If powered by renewable energy, nearly zero. If powered by coal plants, significant indirect emissions.

VERDICT: The claim is technically true but misleading without context. Should be rephrased as "Electric cars produce zero direct emissions during operation."
```

**Self-correction pattern:**
```
Answer the question, then critique your own answer and improve it.

FIRST ANSWER:
[Initial response]

SELF-CRITIQUE:
- What assumptions did I make?
- What could be wrong?
- What did I overlook?
- How confident am I? (0-100%)

REVISED ANSWER:
[Improved response addressing critiques]
```

**Strengths:**
- Catches obvious errors before presenting to user
- Forces systematic thinking about answer quality
- Can be implemented with a single model
- Improves reliability without external tools

**Limitations:**
- Model may not catch its own systematic errors
- Adds tokens and latency
- Can over-correct and change correct answers to wrong ones
- Limited by the model's own knowledge and capabilities

### Multiple Perspectives

**Definition:** Approach the same problem from different viewpoints, then reconcile or select the best answer. Multi-Perspective approaches generate diverse outputs from different perspectives and embed consistency information to identify the best solution.

**When to use:**
- Subjective questions with multiple valid approaches
- Complex problems where different frameworks apply
- Situations where bias in one perspective might mislead
- Creative tasks benefiting from diverse viewpoints

**Pattern 1: Role-based perspectives**
```
Analyze this business decision from three perspectives:

PERSPECTIVE 1 - Financial Analyst:
[Analysis focusing on ROI, cash flow, financial risk]

PERSPECTIVE 2 - Customer Advocate:
[Analysis focusing on customer experience, satisfaction, retention]

PERSPECTIVE 3 - Operations Manager:
[Analysis focusing on implementation, resources, feasibility]

SYNTHESIS:
[Integrate insights, note conflicts, recommend balanced approach]
```

**Pattern 2: Method-based perspectives (for coding):**
In code generation, you can prompt LLMs to generate from three perspectives: Solution (actual code), Specification (requirements), and Test case (verification), then check consistency across all three.

```
Task: Write a function to find the median of a list.

PERSPECTIVE 1 - Solution:
```python
def median(lst):
    sorted_lst = sorted(lst)
    n = len(sorted_lst)
    mid = n // 2
    if n % 2 == 0:
        return (sorted_lst[mid-1] + sorted_lst[mid]) / 2
    return sorted_lst[mid]
```

PERSPECTIVE 2 - Specification:
Function should:
- Accept a list of numbers
- Return the middle value if odd length
- Return average of two middle values if even length
- Handle empty lists appropriately

PERSPECTIVE 3 - Test Cases:
```python
assert median([1, 3, 5]) == 3  # odd length
assert median([1, 2, 3, 4]) == 2.5  # even length
assert median([5]) == 5  # single element
assert median([3, 1, 4, 1, 5]) == 3  # unsorted
```

VERIFICATION:
Check if Solution satisfies Specification and passes all Test Cases.
```

**Pattern 3: Adversarial perspectives**
```
Claim: "Remote work increases productivity."

SUPPORTING PERSPECTIVE:
- Eliminates commute time
- Reduces office distractions
- Allows flexible scheduling
- Evidence: [cite studies showing productivity gains]

OPPOSING PERSPECTIVE:
- Reduces collaboration
- Increases isolation
- Blurs work-life boundaries
- Evidence: [cite studies showing productivity losses]

NUANCED SYNTHESIS:
Remote work's effect on productivity depends on:
- Job type (individual vs collaborative)
- Individual personality (self-directed vs needs structure)
- Company support (tools, policies, culture)
- Home environment
Recommendation: Hybrid model with flexibility
```

**Pattern 4: Expert panel**
```
Imagine three experts discussing this question:

Expert A (Optimist): [Positive interpretation, best-case scenario]
Expert B (Pessimist): [Negative interpretation, worst-case scenario]
Expert C (Realist): [Balanced view, most likely scenario]

After hearing all three, what's the most reasonable conclusion?
```

**Consistency-based selection:**
When you generate multiple perspectives, choose the answer that appears most consistently across perspectives, or that reconciles conflicting perspectives most coherently.

**Strengths:**
- Reduces bias from single-perspective thinking
- Catches errors one perspective might miss
- Provides more robust, well-rounded answers
- Particularly good for subjective or complex questions

**Limitations:**
- Expensive (multiple generations)
- Requires good synthesis to reconcile differences
- May produce overly complex answers
- Not always necessary for straightforward factual questions

### Pattern Recognition: When to Verify

```
Decision tree for verification:

Is accuracy critical?
├─ YES: Use verification
│   ├─ Need external facts? → ReAct
│   ├─ Need logical consistency? → Self-verification
│   ├─ Subjective/complex? → Multiple perspectives
│   └─ All of the above? → Combine techniques
│
└─ NO: Skip verification (save cost/time)
    But consider: What's the cost of an error?
```

**Use cases by domain:**

| Domain | Verification Method | Why |
|--------|-------------------|-----|
| Medical advice | ReAct + Multiple Perspectives | Lives at stake, need facts + medical reasoning |
| Legal analysis | Multiple Perspectives + Verification | Multiple interpretations, need careful logic |
| Financial calculations | Self-Verification | Math must be exact, steps must be checked |
| Code generation | Multiple Perspectives (Solution/Spec/Test) | Functional correctness critical |
| Creative writing | Usually skip | Subjective, no "correct" answer |
| Fact-checking | ReAct | Need to ground in authoritative sources |
| Strategic decisions | Multiple Perspectives | Benefit from diverse viewpoints |

---

## Summary: Verification & Advanced/Meta Patterns

**Verification (accuracy is critical):**
- **ReAct:** Interleave reasoning and external actions for grounded, verifiable answers
- **Verification:** Self-check outputs against criteria
- **Multiple Perspectives:** Approach from different angles, reconcile viewpoints
- **Pattern:** Fact-checking, medical/legal/financial = always verify