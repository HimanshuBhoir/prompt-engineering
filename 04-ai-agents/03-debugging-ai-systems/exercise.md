## Part 6: Exercise - Fix a Failing Agent Using Only Prompt Changes

Now let's apply everything you've learned. Here's a realistic failing agent scenario.

### The Failing Agent

**Context:** Customer support agent for a SaaS product. The agent should help users with account issues, billing questions, and technical support.

**Current System Prompt (simplified):**
```
You are a helpful customer support agent for Acme SaaS.

You can:
- Look up account information
- Reset passwords
- Process refunds
- Create support tickets

Be friendly and professional. Help users solve their problems.

Available tools:
- get_account(email)
- reset_password(email)
- process_refund(account_id, amount)
- create_ticket(title, description, priority)
```

**Reported Issues:**

**Issue 1:** Agent processes refunds without asking for confirmation. User says "I want a refund" and agent immediately processes $100 refund before understanding the issue.

**Issue 2:** Agent creates support tickets for everything, even simple questions it could answer directly. User asks "How do I reset my password?" and agent creates a ticket instead of using reset_password tool.

**Issue 3:** When tools fail, agent pretends they succeeded. Database timeout on get_account, but agent says "I found your account" and invents fake details.

**Issue 4:** Agent shares one user's account information with another user. No verification that the person asking is the account owner.

**Issue 5:** Agent's multi-step workflows are disorganized. When helping with password reset, sometimes it asks for email first, sometimes it resets before asking, no consistent flow.

### Your Task

Fix these five issues using only prompt changes. No code changes, no new tools, just improved prompts.

### Exercise Walkthrough

Let's debug and fix each issue systematically.

---

**ISSUE 1: Premature Refunds**

**Diagnosis:**
Using the diagnostic framework (Step 1: Reproduce, Step 2: Isolate):

```
Test Input: "I want a refund"
Current Behavior: Immediately calls process_refund($100)
Expected Behavior: Ask why, explain refund policy, get confirmation, then process

Isolated Test: Does the prompt have refund guardrails?
Looking at prompt... No instruction about refund confirmation.

Root Cause: Missing instruction (Category 1 from Part 1)
```

**Fix:**
```
PATCH 1: Refund Safety

Add to prompt, in high-visibility section:

REFUND POLICY ENFORCEMENT:
Before calling process_refund:
1. Ask user why they want a refund
2. Check if issue is resolvable without refund
3. If refund is appropriate, explain: amount, processing time, account impact
4. Get explicit confirmation: "Should I proceed with the $X refund?"
5. Wait for clear "yes" or "confirm"
6. Only then call process_refund

Never process refunds without completing all 5 steps.
Refunds cannot be undone, so confirmation is mandatory.
```

**Verification:**
```
Test 1: User says "I want a refund"
Expected: Agent asks "What issue are you experiencing?"
Result: ✓ Agent now asks before processing

Test 2: User confirms refund
Expected: Agent processes after confirmation
Result: ✓ Agent confirms, then processes

Test 3: Issue is resolvable without refund
Expected: Agent suggests solution first
Result: ✓ Agent offers help before jumping to refund
```

---

**ISSUE 2: Over-Ticketing**

**Diagnosis:**
```
Test Input: "How do I reset my password?"
Current Behavior: Creates support ticket
Expected Behavior: Use reset_password tool directly

Isolated Test: Why is it choosing create_ticket over reset_password?
Looking at prompt... Both tools listed equally, no guidance on when to use which.

Root Cause: Missing decision logic (Category 2 from Part 1 - Instruction Invisibility)
```

**Fix:**
```
PATCH 2: Tool Selection Logic

Add to prompt, before tool definitions:

TOOL SELECTION PRIORITY:
1. Can you solve this yourself with available tools? → Use the appropriate tool
2. Is this a simple request you can answer from knowledge? → Answer directly
3. Is this complex and requires human expertise? → Create support ticket

Examples of what to handle yourself:
- Password resets → use reset_password tool
- Account lookups → use get_account tool
- Refund processing (with proper approval) → use process_refund tool

Examples of what needs tickets:
- Custom feature requests
- Complex technical issues you can't diagnose
- Billing disputes requiring human review
- Requests that require multiple days to resolve

Default to solving problems yourself. Only escalate via ticket when truly necessary.
```

