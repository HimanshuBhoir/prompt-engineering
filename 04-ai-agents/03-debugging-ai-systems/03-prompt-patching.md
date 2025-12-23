## Part 3: How to Patch Prompts

Patching is surgical. You don't rewrite everything when one part fails. You make targeted fixes.

### The Patching Principles

**1. Minimal Effective Change**
Fix the specific failure without touching unrelated parts. If the model is using the wrong tool, fix the tool selection instruction. Don't also rewrite your greeting and closing instructions.

**2. Test the Patch in Isolation**
Before deploying a patch, verify it fixes the problem without breaking other things. Add one patch at a time.

**3. Document Why**
When you patch, write a comment explaining what failed and why this fixes it. Six months later you'll forget.

**4. Version Control**
Keep old versions. Sometimes a patch fixes one issue but regresses another. You need to be able to roll back.

### Patch Types

**Type 1: Clarification Patch**
The instruction was too vague. Make it specific.

```
BEFORE:
"Be helpful when users have problems"

AFTER:
"When users report problems:
1. Acknowledge the issue: 'I understand you're experiencing [problem]'
2. Ask clarifying questions if needed
3. Provide solution or escalate if unsolvable"
```

**Type 2: Constraint Patch**
The model had too much freedom. Add guardrails.

```
BEFORE:
"Use search_database to find customer information"

AFTER:
"Use search_database to find customer information.
CONSTRAINTS:
- Maximum 50 results per query
- If 0 results, try broader search terms before giving up
- Never search with only 1-2 characters (too broad)"
```

**Type 3: Prioritization Patch**
Conflicting instructions need ordering.

```
BEFORE:
[Multiple instructions with no priority]

AFTER:
"When instructions conflict, priority order:
1. Security and privacy rules (NEVER compromise)
2. Data accuracy requirements
3. User experience preferences
4. Performance optimization"
```

**Type 4: Edge Case Patch**
You found a scenario not covered. Add it.

```
BEFORE:
"If tool returns empty results, inform user"

AFTER:
"If tool returns empty results, inform user.

EDGE CASE: If search_users returns empty but you know the user exists from conversation context, the user might be searching wrong. Suggest: 'I don't see results for that search. Did you mean [alternative based on context]?'"
```

**Type 5: Enforcement Patch**
A rule exists but isn't enforced. Add teeth.

```
BEFORE:
"Verify user identity before account changes"

AFTER:
"Verify user identity before account changes.

ENFORCEMENT:
Before calling update_account, cancel_subscription, or change_payment_method:
1. Check: Has verify_user been called this session?
2. If NO, call verify_user first
3. If verify_user returns false, STOP and respond 'I need to verify your identity first'
4. Only proceed if verify_user returns true

Do not skip verification under any circumstances."
```

### The Patch Testing Protocol

Don't just apply a patch and hope. Test it:

**1. Reproduce the original failure**
Run the same input that caused the bug. Confirm it still fails with the old prompt.

**2. Apply the patch**
Add your fix to the prompt.

**3. Test the original failure case**
Does the patch fix it? If no, iterate on the patch.

**4. Test adjacent cases**
Run similar scenarios. Make sure the patch doesn't break related functionality.

**5. Test unrelated cases**
Run your standard test suite. Make sure the patch doesn't have unexpected side effects.

**6. Regression test over time**
A patch that works today might stop working if the model's behavior changes or if you add other instructions. Re-test periodically.

### The "Diff" Approach

When patching, show exactly what changed:

```
PATCH #7: Fix email verification bug
DATE: 2024-12-21
ISSUE: Agent sending emails without verifying recipient field

CHANGE:
- REMOVED: "Verify recipient before sending email"
+ ADDED: "Before calling send_email tool:
  1. Check that 'to' parameter is not empty
  2. Check that 'to' parameter contains '@' symbol
  3. If either check fails, respond 'I need a valid email address before I can send this'
  4. Only call send_email if both checks pass"

RATIONALE: Original instruction too vague, model didn't know what "verify" meant

TEST CASES:
✓ Empty recipient → correctly refuses
✓ Invalid format → correctly refuses  
✓ Valid email → sends successfully
```

This documents your debugging process.

### Patch Accumulation Strategy

As you add patches, your prompt grows. Eventually it's unmaintainable. Strategies:

**Strategy 1: Consolidation**
Every 5-10 patches, consolidate related patches into coherent sections.

```
PATCHES 12, 15, 18 all addressed tool selection issues.

CONSOLIDATED INTO:
"Tool Selection Logic:
[Unified set of rules incorporating all three patches]"
```

**Strategy 2: Abstraction**
Multiple specific patches reveal a general pattern. Abstract it.

```
PATCHES:
- "Don't use send_email without verifying recipient"
- "Don't use delete_account without user confirmation"
- "Don't use charge_card without checking balance"

ABSTRACTED:
"High-risk operations checklist:
These operations require pre-checks:
- send_email → verify recipient field
- delete_account → get user confirmation
- charge_card → verify sufficient balance
[Continue pattern]

Before ANY high-risk operation, perform its required pre-check."
```

**Strategy 3: Modularization**
Break your monolithic prompt into loadable modules. Load only relevant modules per task type.

```
[CORE_INSTRUCTIONS] - always loaded
[TOOL_DEFINITIONS] - always loaded
[WORKFLOW_SALES] - loaded for sales tasks only
[WORKFLOW_SUPPORT] - loaded for support tasks only
[SAFETY_RULES] - always loaded
```

This keeps prompts manageable even with many patches.

### Anti-Patterns in Patching

**Anti-Pattern 1: Redundancy**
Adding the same instruction multiple times in different words doesn't help. The model still ignores it or still follows it. Redundancy wastes tokens.

**Anti-Pattern 2: Contradiction**
New patch contradicts old instruction. Model behavior becomes random. Always check for conflicts.

**Anti-Pattern 3: Over-Specification**
Patching by adding 1000 examples. The model can't learn patterns from prompts; it follows instructions. Show patterns, don't enumerate every case.

**Anti-Pattern 4: Blame-Shifting**
"The model is bad at X, so I'll just tell it to try harder." Doesn't work. If the model fails at X, either: (a) give it better instructions for X, (b) give it a tool that does X, or (c) don't ask it to do X.

### When to Rewrite vs Patch

**Patch when:**
- The failure is isolated to one component
- The fix is additive (adding missing instruction)
- The fix is clarifying (making vague instruction specific)
- Most of your prompt works fine

**Rewrite when:**
- Multiple components conflict
- The architecture is fundamentally wrong (wrong task decomposition)
- Patches accumulate to >30% of prompt length
- You can't find where to patch because the structure is unclear

Rewriting is rare if you architect well from the start (Phase 4 Section 2 on system prompt architecture helps here).

---

