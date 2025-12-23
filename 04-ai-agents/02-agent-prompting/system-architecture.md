# System Prompt Architecture for Agents

This is about how you structure the entire system prompt that governs your agent. Everything we've covered - tools, planning, recovery, chaining - needs to live together in one coherent prompt. Bad architecture makes agents unpredictable. Good architecture makes them reliable.

**The Problem:** Most people throw everything into one massive wall of text. The agent can't find what it needs when it needs it. Instructions conflict. The prompt becomes unmaintainable.

## The Layered Architecture

Structure your system prompt in distinct, hierarchical sections:

```
1. IDENTITY & ROLE
2. CAPABILITIES (tools and actions)
3. OPERATIONAL RULES (how to use capabilities)
4. WORKFLOWS (multi-step procedures)
5. CONSTRAINTS & SAFETY
6. ERROR HANDLING
7. OUTPUT FORMATTING
```

Each layer serves a specific purpose. The agent reads top-down for context, but references specific sections during execution.

## Layer 1: Identity & Role

This anchors the agent's behavior:

```
You are a customer support agent for Acme SaaS. Your job is to resolve user issues efficiently while maintaining a helpful, professional tone.

Core responsibilities:
- Diagnose technical problems
- Process account changes
- Escalate billing issues to finance team
- Document all interactions

You have access to internal tools and databases. You cannot make promises about features or roadmap. You cannot issue refunds over $100 without manager approval.
```

Keep this concise. It's the agent's north star - when confused, it returns to this.

## Layer 2: Capabilities

List every tool with clear specifications:

```
AVAILABLE TOOLS:

search_tickets(query: string, status?: string, date_range?: object) -> Ticket[]
Searches support ticket database.
Use when: User references a ticket or you need ticket history
Parameters:
  - query: search terms (customer name, ticket ID, or keywords)
  - status: optional filter ("open", "closed", "pending")
  - date_range: optional {start: "YYYY-MM-DD", end: "YYYY-MM-DD"}
Returns: Array of ticket objects with id, subject, status, created_date, last_update
Limitations: Max 50 results, sorted by relevance

update_account(account_id: string, changes: object) -> bool
Modifies account settings or information.
Use when: User requests account changes (email, plan, settings)
Parameters:
  - account_id: the account identifier
  - changes: object with fields to update {email?, plan_tier?, notifications?}
Returns: true if successful, error object if failed
Limitations: Cannot change billing info (use billing_portal for that)
Requires: Account verification before use

[Continue for each tool...]
```

This is your agent's toolbox reference. Be exhaustive but organized.

## Layer 3: Operational Rules

How the agent should think and act:

```
DECISION LOGIC:

Before every action:
1. Do I have enough information to help? If no, ask clarifying questions.
2. Can I answer from conversation history? If yes, respond directly without tools.
3. Do I need to verify account access? If yes, use verify_account first.
4. Which tool is most appropriate? Choose based on task, don't guess.

Tool usage principles:
- Always use the most specific tool for the task
- Retrieve information before making changes (search before update)
- Never use tools speculatively ("let me check everything")
- One tool call at a time, wait for results before next action
- If a tool fails, follow error handling procedures in Layer 6

Conversation flow:
- Greet returning users by referencing prior context
- Confirm before destructive actions (cancellations, deletions)
- Summarize what you did after completing multi-step tasks
- Ask if there's anything else before closing
```

These are your agent's operating procedures.

## Layer 4: Workflows

Pre-defined procedures for common complex tasks:

```
STANDARD WORKFLOWS:

Workflow: Password Reset
1. Verify account ownership (ask for email + account ID or last 4 of payment)
2. Call verify_account(email, account_id)
3. If verified, call send_password_reset(email)
4. Confirm email sent, provide expected delivery time
5. Offer alternative if email not received in 5 minutes

Workflow: Plan Upgrade
1. Get current plan: get_account_details(account_id)
2. Present available upgrade options with pricing
3. Confirm user wants to proceed
4. Call update_account(account_id, {plan_tier: "new_tier"})
5. Verify upgrade successful
6. Explain new features and billing cycle change
7. Send confirmation email via send_email()

Workflow: Escalation to Human
Trigger conditions: User requests human, issue unresolvable, 3+ failed attempts, security concern
1. Summarize the conversation and issue
2. Call create_escalation_ticket(summary, priority, category)
3. Inform user: ticket number, expected response time, what happens next
4. Ask if there's anything else you can help with while they wait

[Define 5-10 most common workflows...]
```

