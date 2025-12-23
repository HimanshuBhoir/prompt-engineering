## Part 2: Which Instruction Was Ignored

Once you know WHY something failed, you need to pinpoint WHICH instruction didn't fire. This is instruction tracing.

### The Tracing Method

**Step 1: List all relevant instructions**
From your system prompt, extract every instruction that should have applied to the failed scenario.

Example scenario: Model sent email to wrong person.

Relevant instructions:
- "Verify recipient before sending email"
- "Use get_contact_info tool to retrieve email addresses"
- "Never guess email addresses"
- "Confirm with user if ambiguity about recipient"

**Step 2: Check execution order**
Did the model follow any of these? In what order?

Execution trace from model outputs:
- ✗ Did not call get_contact_info
- ✗ Did not verify recipient
- ✗ Did not confirm with user
- ✓ Called send_email directly

**Step 3: Identify the first missed instruction**
"Verify recipient before sending email" should have fired first. This is your primary failure point.

**Step 4: Determine why it didn't fire**
Look at how this instruction is written:

```
Before sending emails, verify the recipient is correct.
```

Problems:
- "Verify" is vague - how? Using what tool?
- "Recipient is correct" is subjective - correct according to what?
- No explicit blocking language - model might interpret this as a suggestion

### Making Instructions Traceable

Write instructions so you can see if they fired:

**Bad (untraceable):**
```
Be careful when handling user data.
```

You can't tell if the model "was careful." It's not a measurable action.

**Good (traceable):**
```
Before accessing user data:
1. Call verify_permissions(user_id, data_type)
2. If result is false, respond "I don't have permission to access that"
3. If result is true, proceed with data access

After accessing data, log the action using log_access(user_id, data_type, timestamp)
```

Now you can trace: Did it call verify_permissions? Did it check the result? Did it log?

### Conditional Instruction Tracing

Conditionals are where most tracing breaks down.

**Bad conditional:**
```
If the user seems frustrated, be more empathetic.
```

Untraceable: What does "seems frustrated" mean? How empathetic is "more empathetic"?

**Good conditional:**
```
If the user's message contains words like "frustrated," "annoyed," "upset," or uses all caps, or explicitly says they're having a bad experience:
- Acknowledge their frustration explicitly: "I understand this is frustrating"
- Offer escalation: "Would you like me to connect you with a supervisor?"
- Avoid defensive language
```

Now you can trace: Did the model detect frustration signals? Did it acknowledge? Did it offer escalation?

### The Instruction Chain Technique

For complex behaviors, write instructions as explicit chains:

```
Workflow: Account Deletion
1. Check if user is authenticated → if no, request authentication
2. Explain what deletion does → must include: data loss, irreversibility, alternatives
3. Ask "Are you absolutely sure?" → wait for explicit "yes" or "confirm"
4. If user confirms, call delete_account(account_id)
5. Send confirmation email using send_email(to, subject, body)
6. Respond with deletion confirmation and support contact

Each step must complete before next step begins.
```

When debugging, you can trace exactly which step failed:
- Step 1: ✓ Authentication checked
- Step 2: ✗ Explanation was incomplete, didn't mention irreversibility
- Step 3: ✗ Skipped, went straight to deletion
- Step 4: ✓ Called delete_account
- Step 5: ✗ Forgot confirmation email
- Step 6: ✓ Responded with confirmation

You can see steps 2, 3, and 5 failed. Now fix those specific points.

### The "Echo Back" Debugging Technique

Add this to your prompt temporarily while debugging:

```
After reading each instruction, before executing, output:
<instruction_check>
Instruction: [copy the instruction you're about to follow]
Applies to current situation: [yes/no]
If yes, action I'll take: [specific action]
</instruction_check>
```

This makes the model's reasoning visible. You can see which instructions it even considers.

Example output:
```
<instruction_check>
Instruction: Verify recipient before sending email
Applies to current situation: yes
Action I'll take: I'll call get_contact_info to retrieve the email address for John Smith
</instruction_check>

<tool_call>
<tool_name>get_contact_info</tool_name>
<parameters>{"name": "John Smith"}</parameters>
</tool_call>
```

Now you know the instruction fired. If output doesn't include the instruction_check, you know it didn't fire.

### Priority Conflicts

Sometimes multiple instructions apply and conflict:

```
Instruction A: "Respond quickly, don't make users wait"
Instruction B: "Always verify information before responding"
```

User asks: "What's my account balance?"

The model has to choose: respond quickly (might give wrong info) or verify first (slower but accurate). Without explicit priority, it's a coin flip.

**Fix with explicit priority:**
```
Instruction priorities (highest to lowest):
1. Accuracy and correctness
2. Security and privacy
3. Following user requests
4. Response speed
5. Conversational polish

When instructions conflict, follow the higher priority.
```

Now you can trace: Did the model face a conflict? Which instruction won? Was it the correct priority?

### Instruction Coverage Analysis

Make a table:

| Scenario | Expected Behavior | Instruction in Prompt | Works? |
|----------|-------------------|----------------------|--------|
| User asks to delete account | Confirm first, then delete | "Confirm destructive actions" | ✗ Too vague |
| User provides partial info | Ask for missing info | "Gather all required info" | ✓ Works |
| Tool returns error | Try alternative approach | "If tool fails, try X" | ✗ No instruction exists |

This reveals gaps. Scenario 3 has no instruction at all - you can't trace what doesn't exist.

### The "Negative Test" Technique

Write instructions that explicitly forbid common mistakes:

```
DO NOT:
- Send emails without verifying recipient
- Delete data without confirmation
- Share one user's data with another user
- Proceed when required fields are missing
- Retry the same failing action more than 3 times
```

When debugging, check: Did the model do any of these forbidden things? If yes, that specific prohibition didn't work. Make it stronger.

---