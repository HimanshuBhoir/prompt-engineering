# Planning Prompts

Planning prompts teach your agent to break down complex tasks into steps before executing anything. This is what separates a reactive agent from a goal-oriented one.

**The Core Idea:** When a user asks for something that requires multiple actions, the agent should create a plan first, then execute that plan step by step. Without planning, agents ping-pong between tools randomly.

## Why Planning Matters

User request: "Book me a flight to Chicago next Tuesday and reserve a hotel near the conference center."

**Without planning:** The agent might book a flight, realize it needs the conference center address, search for it, book a hotel, then discover the flight times don't work with the hotel check-in. Now it needs to cancel and rebook. Chaos.

**With planning:** The agent creates a plan first: (1) Find conference center location, (2) Search flights for next Tuesday, (3) Search hotels near that location, (4) Present options to user, (5) Book after confirmation. Clean execution.

## Basic Planning Structure

```
When given a complex task, create a plan before acting:

<plan>
GOAL: [Restate what the user wants]
STEPS:
1. [First action needed]
2. [Second action needed]
3. [Continue...]
DEPENDENCIES: [Note which steps depend on others]
RISKS: [What could go wrong]
</plan>

After creating the plan, execute step 1. After each step completes, check if you need to revise the plan based on new information.
```

## Practical Example

User: "Find customers who haven't ordered in 6 months and send them a 20% discount code."

The agent outputs:

```
<plan>
GOAL: Re-engage inactive customers with discount offer

STEPS:
1. Query database for customers with last_order_date > 6 months ago
2. Filter out customers who unsubscribed from marketing
3. Generate unique discount codes for each customer
4. Draft personalized email template
5. Send emails in batches (not all at once to avoid spam filters)
6. Log the campaign for tracking

DEPENDENCIES:
- Step 2 depends on results from step 1
- Steps 3-5 must happen in sequence
- Step 6 happens after step 5

RISKS:
- Large customer list might timeout the query (add limit)
- Email service might have rate limits (batch sending in step 5)
- Need to verify discount code generation doesn't create duplicates
</plan>

Executing Step 1...
<tool_call>
<tool_name>query_database</tool_name>
<parameters>{"query": "SELECT * FROM customers WHERE last_order_date < DATE_SUB(NOW(), INTERVAL 6 MONTH) LIMIT 1000"}</parameters>
</tool_call>
```

## Dynamic Planning (Plan Refinement)

Static plans break when reality doesn't match expectations. Teach your agent to revise:

```
After completing each step:
1. Confirm the step succeeded
2. Check if new information requires plan changes
3. If yes, output <plan_revision> with updated steps
4. Proceed with next step

Example: If step 1 returns zero results, revise the plan to inform the user rather than continuing to step 2.
```

**Example revision:**

```
<plan_revision>
ORIGINAL_PLAN: Query customers, filter, send emails
ISSUE: Database query returned 0 customers matching criteria
REVISED_PLAN:
1. ✓ Queried database - found no customers inactive for 6+ months
2. NEW: Check if criteria is too strict (maybe try 3+ months)
3. NEW: Report findings to user before proceeding
</plan_revision>

I found that no customers have been inactive for 6 months. Would you like me to check for customers inactive for 3 months instead? That might give us a better re-engagement pool.
```

## Multi-Agent Planning (LangGraph Context)

In LangGraph, you might have a dedicated "planner" node:

```
You are the planning agent. Your only job is to create plans.

Given a user request, output:
<plan>
GOAL: [clear objective]
STEPS: [numbered list]
WHICH_AGENT: [for each step, specify which agent handles it: search_agent, email_agent, database_agent]
</plan>

You do not execute plans. You only create them.
```

Then your executor agents receive the plan and their assigned step.

## Plan Granularity

**Too coarse:**
```
1. Get customer data
2. Send emails
```
This doesn't help. What if step 1 fails? How?

**Too fine:**
```
1. Open database connection
2. Construct query
3. Execute query
4. Parse results
5. Close connection
...
```
This is micromanagement. The agent knows how to use a tool.

**Just right:**
```
1. Query customers inactive 6+ months (database_query tool)
2. Filter out unsubscribed users (check subscription_status field)
3. Generate discount codes (code_generator tool, batch of 100)
4. Draft email (use template_id: "reactivation_v2")
5. Send emails (email_service tool, batches of 50, 5min delay between batches)
```

Each step is one tool call or one decision, with enough detail to execute correctly.

## Critical Planning Instruction

```
Before executing a multi-step task, always plan first. A task is multi-step if:
- It requires more than one tool call
- It involves conditional logic (if X then Y)
- It affects multiple resources
- Failure at any step would require cleanup

For single-step tasks, skip planning and act immediately.
```

This prevents over-engineering simple requests while ensuring complex ones get proper planning.

## Error Handling in Plans

```
Each step in your plan should include a failure branch:

STEP 3: Generate discount codes
- SUCCESS: Proceed to step 4
- FAILURE: If code_generator returns error, log the issue and notify user before continuing
```

This makes plans robust. The agent knows what to do when things go wrong.

---

Planning prompts turn your agent from reactive to strategic. The agent thinks through the whole task, spots dependencies and risks, then executes methodically. In LangGraph this becomes your graph structure. In ADK/AutoGPT this becomes the agent's execution roadmap.