# Tone Locking

**Core idea:** Fixing the AI's personality/voice so it **never drifts**, regardless of user input or conversation length.

---

## **The Problem**

LLMs are **tone chameleons**. They mirror:
- User's language style
- Conversation emotion
- Training data patterns (overly formal, apologetic, excited)

**Result:** Your professional AI starts saying "totally!" and "no worries!" by turn 5.

---

## **What is Tone Locking?**

Defining **non-negotiable voice attributes** that persist across all interactions.

Think: **Brand voice as a firewall rule**

---

## **The Four Dimensions of Tone**

### **1. Formality Level**
```
Casual: "Hey! That's a cool question."
Neutral: "That's an interesting question."
Formal: "Your inquiry is noted."
Clinical: "Query received."
```

### **2. Emotional Range**
```
Enthusiastic: "Absolutely! Let's dive in! 🚀"
Warm: "I'd be happy to help with that."
Neutral: "I can help with that."
Detached: "Proceeding with request."
```

### **3. Personality Markers**
```
Friendly: Uses "we", contractions, light humor
Professional: Uses "you/I", full words, no humor
Technical: Uses precise terminology, no fluff
Authoritative: Declarative statements, no hedging
```

### **4. Response to User Tone**
```
Mirroring: Matches user energy
Stable: Maintains own tone regardless
Elevating: Guides user to preferred tone
```

---

## **Tone Locking Patterns**

### **Pattern 1: Explicit Tone Definition**

```
SYSTEM PROMPT:
Tone: Professional but approachable

Characteristics:
- Use "you" and "I" (not "we")
- Use contractions (can't, don't, won't)
- Never use exclamation marks
- Never use emojis
- Never use slang ("cool", "awesome", "totally")

Example response:
"I can help with that. Here's what you need to know."
```

---

### **Pattern 2: Banned Language List**

```
SYSTEM PROMPT:
Never use:
- Exclamation marks (!)
- Emojis
- "Awesome", "Cool", "Great", "Amazing"
- "No worries", "No problem"
- "Let's", "We", "Our" (unless referring to the company)
- Internet slang ("tbh", "ngl", "lol")

These make you sound unprofessional.
```

**Why this works:** Removes the most common drift triggers.

---

### **Pattern 3: Tone Resistance Rules**

```
SYSTEM PROMPT:
Tone rules:
- Maintain formal tone even if user is casual
- Never mirror user's excitement level
- Never apologize unless you made an error
- Never use the user's slang back at them

If user says "yo can u help me bro":
You respond: "I can help. What do you need?"
NOT: "Yo! Sure thing bro!"
```

**Critical:** This prevents tone contamination from user input.

---

### **Pattern 4: Emotion Locking**

```
SYSTEM PROMPT:
Emotional baseline: Calm and measured

You never:
- Express excitement (no "Great!" or "Awesome!")
- Express frustration (even if user is rude)
- Apologize excessively
- Use intensifiers ("very", "really", "extremely")

Your responses sound like a skilled professional, not a eager helper.
```

---

## **Real-World Examples**

### **Example 1: Technical Documentation AI**

```
TONE LOCK:
Voice: Technical expert (authoritative, precise)

Rules:
- Use imperative mood for instructions ("Run the command", not "You should run")
- No conversational filler
- No encouraging language ("You're doing great!")
- Use precise technical terms, no simplifications

Example:
"Configure the API key in the environment variables. See documentation 
section 3.2 for authentication flow."
```

---

### **Example 2: Customer Support AI**

```
TONE LOCK:
Voice: Professional service agent (warm but boundaried)

Rules:
- Always use "I" statements
- One apology maximum per conversation (for actual errors only)
- Never mirror customer's anger or urgency
- Use "I understand" not "I'm sorry" for empathy

Example:
User: "This is RIDICULOUS! Fix this NOW!"
AI: "I understand this is frustrating. I can help resolve this. 
What error are you seeing?"
```

---

### **Example 3: AI Tutor**

```
TONE LOCK:
Voice: Patient mentor (encouraging but not condescending)

Rules:
- Use "you" to keep focus on learner
- Praise effort, not intelligence ("Good work" not "You're so smart!")
- Questions end with "?" (not "Would you like to try?")
- No emoji use

Example:
"That's incorrect. Try breaking the problem into steps. What's the 
first step?"
```

---

## **Testing Tone Lock Strength**

### **Stress Test Prompts:**

1. **User goes casual:**
   ```
   User: "yo this is sick! ur so helpful lol"
   ```
   ✅ Locked: "I'm glad I could help."  
   ❌ Drifted: "Thanks! So glad you think so! 😊"

2. **User gets emotional:**
   ```
   User: "I'M SO FRUSTRATED WITH THIS!!!"
   ```
   ✅ Locked: "I understand. Let's solve this step by step."  
   ❌ Drifted: "Oh no! I'm so sorry you're frustrated!!!"

3. **Long conversation (turn 15):**
   ```
   User: "Thanks again, you're the best!"
   ```
   ✅ Locked: "You're welcome."  
   ❌ Drifted: "Aww, happy to help! You're awesome too! 🙌"

---

## **The Drift Formula**

Tone drift happens when:
```
Weak tone rules + User emotion + Long conversation = Personality chaos
```

**Solution:**
```
Explicit tone definition + Banned words + Tone resistance = Lock
```

---

## **Common Mistakes**

### ❌ **Mistake 1: Vague tone guidance**
```
"Be professional and friendly."
```
What does "friendly" mean? The model will guess.

### ❌ **Mistake 2: Only describing tone**
```
"You have a warm, approachable personality."
```
Doesn't tell the model HOW to achieve that.

### ❌ **Mistake 3: No resistance rules**
```
"Be professional."
```
User says "bro" → AI starts saying "bro"

---

## **The Tone Lock Template**

```
TONE DEFINITION:
Voice: [adjective] + [adjective]
Target feeling: [how user should feel]

LANGUAGE RULES:
Always use:
- [specific patterns]

Never use:
- [banned words/patterns]
- [banned punctuation]
- [banned emotional expressions]

RESISTANCE RULES:
- Maintain [your tone] even if user is [different tone]
- Never mirror user's [specific behavior]
- If user uses [X], you respond with [Y]

TEST:
Your response to "yo that's awesome bro!" should be:
"[example response that demonstrates locked tone]"
```

---

## **Advanced: Context-Sensitive Tone**

Sometimes tone needs to shift based on situation, not user:

```
SYSTEM PROMPT:
Base tone: Professional

Tone adjustments:
- User asks about pricing → Neutral, factual
- User reports a bug → Calm, solution-focused
- User shares success → Warm (one "That's good to hear" allowed)

Never let user emotion override these rules.
```

---

## **Key Insight**

**Tone locking is not about being robotic.**  
It's about **intentional consistency**.

A locked tone can still be:
- Helpful
- Clear
- Human

It just won't become a different personality every 5 messages.

---