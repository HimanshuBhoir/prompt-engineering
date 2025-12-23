### Teaching

**The unique challenge:** Teaching requires adapting to learner level, providing scaffolded support, encouraging critical thinking, and avoiding just giving answers.

**Key principles for teaching prompts:**

**1. Define the learner persona clearly**

Core components of effective educational prompts include specifying roles, limiting scope, and emphasizing output format.

```
You are a patient tutor helping a 10th-grade student learn algebra. The student understands basic arithmetic but struggles with abstract concepts. Use simple language, concrete examples, and encourage the student to think through problems rather than just providing answers.

Topic: Solving linear equations
```

**2. Use Chain-of-Thought for reasoning transparency**

Chain-of-thought prompting guides LLMs to outline reasoning step-by-step, making the thinking process transparent and accessible to students.

```
Explain how photosynthesis works to a middle school student.

For each major step:
1. State what happens in simple terms
2. Explain why this step is necessary
3. Provide a real-world analogy
4. Show how it connects to the next step

Think through your explanation step-by-step before presenting it.
```

**3. Socratic method prompting**

Don't give direct answers - guide discovery:

```
A student asks: "Why does ice float on water?"

Instead of explaining directly:
1. Ask guiding questions that lead them to discover the answer
2. Build on their responses with follow-up questions
3. Only provide hints if they're truly stuck
4. Celebrate when they reach the insight themselves

Example approach:
"That's a great question! Let's think about this together. What do you know about what happens to water when it freezes? Does it get heavier or lighter?"
```

**4. Scaffolded learning prompts**

```
Create a 3-lesson sequence teaching 8th graders about the Pythagorean theorem:

LESSON 1 (Foundation):
- Start with squares and area
- Visual demonstration with grid paper
- Simple examples with whole numbers

LESSON 2 (Application):
- Connect to right triangles
- Practice problems with increasing difficulty
- Real-world applications (ladder problems, etc.)

LESSON 3 (Extension):
- Proof of the theorem
- Complex problems
- Connection to distance formula

Each lesson should:
- Begin with review of prerequisites
- Include 3-5 worked examples
- Provide 5 practice problems (increasing difficulty)
- End with reflection questions
```

**5. Feedback generation prompts**

Studies show LLMs can provide formative feedback that increases student text revision, motivation, and positive emotions.

```
A student submitted this essay answer:
[paste student answer]

The question asked:
[paste question]

Provide constructive feedback using this framework:
1. STRENGTHS: What did they do well? (Be specific)
2. AREAS FOR GROWTH: What's missing or could be improved?
3. GUIDING QUESTIONS: 2-3 questions to help them think deeper
4. NEXT STEPS: One concrete action they can take to improve

Tone: Encouraging and growth-oriented. Balance critique with recognition.
```

**6. Personalization prompts**

```
I'm teaching [topic] to [learner description: age, background, interests].

Customize your explanation by:
1. Using examples related to their interests
2. Connecting to their prior knowledge
3. Adjusting vocabulary to their level
4. Providing analogies from their lived experience

For example, if teaching fractions to a student interested in cooking, use recipe examples.
```

**7. Assessment creation**

```
Create a quiz assessing understanding of [topic] for [grade level]:

Requirements:
- 10 questions total
- Mix of difficulty levels (3 easy, 5 medium, 2 hard)
- Variety of question types:
  * Multiple choice (test recall)
  * Short answer (test understanding)
  * Application problems (test transfer)
  * One open-ended question (test synthesis)

For each question:
- Provide answer key
- Explain common misconceptions for wrong answers
- Suggest follow-up for students who struggle
```

**8. TRACI Framework**

The TRACI framework (Task, Role, Audience, Create, Intent) helps structure prompts by considering key parameters like the activity requested, overall purpose, and target audience.

```
TRACI Prompt Example:

TASK: Create a lesson plan teaching cell division
ROLE: You are an experienced high school biology teacher
AUDIENCE: 10th-grade students in an honors biology class
CREATE: A 45-minute lesson plan with activities and assessment
INTENT: Students should understand mitosis vs. meiosis and why each matters

Now generate the lesson plan following this framework.
```

**Common mistakes in teaching prompts:**

**Mistake 1: Asking for direct answers without pedagogical consideration**
```
BAD: "Explain quantum mechanics"
GOOD: "Create a scaffolded introduction to quantum mechanics for undergraduates with physics background. Start with classical mechanics review, introduce wave-particle duality through double-slit experiment, then build to Schrödinger equation. Include think-pair-share activities and formative assessment checkpoints."
```

**Mistake 2: Not specifying learner misconceptions to address**
```
Include these common misconceptions to explicitly address:
- "Heavier objects fall faster" (gravity)
- "Seasons are caused by distance from sun" (Earth's tilt)
- "Evolution means 'survival of the fittest'" (actually survival of the fit enough)
```

---

