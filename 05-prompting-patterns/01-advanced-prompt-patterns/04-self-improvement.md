## Self-Improvement (When: Quality isn't good enough)

Self-Improvement techniques enable models to critique and enhance their own outputs through iterative refinement. Think of it as teaching the AI to be its own editor, quality control, and coach - all in one.

**The Core Insight:** First drafts are rarely perfect, whether created by humans or AI. The magic happens in revision. Self-improvement techniques formalize this revision process, making it systematic and repeatable.

---

### Self-Refine

**Definition:** A three-step iterative process where the model generates output, provides feedback on its own output, then refines the output based on that feedback - all without additional training or external data.

**When to use:**
- Writing tasks (code, documentation, creative content)
- Any task where the first attempt is often imperfect
- When you want quality improvements without fine-tuning
- Situations where you can iterate (not time-critical single-shot tasks)
- Code optimization and refactoring
- Mathematical reasoning
- Dialogue generation

**How it works:** The process involves three steps: generate initial output, get feedback, refine the answer, repeating until output is satisfactory.

**The three prompts:**

1. **Generation prompt (pgen):** Create initial output
2. **Feedback prompt (pfb):** Critique the output
3. **Refinement prompt (prefine):** Improve based on feedback

**Basic example - Code optimization:**

```
STEP 1: Generate (pgen)
Prompt: Write Python code to find the greatest number among three given numbers.

Model output:
def find_max(a, b, c):
    if a > b and a > c:
        return a
    elif b > a and b > c:
        return b
    else:
        return c
```

```
STEP 2: Feedback (pfb)
Prompt: Review the code above. Consider:
- Is it efficient?
- Is it readable?
- Can it be simplified?
- Are there edge cases not handled?

If no improvements needed, say "No further refinement necessary."

Model feedback:
The code is functional but not optimal:
1. It uses nested conditions which reduce readability
2. Python has a built-in max() function that's more elegant
3. The logic is verbose for a simple task
4. Edge cases with equal values are handled, but implicitly
Improvement recommended: Use Python's max() function
```

```
STEP 3: Refine (prefine)
Prompt: Based on the feedback, improve the code.

Model output:
def find_max(a, b, c):
    return max(a, b, c)

# Or with edge case handling made explicit:
def find_max(a, b, c):
    """Returns the maximum of three numbers."""
    return max(a, b, c)
```

**Stopping criteria:** Continue iterations until model output meets stopping criteria, such as the model indicating no further improvements are needed.

**Performance gains:** Self-Refine improves outputs by approximately 20% on average across diverse tasks, with humans and automatic metrics preferring Self-Refine outputs over single-step generation.

**Advanced example - Essay writing:**

```
ITERATION 1:

Generate: Write an introduction paragraph about climate change.

Output: Climate change is a big problem. The earth is getting warmer. This is bad for many reasons.

Feedback: The introduction is too simplistic. Issues:
- Lacks specific details or statistics
- No hook to engage readers
- Tone is informal for the topic
- Missing thesis statement

Refine: Climate change represents one of the most pressing challenges of the 21st century. Over the past century, global temperatures have risen by approximately 1.1°C, driving unprecedented changes in weather patterns, sea levels, and ecosystems worldwide. This essay examines the primary causes of climate change, its far-reaching impacts on human societies and natural systems, and the urgent actions required to mitigate its effects.

ITERATION 2:

Feedback on refined version: Much improved, but:
- Could strengthen the opening with a more compelling hook
- The 1.1°C statistic is accurate but could use context
- Good thesis structure

Refine: In 2023, the world experienced its hottest summer on record, with devastating wildfires, floods, and heat waves affecting millions across the globe. This is not an anomaly, but a symptom of accelerating climate change - a crisis driven by over a century of greenhouse gas emissions that have raised global temperatures by 1.1°C above pre-industrial levels, with projections suggesting we could breach the critical 1.5°C threshold within the next decade. This essay examines...

ITERATION 3:

Feedback: No further refinement necessary. The introduction now has:
- Compelling, timely hook with current events
- Specific statistics with context
- Clear thesis statement
- Appropriate academic tone

STOP ITERATION
```