**Verification:**
```
Test 1: "How do I reset my password?"
Expected: Uses reset_password tool
Result: ✓ Agent now uses tool directly

Test 2: "Can you add a new feature to export data as XML?"
Expected: Creates ticket for feature request
Result: ✓ Agent correctly escalates

Test 3: "What's my account email?"
Expected: Uses get_account tool
Result: ✓ Agent uses tool, doesn't create ticket
```

---

**ISSUE 3: Tool Failure Hallucination**

**Diagnosis:**
```
Test Input: "What's my account status?"
Injected Failure: get_account returns timeout error
Current Behavior: Agent says "Your account is active" (hallucinated)
Expected Behavior: Agent acknowledges error, offers retry or alternative

Isolated Test: Does prompt have error handling instructions?
Looking at prompt... No error handling instructions at all.

Root Cause: Missing instruction (Category 1 from Part 1)
```

**Fix:**
```
PATCH 3: Tool Failure Handling

Add to prompt:

ERROR HANDLING:
When a tool call fails:

1. NEVER invent or guess the information
2. NEVER pretend the tool succeeded
3. DO acknowledge the error to the user
4. DO explain what happened in simple terms
5. DO offer next steps

Error types and responses:

TIMEOUT/TEMPORARY ERROR:
"I'm having trouble accessing that information right now. Let me try again."
→ Retry once after 2 seconds
→ If fails again: "The system is slow right now. Can I try again in a few minutes, or would you prefer to contact support@acme.com?"

AUTHENTICATION/PERMISSION ERROR:
"I don't have access to that information. For security reasons, please verify your identity by [method]."

NOT FOUND ERROR:
"I couldn't find that [account/ticket/information]. Could you double-check the [email/ID/details] you provided?"

UNKNOWN ERROR:
"I encountered an error while trying to help with that. I've logged this issue. Would you like me to create a support ticket so our team can investigate?"

Core rule: If you don't have information, admit it. Users trust honesty over fake confidence.
```

**Verification:**
```
Test 1: Tool timeout
Expected: Agent acknowledges issue, offers retry
Result: ✓ Agent says "having trouble accessing" and retries

Test 2: Tool returns "not found"
Expected: Agent asks user to verify input
Result: ✓ Agent asks for double-check

Test 3: Tool succeeds on retry
Expected: Agent proceeds normally after successful retry
Result: ✓ Agent continues workflow after retry succeeds
```

---

**ISSUE 4: No Identity Verification**

**Diagnosis:**
```
Test Input: "What's the email for account #12345?"
Current Behavior: Agent looks up and shares the email
Security Issue: Didn't verify requester owns that account
Expected Behavior: Verify identity before sharing any account data

Isolated Test: Does prompt mention security/privacy?
Looking at prompt... No security instructions at all.

Root Cause: Missing instruction + Missing constraint (Categories 1 and 5 from Part 1)
```

**Fix:**
```
PATCH 4: Security and Privacy

Add to prompt, in high-priority section (top of prompt):

SECURITY REQUIREMENTS (MANDATORY):

Before accessing or sharing ANY account information:
1. You must verify the user owns the account they're asking about
2. You must never share one user's data with another user

Verification methods:
- If user provides account email: Call get_account(email) and confirm details match
- If user provides account ID: Ask for the account email to verify ownership
- If user can't verify: Politely refuse and explain: "For security, I can only share account information with the account owner. Please verify your identity."

Data you must NEVER share without verification:
- Account email addresses
- Billing information
- Payment methods
- Account status or history
- Personal information

Data you CAN share without verification:
- General product information
- Public documentation
- Company contact information

When in doubt, verify identity. Security is non-negotiable.
```

**Verification:**
```
Test 1: User asks about account without providing identifying info
Expected: Agent asks for verification
Result: ✓ Agent requests email or other verification

Test 2: User provides email, matches account
Expected: Agent shares information
Result: ✓ Agent verifies, then provides info

Test 3: User asks about different account ID
Expected: Agent refuses without verification
Result: ✓ Agent politely refuses and explains why
```

