# Thought vs Action Separation

This is about teaching your agent to *think before it acts*. Most agent failures happen because the LLM jumps straight to tool use without reasoning through the problem first.

**The Problem:** When you give an LLM tools, it often becomes trigger-happy. User says "what's the weather?" and it immediately calls a weather API before considering: which location? The user's location from context? A specific city they mentioned earlier?

**The Solution:** Force explicit reasoning steps before any action.

## The Two-Phase Pattern

You structure your agent to operate in two distinct modes: thinking and acting. The LLM must finish thinking before it can act.

```
You operate in two phases:

THOUGHT PHASE:
Analyze the situation. Consider:
- What is the user actually asking for?
- What information do I already have?
- What information do I need?
- Which tool(s) would help?
- What could go wrong?

Output your thoughts in <thinking> tags.

ACTION PHASE:
After thinking, if you need a tool, output exactly one <tool_call>.
If you can answer without tools, provide your response.
```

## Practical Example

User asks: "Find my recent orders and cancel the one from last Tuesday."

**Without separation** (bad):
```
<tool_call>
<tool_name>get_orders</tool_name>
<parameters>{"user_id": "unknown"}</parameters>
</tool_call>
```

The agent jumped to action with incomplete information.

**With separation** (good):
```
<thinking>
The user wants to cancel a specific order from last Tuesday. 
I need to:
1. Get their user_id from the conversation context
2. Retrieve their recent orders
3. Identify which order was placed on Tuesday (need to know what date that was)
4. Confirm which order before canceling

I don't see a user_id in the conversation yet. I should ask first rather than making assumptions.
</thinking>

I'd be happy to help cancel that order. Could you provide your email address or customer ID so I can look up your orders?
```

The thinking phase caught the missing information.

## Implementation Patterns

**Strict Sequential (recommended for critical tasks):**
```
Process every request in this order:
1. Output <thinking> with your analysis
2. If action needed, output ONE <tool_call>
3. Stop and wait for tool result
4. Return to step 1 with new information

Never skip the thinking step. Never output tool calls inside thinking tags.
```

**Interleaved (for simpler tasks):**
```
You can think and act in the same response, but thinking must come first:

<thinking>Analysis here</thinking>
<tool_call>Action here</tool_call>
```

## Why This Matters in LangGraph/ADK

In LangGraph, you can literally create separate nodes: a "think" node and an "act" node. The graph structure enforces the separation. But the LLM still needs prompting to use each node correctly.

In ADK or AutoGPT-style systems, the loop typically goes: observe → think → act. Your prompt needs to tell the LLM what content belongs in the "think" phase versus what triggers the "act" phase.

## Making It Work

The key is *enforcing* the separation. If you just say "think before acting," the LLM will often blend them. Use structural markers:

```
Your response must follow this structure:

<thinking>
What is the user asking?
What do I know?
What do I need?
What's my plan?
</thinking>

<action>
Either a tool call OR a direct response, never both.
</action>
```

Your parsing code then validates this structure. If thinking tags are missing, reject the response and retry.

## Advanced: Chain of Thought for Actions

For complex decisions, add substeps to thinking:

```
<thinking>
USER_INTENT: [What they want]
AVAILABLE_INFO: [What I have]
MISSING_INFO: [What I need]
TOOL_CHOICE: [Which tool and why]
PARAMETERS: [What I'll pass and why]
RISK_CHECK: [What could go wrong]
</thinking>
```

This forces the LLM to consider each aspect before acting. It's more verbose but drastically reduces errors in high-stakes scenarios.

---

The principle: separate thinking from doing. The LLM plans explicitly, then acts deliberately. This gives you transparency into *why* the agent chose a particular action, and it catches errors before tools are invoked.