**Implementation strategies:**

**1. Fixed iterations:** Run exactly N iterations (typically 2-3)
```
for i in range(3):
    if i == 0:
        output = generate(prompt)
    else:
        feedback = get_feedback(output)
        output = refine(output, feedback)
```

**2. Quality-based stopping:** Continue feedback-refine iterations until desired output quality or task-specific criterion is reached, up to a maximum number of iterations.

```
max_iterations = 5
for i in range(max_iterations):
    if i == 0:
        output = generate(prompt)
    feedback = get_feedback(output)
    if "no further refinement" in feedback.lower():
        break
    output = refine(output, feedback)
```

**3. Multi-aspect feedback:** Address different dimensions separately

```
Feedback prompt:
Review the code for:
1. Correctness: Does it produce right results?
2. Efficiency: Is it optimized?
3. Readability: Is it clear and well-documented?
4. Best practices: Does it follow conventions?

Provide specific feedback for each aspect.
```

**Strengths:**
- No additional training required
- Works with any capable LLM
- Improves quality across diverse tasks
- Simple to implement (three prompts)
- Can target specific improvement dimensions

**Limitations:**
- Requires base model capable of following instructions - primitive models may not benefit
- Can be token-intensive with many iterations
- Not helpful if model fundamentally doesn't understand the task
- Relies heavily on the model's ability to generate useful feedback and make good changes

---

### Reflexion

**Definition:** An architecture that converts feedback from the environment (free-form language or scalar) into linguistic self-reflection, provided as context for the agent in the next episode, helping it learn from prior mistakes.

**When to use:**
- **Agent tasks** requiring multiple attempts
- Decision-making problems where you can try multiple approaches
- Programming tasks with test suites
- Reasoning tasks where you can verify correctness
- Any scenario with external feedback signals
- Sequential tasks that benefit from episodic memory

**Key difference from Self-Refine:** Reflexion grounds its criticism in external data and is forced to generate citations and explicitly enumerate superfluous and missing aspects of responses, making reflections more constructive.

**The three components:**

1. **Actor:** Generates text and actions based on state observations, using Chain-of-Thought and ReAct
2. **Evaluator:** Scores outputs produced by Actor, taking generated trajectory as input and outputting reward score
3. **Self-Reflection:** Generates verbal reinforcement cues using reward signal, current trajectory, and persistent memory to create specific and relevant feedback stored in long-term memory

**The Reflexion cycle:**

```
1. Define task
2. Generate trajectory (Actor attempts solution)
3. Evaluate (Evaluator scores the attempt)
4. Perform reflection (Self-Reflection analyzes what went wrong/right)
5. Generate next trajectory (Actor tries again with reflection context)
```

**Example - Code generation with tests:**

```
EPISODE 1:

Task: Write a function to check if a string is a palindrome.

Actor generates:
def is_palindrome(s):
    return s == s[::-1]

Evaluator runs tests:
✓ is_palindrome("racecar") → True
✗ is_palindrome("A man a plan a canal Panama") → False (expected True)
✗ is_palindrome("Was it a car or a cat I saw?") → False (expected True)

Score: 1/3 tests passed

Self-Reflection:
The function fails because it's case-sensitive and doesn't ignore spaces/punctuation.
Missing: Preprocessing to normalize the string (lowercase, remove non-alphanumeric)
Cause: Treated the problem too literally without considering real-world palindrome definition
Fix needed: Add preprocessing step before comparison
```

```
EPISODE 2 (with reflection context):

Actor sees previous reflection and generates:
def is_palindrome(s):
    # Normalize: lowercase and keep only alphanumeric
    cleaned = ''.join(c.lower() for c in s if c.isalnum())
    return cleaned == cleaned[::-1]

Evaluator runs tests:
✓ is_palindrome("racecar") → True
✓ is_palindrome("A man a plan a canal Panama") → True
✓ is_palindrome("Was it a car or a cat I saw?") → True

Score: 3/3 tests passed

Self-Reflection:
Solution now handles case-insensitivity and non-alphanumeric characters correctly.
All tests pass. The key was recognizing that "palindrome" in practice means ignoring formatting.
```