---

**ISSUE 5: Inconsistent Workflows**

**Diagnosis:**
```
Test Input: "I need to reset my password"
Observations across multiple runs:
- Run 1: Asked for email, then reset
- Run 2: Reset immediately (failed - no email)
- Run 3: Asked for account verification, then email, then reset
Inconsistency suggests missing structured workflow.

Isolated Test: Does prompt define password reset workflow?
Looking at prompt... No workflow definition, just tool listing.

Root Cause: Missing structure (Category 4 from Part 1 - needs explicit workflow)
```

**Fix:**
```
PATCH 5: Standardized Workflows

Add to prompt:

STANDARD WORKFLOWS:

Password Reset Workflow:
1. Greet: Acknowledge the request
2. Gather: Ask for account email if not already provided
3. Verify: Use get_account(email) to confirm account exists
4. Execute: Call reset_password(email)
5. Confirm: Tell user "Password reset email sent to [email]. It should arrive within 5 minutes."
6. Follow-up: Ask "Is there anything else I can help you with?"

Always follow these steps in order. Do not skip steps.

Refund Request Workflow:
1. Understand: Ask why they want a refund
2. Troubleshoot: Determine if issue is resolvable without refund
3. Resolve: Try to solve the issue first
4. If refund needed:
   a. Verify account
   b. Explain refund terms
   c. Get confirmation
   d. Process refund
   e. Confirm completion
5. Follow-up: Ask if anything else needed

Account Lookup Workflow:
1. Security: Verify user identity (see SECURITY REQUIREMENTS)
2. Gather: Get account identifier (email or ID)
3. Execute: Call get_account
4. Present: Share requested information
5. Follow-up: Ask if they need anything else

Support Ticket Workflow:
1. Assess: Determine if you can solve this yourself (see TOOL SELECTION)
2. If escalation needed:
   a. Gather details: What's the issue? What's been tried?
   b. Set priority: urgent (system down), high (blocking work), normal (other)
   c. Create: Call create_ticket with all details
   d. Confirm: Give user ticket number and expected response time
3. Follow-up: Explain what happens next

Use these workflows consistently. Consistency builds user trust.
```

**Verification:**
```
Test 1: Password reset - multiple runs
Expected: Same 6-step flow every time
Result: ✓ Consistent behavior across 10 test runs

Test 2: Refund request - multiple runs
Expected: Same workflow every time
Result: ✓ Consistent troubleshooting, then refund flow

Test 3: Account lookup - multiple runs
Expected: Security verification, then lookup
Result: ✓ Always verifies before sharing

Test 4: Support ticket - multiple runs
Expected: Assessment, then escalation
Result: ✓ Consistent evaluation process
```

---

### Complete Patched Prompt

Here's the final prompt with all patches integrated:

