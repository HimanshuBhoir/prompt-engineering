# Failure Recovery Prompts

Failure recovery is how you teach your agent to handle errors gracefully instead of breaking or hallucinating. In production, things fail: APIs timeout, databases return empty results, tools get rate-limited. Your agent needs explicit instructions on what to do when this happens.

**The Reality:** Without recovery instructions, agents do three terrible things when tools fail: (1) hallucinate success, (2) retry the same failing action infinitely, or (3) give up immediately. All three are bad.

## The Recovery Framework

You need to tell your agent how to detect failure, how to respond, and when to escalate.

```
When a tool call fails, follow this process:

1. DETECT: Recognize the failure type from the error message
2. ANALYZE: Determine if the failure is retryable
3. DECIDE: Choose recovery strategy
4. ACT: Execute recovery
5. ESCALATE: If recovery fails, inform the user

Never pretend a tool succeeded when it failed. Never retry more than 3 times without changing your approach.
```

## Failure Types and Responses

Teach your agent to recognize different failure categories:

```
Tool failures fall into these categories:

TRANSIENT (retry-able):
- Network timeout
- Rate limit hit
- Temporary service unavailability
- Database lock
ACTION: Wait briefly, then retry with same parameters. Max 3 attempts.

PARAMETER ERROR (fixable):
- Invalid format
- Missing required field
- Type mismatch
ACTION: Fix the parameters based on error message, retry once.

LOGICAL ERROR (requires rethinking):
- No results found
- Permission denied
- Resource doesn't exist
ACTION: Revise your approach. Don't retry the same call.

FATAL (can't fix):
- Authentication failure
- Tool not available
- Invalid tool name
ACTION: Inform user immediately. Suggest alternatives if possible.
```

## Practical Example

User: "Get me the sales report for Q4 2024."

**Attempt 1 fails:**
```
<tool_call>
<tool_name>get_report</tool_name>
<parameters>{"report_type": "sales", "period": "Q4 2024"}</parameters>
</tool_call>

[System returns: Error - Invalid period format. Expected YYYY-Qn]
```

**Agent with good recovery:**
```
<thinking>
The tool failed with a parameter error. The date format is wrong.
Error says it expects "YYYY-Qn" format.
Q4 2024 should be "2024-Q4".
This is fixable - I'll retry with correct format.
</thinking>

<tool_call>
<tool_name>get_report</tool_name>
<parameters>{"report_type": "sales", "period": "2024-Q4"}</parameters>
</tool_call>
```

**Attempt 2 succeeds.** Problem solved without bothering the user.

## Retry Logic with Backoff

For transient failures, teach exponential backoff:

```
For transient failures, retry with increasing delays:
- Attempt 1: immediate
- Attempt 2: wait 2 seconds
- Attempt 3: wait 5 seconds
- After 3 failures: escalate to user

Format your retries like:
<retry attempt="2" reason="timeout" delay="2s">
<tool_call>...</tool_call>
</retry>
```

## Alternative Strategies

When direct retry won't work, teach the agent to pivot:

```
If a tool fails logically (no results, permission denied, etc), try alternatives:

PRIMARY FAILS: search_database for "John Smith" returns no results
ALTERNATIVES TO TRY:
1. Search with partial match: "John"
2. Search with different field: email instead of name
3. Try different tool: search_archive instead of search_database
4. Ask user for more specific information

Try up to 2 alternatives before asking the user.
```

**Example:**

```
<thinking>
search_database returned no results for customer "Acme Corp".
This might be a spelling issue or the name might be stored differently.
Let me try a partial match search first.
</thinking>

<tool_call>
<tool_name>search_database</tool_name>
<parameters>{"query": "Acme", "match_type": "partial"}</parameters>
</tool_call>
```

## Graceful Degradation

Sometimes the agent can still accomplish a partial goal:

```
If you cannot complete the full request due to tool failures:
1. Complete what you CAN do
2. Clearly state what you couldn't do and why
3. Offer alternatives

Example: If email_service is down but you've prepared the customer list, tell the user: "I've identified 347 customers to contact and generated their discount codes. However, the email service is currently unavailable. I can: (a) try again in 10 minutes, (b) export the list for you to send manually, or (c) schedule this for later."
```

## Error Context Passing

In multi-step workflows, failures in early steps affect later ones:

```
When a step fails in a multi-step plan:
1. Mark that step as FAILED in your plan tracking
2. Check if subsequent steps depend on the failed step
3. If yes, skip dependent steps and explain why
4. If no, continue with independent steps

Example plan after failure:
STEP 1: Query database ✓ SUCCESS
STEP 2: Enrich data with API ✗ FAILED (API timeout)
STEP 3: Generate report - SKIPPED (depends on step 2)
STEP 4: Send summary email ✓ SUCCESS (independent, used partial data)
```

## User Communication During Recovery

```
When retrying after a failure, keep the user informed:

FIRST FAILURE: Try to recover silently if you can fix it immediately
SECOND FAILURE: Brief note: "The service is slow, retrying..."
THIRD FAILURE: Full explanation: "I've tried 3 times to access the database but it's timing out. This usually means the system is under heavy load. Would you like me to try again in a few minutes, or would you prefer an alternative approach?"
```

Don't spam the user with every retry, but don't stay silent if recovery is taking time.

## The Nuclear Option: State Rollback

For agents that modify data, teach rollback:

```
If you've modified data and a subsequent step fails:
1. Attempt to undo the changes you made
2. Use rollback tools if available
3. Log what was changed for manual review
4. Inform the user of the partial state

Example: "I updated 3 customer records before the email service failed. I've rolled back those updates to keep data consistent. No changes have been saved. Would you like to try again?"
```

## Framework-Specific Notes

**LangGraph:** Your recovery logic can be a dedicated node. Failed tool calls route to a "recovery" node that decides whether to retry, pivot, or escalate.

**ADK/AutoGPT:** Recovery happens in the observation phase. After a tool fails, the agent observes the error, reasons about it in the next think phase, then decides the recovery action.

---

The key principle: failures are expected, not exceptional. Your agent should have a clear decision tree for every type of failure. This turns brittle agents into robust ones.