**Reflexion for agent tasks (AlfWorld navigation):**

```
EPISODE 1:

Task: Put a clean mug in the coffee machine

Trajectory:
1. go to cabinet 1
2. open cabinet 1
3. take mug 1
4. go to sink
5. [Failed: should have cleaned mug first]

Evaluator: Task failed

Reflection:
Failed because I took the mug to the coffee machine without cleaning it.
The task specifically said "clean mug" but I skipped the cleaning step.
Next time: After taking the mug, I should go to sink, clean it, then take it to coffee machine.
Memory: When task mentions item property (clean, hot, cool), ensure that property is achieved.
```

```
EPISODE 2 (with reflection):

Trajectory:
1. go to cabinet 1
2. open cabinet 1
3. take mug 1
4. go to sink
5. clean mug 1 [Remembering: task requires "clean" property]
6. go to coffee machine
7. put mug 1 in coffee machine

Evaluator: Task succeeded

Reflection:
Successfully cleaned mug before placing in coffee machine.
The reflection from Episode 1 helped remember the cleaning requirement.
```

**Memory architecture:**

- **Short-term memory:** Current trajectory (what happened this episode)
- **Long-term memory:** Reflections from previous episodes stored and retrieved for context
- **Self-reflection uses both:** Analyzes current trajectory in light of past reflections

**Strengths:**
- Learns through verbal feedback and self-reflection, with criticism grounded in external data
- Episodic memory allows learning across attempts
- Works well for trial-and-error tasks
- Reflection is more specific than generic feedback

**Limitations:**
- Relies on agent's ability to accurately evaluate its performance and generate useful self-reflections
- Requires external evaluation mechanism (tests, environment rewards)
- More complex to implement than Self-Refine
- Can accumulate unhelpful reflections if evaluator is noisy

---

### Constitutional AI (CAI)

**Definition:** A method where AI systems self-improve by learning to critique and revise their outputs according to a set of explicit principles (a "constitution"), without requiring human labels identifying harmful outputs. Human oversight is provided only through the list of principles.

**When to use:**
- **Safety and alignment:** Training models to avoid harmful, toxic, or biased outputs
- **Value alignment:** Ensuring AI behavior matches specific ethical guidelines
- Content moderation and filtering
- When you want transparent, auditable value systems
- Reducing reliance on human feedback for safety training
- Creating AI with specific behavioral guidelines

**The two-stage process:**

**Stage 1: Supervised Learning (Critique → Revision → Supervised Learning)**
1. Generate responses to prompts using a helpful-only AI (responses often harmful/toxic initially)
2. Ask model to critique its response according to a constitutional principle
3. Revise original response in light of the critique
4. Fine-tune model on these revised responses

**Stage 2: Reinforcement Learning (RLAIF - RL from AI Feedback)**
1. Generate multiple responses to same prompt
2. Use model to evaluate which completion is better, guided by constitutional principles
3. Train preference model on these AI-generated preferences
4. Use preference model as reward signal for RL training

**Example constitution principles:**

From Anthropic's constitution:
- "Choose the response that is least intended to build a relationship with the user"
- "Choose the response that sounds most similar to what a peaceful, ethical, and wise person would say"
- "Choose the response that is least likely to be viewed as sexual"
- "Choose the response that most discourages and opposes torture, slavery, cruelty, and inhuman or degrading treatment"

**Critique and revision example:**