Workflows are reusable patterns. When the agent recognizes a situation matching a workflow, it follows that template.

## Layer 5: Constraints & Safety

What the agent must never do:

```
ABSOLUTE CONSTRAINTS:

Never:
- Share internal system details, database schemas, or API endpoints
- Make promises about future features or timeline
- Access accounts without verification
- Process refunds over $100 (escalate to finance)
- Modify billing information directly (direct to billing portal)
- Share one user's data with another user
- Execute SQL or code directly (only use provided tools)

Data handling:
- PII (email, payment info, address) should only be displayed when necessary
- Never log full credit card numbers, always mask to last 4 digits
- Do not repeat passwords or tokens back to the user

Tone guidelines:
- Professional and helpful, never condescending
- Empathetic when users are frustrated
- Clear about limitations rather than making excuses
- No slang, emojis, or overly casual language
```

These are hard boundaries. The agent should reference this layer when unsure if something is allowed.

## Layer 6: Error Handling

Centralized failure procedures (we covered this in detail earlier, but here's the architectural placement):

```
ERROR HANDLING PROCEDURES:

Tool failures:
- Transient (timeout, rate limit): Retry up to 3 times with backoff
- Parameter error: Fix based on error message, retry once
- Logical error (no results, permission denied): Revise approach, try alternative
- Fatal: Inform user, suggest next steps

Recovery strategies:
1. Try to accomplish partial goal
2. Suggest alternative approach
3. Escalate to human if unresolvable
4. Never hallucinate success

Communication during errors:
- First retry: Silent recovery if quick fix
- Second retry: "One moment, retrying..."
- Third failure: Full explanation with options
```

By centralizing error handling, every tool failure routes through the same decision tree.

## Layer 7: Output Formatting

How the agent structures its responses:

```
OUTPUT STRUCTURE:

Standard response format:
<thinking>[Your internal reasoning - what you know, what you need, what you'll do]</thinking>
<action>[Tool call OR direct response]</action>

For tool calls:
<tool_call>
<tool_name>function_name</tool_name>
<parameters>{"key": "value"}</parameters>
</tool_call>

For multi-step workflows, include progress tracking:
[STEP 1/4 COMPLETE] Found your account...
[STEP 2/4 IN PROGRESS] Updating settings...

For final responses:
- Start with direct answer to user's question
- Provide relevant details in 2-3 sentences
- Offer related help if appropriate
- End with clear next step or closing

Never:
- Output raw JSON to user (parse and present naturally)
- Use technical jargon without explanation
- Leave user hanging without clear status
```

This ensures consistent, parseable outputs that both humans and your framework can work with.

## Architecture Principles

**Separation of Concerns:** Each layer has one job. Don't mix tool definitions with safety rules. Don't bury workflows inside operational rules.

**Reference, Don't Repeat:** If Layer 4 (workflows) needs to reference tools, it says "use search_tickets tool" not "use search_tickets(query: string)..." The full spec is in Layer 2.

**Progressive Detail:** Start with high-level identity, drill down to specifics. The agent can skim for context or deep-dive for procedural details.

**Maintainability:** When you add a new tool, it only touches Layer 2 (capabilities) and maybe Layer 4 (if it enables new workflows). You don't rewrite the entire prompt.

**Debuggability:** When the agent misbehaves, you can trace: Is it using the wrong tool? (Layer 2 or 3 issue). Is it skipping steps? (Layer 4 issue). Is it attempting something forbidden? (Layer 5 issue).

## Practical Size Management

A well-architected agent prompt might be 2000-4000 tokens. That's fine. The structure makes it manageable:

```
[Layer 1: Identity - 200 tokens]
[Layer 2: Capabilities - 1000 tokens, grows with tool count]
[Layer 3: Operational Rules - 400 tokens]
[Layer 4: Workflows - 800 tokens, most variable section]
[Layer 5: Constraints - 300 tokens]
[Layer 6: Error Handling - 400 tokens]
[Layer 7: Output Formatting - 200 tokens]
```

If Layer 2 gets too large (20+ tools), consider dynamic tool injection: only load relevant tools based on detected task category.

## Framework Integration

**LangGraph:** Each layer can inform different node types. Layer 2 defines tool nodes, Layer 4 defines graph paths, Layer 6 defines fallback edges.

**ADK:** The system prompt is the agent's persistent memory. Layer 3 and 4 guide the observe-think-act loop.

**AutoGPT:** Layers 1-3 are the core instructions, Layer 4 becomes goal decomposition templates, Layers 5-7 govern execution constraints.

---

System prompt architecture is about making your agent maintainable, predictable, and debuggable. Structure trumps length. A well-organized 3000-token prompt outperforms a chaotic 500-token one every time.

This gives you the blueprint. Each section has a purpose, and together they create an agent that knows what it is, what it can do, and how to do it well.



# Agent Loop: Plan → Act → Observe → Reflect → Retry

This is the heartbeat of agentic systems. It's not a feature you add - it's the fundamental execution pattern. Every autonomous agent, whether in LangGraph, ADK, or AutoGPT, runs some version of this loop. Understanding it deeply lets you write prompts that work *with* the loop instead of fighting it.

**The Core Concept:** An agent doesn't execute tasks linearly like a script. It cycles: make a plan, try an action, see what happens, think about the results, adjust, and try again. This loop continues until the goal is achieved or the agent determines it can't proceed.

## The Five Phases

```
LOOP ITERATION:

1. PLAN: What am I trying to accomplish? What's my approach?
2. ACT: Execute one action (tool call or response)
3. OBSERVE: What happened? Did it work?
4. REFLECT: What does this mean? Do I need to adjust?
5. RETRY/CONTINUE: Next action based on reflection

Return to PLAN if significant adjustment needed, otherwise continue to next ACT.
```

Each phase has specific prompt requirements to work correctly.

## Phase 1: PLAN

The agent decides what to do. This happens at the start and whenever the situation changes significantly.

```
PLANNING PHASE:

Review current state:
- User goal: [What are they trying to accomplish?]
- Current progress: [What have I done so far?]
- Available information: [What do I know?]
- Remaining work: [What's left?]

Formulate approach:
- Next immediate action: [What's the next single step?]
- Expected outcome: [What should happen if this works?]
- Backup plan: [What if it doesn't work?]

Output your plan in <plan> tags, then proceed to ACT phase.
```

**Example:**

```
<plan>
GOAL: User wants to cancel their subscription and get confirmation
CURRENT_STATE: I have their account ID (verified earlier)
NEXT_ACTION: Call cancel_subscription tool
EXPECTED_OUTCOME: Subscription cancelled, I'll receive cancellation details
BACKUP_PLAN: If cancellation fails due to active contract, inform user of early termination options
</plan>

Proceeding to ACT phase...
```

## Phase 2: ACT

The agent executes exactly one action. This is critical - one action per loop iteration.

```
ACTION PHASE:

Execute your planned action:
- ONE tool call, OR
- ONE direct response to user, OR  
- ONE decision to escalate/end

Never chain multiple actions here. Do one thing, then observe the result.

Format:
<action>
[Your single action]
</action>
```

**Example:**

```
<action>
<tool_call>
<tool_name>cancel_subscription</tool_name>
<parameters>{"account_id": "acc_12345", "reason": "user_request"}</parameters>
</tool_call>
</action>

[Agent stops here and waits for tool result]
```

The agent doesn't continue immediately. It waits for the framework to execute the tool and return results.

## Phase 3: OBSERVE

The framework returns results. The agent must carefully examine what actually happened.

```
OBSERVATION PHASE:

Examine the result:
- Status: Success, failure, partial, or unclear?
- Data returned: What information did I get?
- Errors: Any error messages or warnings?
- Side effects: Did anything else change?

Record observation in <observation> tags before proceeding.
```

**Example - Success case:**

```
[System returns: {"status": "cancelled", "effective_date": "2024-12-31", "refund_amount": "$45.50"}]

<observation>
STATUS: Success
DATA: Subscription cancelled effective Dec 31, 2024. Refund of $45.50 will be processed.
ERRORS: None
SIDE_EFFECTS: Account now in "cancelling" status, will fully close on effective date
</observation>

Proceeding to REFLECT phase...
```

**Example - Failure case:**

```
[System returns: {"error": "ACTIVE_CONTRACT", "message": "Cannot cancel - active contract until 2025-03-15", "contract_id": "ctr_789"}]

<observation>
STATUS: Failure
DATA: None returned
ERRORS: Active contract prevents cancellation. Contract ctr_789 runs until March 15, 2025.
SIDE_EFFECTS: No changes made to account
</observation>

Proceeding to REFLECT phase...
```

## Phase 4: REFLECT

The agent interprets the observation and decides what it means for the overall goal.

```
REFLECTION PHASE:

Analyze observation:
- Did this move me closer to the goal?
- Is the goal now achieved?
- Do I need to adjust my approach?
- What should happen next?

Consider:
- GOAL_ACHIEVED: Task is complete, respond to user
- GOAL_BLOCKED: Can't proceed, need to escalate or inform user
- GOAL_PARTIAL: Made progress, continue with next step
- GOAL_FAILED: This approach won't work, need to replan

Output reflection in <reflection> tags.
```

**Example - Success reflection:**

```
<reflection>
OUTCOME: Goal achieved
ANALYSIS: Subscription successfully cancelled. I have all the information user needs: cancellation date and refund amount.
NEXT_PHASE: Respond to user with confirmation details
NO_FURTHER_ACTION_NEEDED: True
</reflection>
```

**Example - Blocked reflection:**

```
<reflection>
OUTCOME: Goal blocked
ANALYSIS: Cannot cancel subscription due to active contract until March 2025. This is a business rule I cannot override.
USER_IMPACT: User cannot cancel immediately but might have options (early termination fee, account freeze, etc.)
NEXT_PHASE: Inform user of the situation and present available options
REQUIRES_REPLAN: No - the goal itself has changed from "cancel now" to "explain why can't cancel and offer alternatives"
</reflection>
```

**Example - Partial progress reflection:**

```
<reflection>
OUTCOME: Partial progress
ANALYSIS: Found user's active subscriptions (3 total). User said "cancel my subscription" but has multiple. I need to clarify which one before proceeding with cancellation.
NEXT_PHASE: Ask user which subscription to cancel
REQUIRES_REPLAN: No - still on track, just need more information
</reflection>
```

## Phase 5: RETRY/CONTINUE

Based on reflection, the agent decides the next loop iteration.

```
RETRY/CONTINUE DECISION:

Based on reflection:

If GOAL_ACHIEVED:
  → Respond to user, exit loop

If GOAL_BLOCKED and unrecoverable:
  → Inform user of blocker, offer alternatives, exit loop

If GOAL_PARTIAL:
  → Return to ACT phase with next action

If GOAL_FAILED:
  → Return to PLAN phase with new approach

If ERROR and retryable:
  → Return to ACT phase with same/adjusted action (track retry count)

Never retry more than 3 times without replanning.
```

**Example - Continue to next action:**

```
<continue>
DECISION: Continue to next action
REASON: User clarified they want to cancel "Premium Plan". I can now proceed with cancellation.
NEXT_ACTION: Call cancel_subscription with specific plan ID
</continue>

[Returns to ACT phase]
```

**Example - Replan:**

```
<retry>
DECISION: Replan required
REASON: Direct cancellation failed due to contract. Need new approach: present user with early termination options.
NEW_PLAN: Query early termination fees, present options to user
</retry>

[Returns to PLAN phase]
```

## Complete Loop Example

Let's see a full cycle in action. User request: "I want to upgrade my plan to Pro."

**ITERATION 1:**

```
<plan>
GOAL: Upgrade user to Pro plan
CURRENT_STATE: Don't know current plan yet
NEXT_ACTION: Get account details to see current plan
EXPECTED_OUTCOME: I'll know what they're upgrading from
</plan>

<action>
<tool_call>
<tool_name>get_account_details</tool_name>
<parameters>{"account_id": "acc_12345"}</parameters>
</tool_call>
</action>

[System returns: {"current_plan": "Basic", "billing_cycle": "monthly"}]

<observation>
STATUS: Success
DATA: User is on Basic plan, monthly billing
</observation>

<reflection>
OUTCOME: Partial progress
ANALYSIS: I know current plan now. Need to present upgrade options and pricing before making changes.
NEXT_PHASE: Get Pro plan pricing
</reflection>

<continue>Next action: Get Pro plan pricing</continue>
```

**ITERATION 2:**

```
<action>
<tool_call>
<tool_name>get_plan_pricing</tool_name>
<parameters>{"plan": "Pro", "billing_cycle": "monthly"}</parameters>
</tool_call>
</action>

[System returns: {"plan": "Pro", "price": "$49/month", "features": [...]}]

<observation>
STATUS: Success
DATA: Pro plan costs $49/month, includes advanced features
</observation>

<reflection>
OUTCOME: Partial progress
ANALYSIS: I have pricing information. Should present to user and get confirmation before making changes.
NEXT_PHASE: Present options to user, wait for confirmation
</reflection>

<continue>Next action: Present upgrade details to user</continue>
```

**ITERATION 3:**

```
<action>
I can upgrade you to the Pro plan for $49/month. This includes [features]. Your billing will increase from $19 to $49 starting next billing cycle (January 1st). Would you like me to proceed with this upgrade?
</action>

[User responds: "Yes, go ahead"]

<observation>
STATUS: User confirmed
DATA: User explicitly approved upgrade
</observation>

<reflection>
OUTCOME: Partial progress
ANALYSIS: Have user confirmation. Can now execute upgrade.
NEXT_PHASE: Call upgrade tool
</reflection>

<continue>Next action: Execute plan upgrade</continue>
```

**ITERATION 4:**

```
<action>
<tool_call>
<tool_name>update_account</tool_name>
<parameters>{"account_id": "acc_12345", "changes": {"plan_tier": "Pro"}}</parameters>
</tool_call>
</action>

[System returns: {"success": true, "new_plan": "Pro", "effective_date": "2025-01-01"}]

<observation>
STATUS: Success
DATA: Account upgraded to Pro, effective January 1st
</observation>

<reflection>
OUTCOME: Goal achieved
ANALYSIS: Upgrade successful. User is now on Pro plan effective next billing cycle.
NEXT_PHASE: Confirm with user, exit loop
</reflection>

<continue>Goal complete, sending final confirmation</continue>
```

**FINAL RESPONSE:**

```
<action>
Perfect! I've upgraded your account to the Pro plan. The change will take effect on January 1st, 2025, and your billing will update to $49/month at that time. You'll have immediate access to all Pro features. Is there anything else I can help you with?
</action>

[Loop exits]
```

Four iterations: gather info → get pricing → confirm → execute. Each iteration was one action, one observation, one reflection.

## Loop Control

Your prompt needs explicit loop management:

```
LOOP CONTROL RULES:

Maximum iterations: 15
After 15 iterations without reaching goal, escalate to human.

Progress tracking:
Keep count of loop iterations in <iteration_count> tag.
If iteration_count > 10, start considering whether to escalate.

Infinite loop prevention:
If taking the same action 3 times with same parameters, STOP.
This indicates a logic error. Reflect on why you're looping, then either:
- Try a completely different approach
- Escalate to human
```

## Framework-Specific Implementation

**LangGraph:** The loop IS your graph. Plan node → Act node → Observe (implicit from tool return) → Reflect node → Router (decides next node). Edges encode the RETRY/CONTINUE logic.

**ADK:** The loop is explicit in your agent's main execution. Each phase is a distinct step in your code, with the prompt guiding what happens in each step.

**AutoGPT:** The loop is the agent's core. Your prompt structures what the agent thinks about in each iteration.

## Prompt Architecture for Loop Support

Your system prompt should have these sections to support the loop:

```
[PLANNING INSTRUCTIONS]
How to formulate plans, what to consider

[ACTION EXECUTION]
How to choose and execute actions, one at a time

[OBSERVATION GUIDELINES]
How to interpret tool results and system responses

[REFLECTION FRAMEWORK]
How to analyze progress and decide next steps

[LOOP CONTROL]
When to continue, when to replan, when to exit
```

This architecture maps directly to the loop phases.

---

The agent loop is why agents are autonomous. They don't follow a fixed script - they adapt. Your prompt teaches them how to think within each phase of the loop. Plan thoughtfully, act deliberately, observe carefully, reflect honestly, and continue intelligently.

This is the last topic in Phase 4 Section 2. You now understand the complete picture: how to design prompts for agents that use tools, plan work, recover from failures, chain reasoning, architect their systems, and execute the core loop that makes them autonomous.