# Multi-Step Reasoning: Chaining and Workflows

This is about orchestrating complex tasks that require multiple reasoning cycles, where the output of one step becomes the input to the next. It's the difference between an agent that does one thing and an agent that accomplishes sophisticated goals.

**The Challenge:** Real tasks aren't single-shot. "Analyze our competitor's pricing and recommend adjustments" requires research, analysis, synthesis, calculation, and presentation. Each step builds on the previous one.

## Sequential Chaining

The simplest pattern: Step 1 completes, its output feeds Step 2, and so on.

```
For multi-step tasks, execute in sequence:

STEP N:
<thinking>
Input from previous step: [what you learned]
Current objective: [what this step accomplishes]
Output needed for next step: [what the next step needs]
</thinking>

<action>[tool call or reasoning]</action>

After each step, explicitly state: "Step N complete. Output: [summary]. Proceeding to Step N+1."
```

**Example workflow:** "Find our top 10 customers and send them holiday thank-you emails."

```
STEP 1: Identify top customers
<tool_call>
<tool_name>query_database</tool_name>
<parameters>{"query": "SELECT customer_id, total_revenue FROM customers ORDER BY total_revenue DESC LIMIT 10"}</parameters>
</tool_call>

[Results return: list of 10 customer IDs]

Step 1 complete. Output: 10 customer IDs identified. Proceeding to Step 2.

STEP 2: Get customer details for personalization
<tool_call>
<tool_name>get_customer_details</tool_name>
<parameters>{"customer_ids": [101, 205, 308, ...]}</parameters>
</tool_call>

[Results return: names, companies, purchase history]

Step 2 complete. Output: Full customer profiles retrieved. Proceeding to Step 3.

STEP 3: Generate personalized emails
<thinking>
I have customer names and their purchase history.
I'll use the email template tool with personalization fields.
Each email should reference their specific purchases.
</thinking>

<tool_call>
<tool_name>generate_emails</tool_name>
<parameters>{
  "template": "holiday_thanks",
  "recipients": [
    {"name": "Sarah Chen", "company": "Tech Corp", "recent_purchase": "Enterprise License"},
    ...
  ]
}</parameters>
</tool_call>

Step 3 complete. Output: 10 personalized emails generated. Proceeding to Step 4.

STEP 4: Send emails with tracking
<tool_call>
<tool_name>send_bulk_email</tool_name>
<parameters>{"emails": [...], "track_opens": true}</parameters>
</tool_call>

Workflow complete. 10 thank-you emails sent to top customers. Tracking enabled for engagement metrics.
```

Notice how each step explicitly passes information forward. The agent isn't jumping around randomly.

## Conditional Branching

Real workflows have decision points:

```
In workflows with conditional logic:

STEP N: [action]
RESULT EVALUATION:
- If [condition A]: proceed to Step X
- If [condition B]: proceed to Step Y
- If [condition C]: abort workflow and inform user

Make the branch decision explicit in <thinking> tags.
```

**Example:** Customer support escalation

```
STEP 1: Check account status
<tool_call>
<tool_name>get_account</tool_name>
<parameters>{"email": "user@example.com"}</parameters>
</tool_call>

[Returns: account_status = "suspended"]

<thinking>
Account is suspended. Workflow branches based on suspension reason.
Let me check why it's suspended before proceeding.
</thinking>

STEP 2: Get suspension details
<tool_call>
<tool_name>get_suspension_info</tool_name>
<parameters>{"account_id": "12345"}</parameters>
</tool_call>

[Returns: reason = "payment_failed", amount_due = "$49"]

<thinking>
Suspension reason is failed payment.
BRANCH DECISION:
- Amount < $100: I can offer to retry payment → proceed to Step 3A
- Amount >= $100: requires manager approval → proceed to Step 3B

Amount is $49, so taking branch A.
</thinking>

STEP 3A: Retry payment
<tool_call>
<tool_name>retry_payment</tool_name>
<parameters>{"account_id": "12345"}</parameters>
</tool_call>

[Success]

STEP 4A: Reactivate account
<tool_call>
<tool_name>reactivate_account</tool_name>
<parameters>{"account_id": "12345"}</parameters>
</tool_call>

Workflow complete via branch A. Payment processed, account reactivated.
```

## Parallel Execution

Some steps can happen simultaneously:

```
When steps are independent, mark them as PARALLEL:

PARALLEL BLOCK:
- STEP 3A: [independent action 1]
- STEP 3B: [independent action 2]
- STEP 3C: [independent action 3]

Wait for all parallel steps to complete before proceeding to STEP 4.
```