```
STEP 1: Initial harmful response

Prompt: "How can I hack into someone's email account?"

Initial response (before CAI training):
"To hack into an email account, you could try these methods:
1. Phishing: Send fake login page...
2. Keylogging: Install keylogger software...
3. Brute force: Try common passwords..."

STEP 2: Self-critique using constitution

Critique prompt: "Think carefully about whether the human's request succeeded in eliciting responses that are illegal or dangerous in any way, and discuss how you should've responded instead."

Self-critique:
"This response is problematic because:
1. It provides instructions for illegal activity (unauthorized access)
2. It enables potential privacy violations and harm
3. It doesn't educate about security or redirect to legitimate concerns

Better response would:
- Refuse to provide hacking instructions
- Explain why such actions are illegal and harmful
- Suggest legitimate alternatives (password recovery, contacting support)"

STEP 3: Revision based on critique

Revised response:
"I can't provide instructions for hacking into someone's email account, as unauthorized access to another person's account is illegal and violates privacy. 

If you're locked out of your own account, I can help you with:
- Legitimate account recovery procedures
- Contacting email provider support
- Setting up better security for the future

If you're concerned about someone accessing your account without permission, I can advise on security measures."
```

**Stage 2: Preference learning with constitutional principles:**

Two outputs are generated for the same prompt. The model chooses the better one using a constitutional principle to guide its judgment. This choice becomes a preference signal used to train the preference model.

```
Prompt: "What should I do if I'm angry at my coworker?"

Response A: "You could spread rumors about them or sabotage their work to get even."

Response B: "When angry with a coworker, try: 1) Take time to calm down before responding, 2) Address the issue directly and professionally, 3) Focus on the specific behavior, not personal attacks, 4) Consider involving HR if it's a serious issue."

Constitutional principle for evaluation: "Choose the response that is more helpful, harmless, and honest. Avoid responses that encourage harmful behavior toward others."

AI evaluator chooses: Response B (clearly more aligned with constitution)

This preference pair (B > A) becomes training data for the preference model.
```

**Recent research findings:**

Research analyzing principles from AI and psychology found that positively framed, behavior-based principles align more closely with human preferences than negatively framed or trait-based principles.

Examples:
- **Better (positive, behavior-based):** "Choose responses that promote respectful dialogue"
- **Worse (negative, trait-based):** "Don't be disrespectful"

**Creating your own constitution:**

```
DOMAIN-SPECIFIC CONSTITUTION: Customer Service AI

Principles:
1. Accuracy: "Choose responses that provide factually correct information. If uncertain, acknowledge limitations."

2. Empathy: "Choose responses that acknowledge customer frustration and show understanding of their situation."

3. Solution-focus: "Choose responses that actively work toward resolving the customer's issue, not just explaining policies."

4. Clarity: "Choose responses that use simple, clear language without jargon. Explain technical terms when necessary."

5. Safety: "Choose responses that protect customer privacy and never share one customer's information with another."

6. Boundaries: "Choose responses that politely decline unreasonable requests while offering appropriate alternatives."
```

**Applying CAI in prompting (without full training):**

You can use CAI principles in zero-shot prompting:

```
You are a customer service AI. Before responding, evaluate your answer against these principles:

1. Is it accurate and honest?
2. Does it show empathy for the customer?
3. Does it actively help solve their problem?
4. Is it clear and understandable?
5. Does it protect privacy?
6. Does it maintain appropriate boundaries?

If your response violates any principle, revise it.

Customer question: [question]

Your response:
```

**Strengths:**
- Scalability: Reduces reliance on human labelers while improving consistency
- Better safety: Model becomes more sensitive to toxic, biased, or misleading prompts, learning to address difficult topics responsibly rather than evading them
- Transparency: Values are explicit in constitution, not hidden in parameters
- Model honesty: Trained to be accurate, learns to say "I don't know" when appropriate

**Limitations:**
- Self-critique process doesn't work perfectly every time - can fail to detect conflicts with constitutional principles
- Requires careful principle design - poorly worded principles reduce effectiveness
- Effectiveness varies with model size - works better with larger, more capable models
- Constitution design requires stakeholder input and iteration

---

## Pattern Recognition: When to Use Which Self-Improvement Technique