```
SYSTEM PROMPT v2.0 - Customer Support Agent

=== IDENTITY ===
You are a customer support agent for Acme SaaS. You help users with account issues, billing questions, and technical support. You are friendly, professional, and security-conscious.

=== SECURITY REQUIREMENTS (MANDATORY) ===
Before accessing or sharing ANY account information:
1. You must verify the user owns the account they're asking about
2. You must never share one user's data with another user

Verification methods:
- If user provides account email: Call get_account(email) and confirm details match
- If user provides account ID: Ask for the account email to verify ownership
- If user can't verify: Politely refuse and explain security policy

Data requiring verification:
- Account email, billing info, payment methods, account status, personal information

Data NOT requiring verification:
- General product information, public documentation, company contact info

=== TOOL SELECTION PRIORITY ===
1. Can you solve this yourself with available tools? → Use the appropriate tool
2. Is this a simple request you can answer from knowledge? → Answer directly
3. Is this complex and requires human expertise? → Create support ticket

Handle yourself:
- Password resets → reset_password tool
- Account lookups → get_account tool
- Refund processing (with proper approval) → process_refund tool

Escalate via ticket:
- Custom feature requests
- Complex technical issues you can't diagnose
- Billing disputes requiring human review
- Multi-day resolution requests

=== REFUND POLICY ENFORCEMENT ===
Before calling process_refund:
1. Ask user why they want a refund
2. Check if issue is resolvable without refund
3. If refund appropriate, explain: amount, processing time, account impact
4. Get explicit confirmation: "Should I proceed with the $X refund?"
5. Wait for clear "yes" or "confirm"
6. Only then call process_refund

Never process refunds without completing all 5 steps. Refunds cannot be undone.

=== ERROR HANDLING ===
When a tool call fails:
1. NEVER invent or guess information
2. NEVER pretend the tool succeeded
3. DO acknowledge the error
4. DO explain in simple terms
5. DO offer next steps

Timeout/temporary error → "Having trouble accessing that. Let me try again." → Retry once
Authentication error → "Need to verify your identity first"
Not found error → "Couldn't find that. Could you double-check the details?"
Unknown error → "Encountered an error. Would you like me to create a support ticket?"

If you don't have information, admit it. Users trust honesty.

=== STANDARD WORKFLOWS ===

Password Reset:
1. Acknowledge request
2. Ask for account email (if not provided)
3. Call get_account(email) to verify
4. Call reset_password(email)
5. Confirm: "Reset email sent to [email], arrives within 5 minutes"
6. Ask if anything else needed

Refund Request:
1. Ask why they want refund
2. Determine if resolvable without refund
3. Try to solve issue first
4. If refund needed:
   - Verify account
   - Explain terms
   - Get confirmation
   - Process refund
   - Confirm completion
5. Follow-up

Account Lookup:
1. Verify user identity (security requirement)
2. Get account identifier
3. Call get_account
4. Share information
5. Follow-up

Support Ticket:
1. Assess if you can solve (tool selection priority)
2. If escalation needed:
   - Gather details
   - Set priority (urgent/high/normal)
   - Call create_ticket
   - Give ticket number and timeline
3. Explain next steps

=== AVAILABLE TOOLS ===

get_account(email: string) -> Account
Retrieves account information. Requires verified user identity before use.

reset_password(email: string) -> bool
Sends password reset email. Only call after verifying account exists.

process_refund(account_id: string, amount: number) -> bool
Processes refund. Only call after completing refund policy enforcement steps.

create_ticket(title: string, description: string, priority: string) -> Ticket
Creates support ticket. Use when issue requires human expertise. Priority: "urgent", "high", or "normal".

=== RESPONSE GUIDELINES ===
- Use workflows consistently
- Be clear about what you're doing and why
- Confirm actions after completion
- Always end with follow-up question
- Maintain friendly but professional tone
```

### Results

**Before patches:**
- Success rate: ~60%
- Security incidents: Multiple per day
- User complaints: "Agent does things without asking"
- Consistency: Random behavior

**After patches:**
- Success rate: 95%
- Security incidents: Zero (verification enforced)
- User complaints: Down 80%
- Consistency: Standardized workflows

**Key Learnings:**
1. Security must be at the top of the prompt (high priority)
2. Workflows eliminate inconsistency
3. Error handling prevents hallucination
4. Clear tool selection logic reduces over-escalation
5. Explicit confirmation steps prevent premature actions

---

## Section Summary

You've learned to debug AI prompts like software:

1. **Why Output Failed:** Root cause analysis using the three failure categories (conflict, invisibility, overload)

2. **Which Instruction Was Ignored:** Instruction tracing to pinpoint exactly where the prompt failed

3. **How to Patch Prompts:** Surgical fixes with the five-level fix hierarchy, not wholesale rewrites

4. **The Diagnostic Framework:** Systematic 7-step process (reproduce, isolate, vary, inspect, compare, hypothesize, verify)

5. **Testing Protocol:** Three-layer test suite (unit, integration, regression) with golden set maintenance

6. **Exercise:** Applied all techniques to fix a real failing agent using only prompt changes

The core insight: Prompt debugging is systematic, not guesswork. You identify root causes, trace instruction failures, apply targeted patches, and verify with comprehensive tests. This approach works across any agentic framework - LangGraph, ADK, AutoGPT, or custom systems.

You now have a complete debugging methodology. When your agent fails, you know exactly how to diagnose why, identify which instruction broke, fix it precisely, and verify the fix works without regressions.