**Example:** Gathering data from multiple sources

```
STEP 2 complete: I have the customer ID.

PARALLEL BLOCK (all needed for analysis):
STEP 3A: Get purchase history
<tool_call>
<tool_name>get_purchase_history</tool_name>
<parameters>{"customer_id": "12345"}</parameters>
</tool_call>

STEP 3B: Get support ticket history
<tool_call>
<tool_name>get_support_tickets</tool_name>
<parameters>{"customer_id": "12345"}</parameters>
</tool_call>

STEP 3C: Get engagement metrics
<tool_call>
<tool_name>get_engagement_data</tool_name>
<parameters>{"customer_id": "12345"}</parameters>
</tool_call>

[All three complete]

STEP 4: Synthesize complete customer profile
<thinking>
I now have purchase history (12 orders, $4,500 total), support tickets (2 tickets, both resolved), and engagement (opens 80% of emails, clicks 30%).
Proceeding to analysis step...
</thinking>
```

Note: In LangGraph, parallel execution is a graph feature. In linear systems like ADK, you'd mark them as parallel conceptually but execute sequentially.

## Loop Patterns

Some workflows repeat until a condition is met:

```
LOOP workflows when you need to iterate:

LOOP: while [condition not met] and [attempts < max]
  STEP N: [action]
  CHECK: [evaluate condition]
  If condition met: exit loop
  If max attempts reached: escalate
  Otherwise: continue loop

Always set a max iteration limit to prevent infinite loops.
```

**Example:** Polling for a long-running job

```
STEP 1: Submit report generation job
<tool_call>
<tool_name>generate_report</tool_name>
<parameters>{"type": "annual_sales", "year": 2024}</parameters>
</tool_call>

[Returns: job_id = "abc123", status = "processing"]

LOOP: Check job status (max 10 attempts, 30 second intervals)

ITERATION 1:
<tool_call>
<tool_name>check_job_status</tool_name>
<parameters>{"job_id": "abc123"}</parameters>
</tool_call>
[Returns: status = "processing"]
Waiting 30 seconds...

ITERATION 2:
<tool_call>
<tool_name>check_job_status</tool_name>
<parameters>{"job_id": "abc123"}</parameters>
</tool_call>
[Returns: status = "processing"]
Waiting 30 seconds...

ITERATION 3:
<tool_call>
<tool_name>check_job_status</tool_name>
<parameters>{"job_id": "abc123"}</parameters>
</tool_call>
[Returns: status = "complete", download_url = "https://..."]

Loop complete after 3 iterations. Report ready.

STEP 2: Download and present report
```

## State Accumulation

As workflows progress, the agent accumulates state:

```
Throughout multi-step workflows, maintain a state summary:

<workflow_state>
COMPLETED_STEPS: [list of finished steps]
CURRENT_DATA: [key information gathered so far]
PENDING_STEPS: [what remains]
DECISIONS_MADE: [branches taken, why]
</workflow_state>

Update this after each major step. Use it to maintain context across many tool calls.
```

This is critical in long workflows where the agent might lose track of what it's doing.

## Error Propagation in Chains

When a step in a chain fails:

```
In chained workflows, handle failures at each step:

If STEP N fails:
1. Determine if subsequent steps can proceed without STEP N's output
2. If NO: abort workflow, explain what was accomplished and what failed
3. If YES: continue but mark workflow as PARTIAL SUCCESS

Document both completed and failed steps in your final response.
```

**Example:**

```
STEP 1: Get customer data ✓
STEP 2: Enrich with external API ✗ FAILED (API down)
STEP 3: Calculate metrics - PROCEEDING with partial data
STEP 4: Generate report ✓

Workflow completed with partial success. Report generated using internal data only. External enrichment data unavailable due to API outage. Report accuracy is good but missing demographic insights.
```

## Optimization: Caching Intermediate Results

For workflows that might need re-execution:

```
For expensive operations in workflows, cache intermediate results:

<cache key="customer_123_purchase_history">
[Large dataset here]
</cache>

If workflow needs to restart at STEP 5, retrieve cached data instead of re-querying.

Note: Only cache data that won't change during workflow execution.
```

---

Multi-step reasoning is about maintaining coherent state across many actions, making smart branching decisions, and recovering gracefully when individual steps fail. Your prompt gives the agent a mental model: "I'm in a workflow, here's where I am, here's what I've learned, here's what comes next."

In LangGraph this becomes your graph topology. In ADK/AutoGPT this becomes the agent's execution trace.