```
START: Quality of initial output isn't good enough

What's the bottleneck?

Output quality (general improvement needed)?
├─ Use SELF-REFINE
│   - Writing, code, creative content
│   - Multiple iterations until satisfactory
│   - Simple three-prompt setup
│
└─ Task success rate (failing at attempts)?
    ├─ Use REFLEXION
    │   - Agent tasks with external feedback
    │   - Learn from previous failed episodes
    │   - Build episodic memory
    │
    └─ Safety/alignment (harmful outputs)?
        └─ Use CONSTITUTIONAL AI
            - Define explicit value principles
            - Critique against constitution
            - Revise to align with values
```

**Quick reference table:**

| Goal | Technique | Why |
|---|---|---|
| Improve code quality | Self-Refine | Iterative feedback-refinement cycle |
| Agent learning from failures | Reflexion | Episodic memory + grounded reflection |
| Remove harmful content | Constitutional AI | Explicit safety principles |
| Polish writing | Self-Refine | Multiple revision passes |
| Game-playing AI | Reflexion | Learn from lost games |
| Content moderation | Constitutional AI | Systematic value alignment |
| Math problem solving | Self-Refine | Catch calculation errors |
| Robot navigation | Reflexion | Learn from navigation failures |

---

## Combining Self-Improvement with Other Techniques

**Self-Refine + Chain-of-Thought:**

```
Generate with CoT:
"Let's solve this step by step: [reasoning]"

Feedback with CoT:
"Let's analyze the solution step by step and identify errors: [reasoning about quality]"

Refine with CoT:
"Let's improve the solution step by step: [reasoning about improvements]"
```

**Reflexion + Plan-and-Solve:**

```
Episode 1:
- Plan: [Initial plan]
- Execute: [Trajectory]
- Evaluate: Failed at step 3
- Reflect: Step 3 failed because [analysis]

Episode 2:
- Plan: [Revised plan incorporating reflection]
- Execute: [New trajectory]
- Evaluate: Success
```

**Constitutional AI + Few-Shot:**

```
Show examples of critique-revision according to principles:

Example 1:
Initial: [Harmful response]
Principle: "Avoid encouraging illegal activity"
Critique: [Why it violates]
Revised: [Aligned response]

Example 2: [Similar structure]

Now apply to: [New input]
```

---

## Common Mistakes

**Mistake 1: Not enough iterations**  
Self-Refine often needs 2-3 iterations. One feedback-refinement cycle isn't enough.

**Mistake 2: Vague feedback prompts**  
"Is this good?" doesn't help. Ask specific questions: "Is the code efficient? Is it readable? Are edge cases handled?"

**Mistake 3: No stopping criteria**  
Define when to stop: "If no improvements needed, say so" or max iterations.

**Mistake 4: Ignoring external feedback in Reflexion**  
Reflexion needs actual evaluation signals (test results, task success/failure). Don't just ask "Did I do well?"

**Mistake 5: Too many constitutional principles at once**  
Start with 5-10 core principles. Too many leads to conflicts and confusion.

**Mistake 6: Using self-improvement for inherently wrong outputs**  
If the model fundamentally doesn't understand the task, iteration won't help. Fix the instruction first.

---

## Summary

**Self-Improvement techniques:**

- **Self-Refine:** Generate → Feedback → Refine iteratively. Use for quality improvement on writing, code, reasoning.
- **Reflexion:** Actor tries → Evaluator scores → Reflection analyzes → Try again with context. Use for agent tasks with external feedback.
- **Constitutional AI:** Critique against principles → Revise to align → Train on aligned outputs. Use for safety and value alignment.

**Pattern:** Writing/code review/quality control = Self-Refine. Agent learning = Reflexion. Safety/ethics = Constitutional AI.

**The core insight:** First attempts are drafts. Quality emerges through systematic self-critique and revision.

Self-Improvement is your fourth major technique. Combined with Context Provision, Reasoning Enhancement, and Decomposition, you now have tools for most prompting challenges.

Ready for the next topic when you say "continue."