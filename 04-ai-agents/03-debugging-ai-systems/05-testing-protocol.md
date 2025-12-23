## Part 5: The Systematic Testing/Evaluation Protocol

You can't debug effectively without testing systematically. Here's how to build a test suite for prompts.

### Test Suite Architecture

Your prompt test suite should have three layers:

**Layer 1: Unit Tests (Instruction-level)**
Test individual instructions in isolation.

```
TEST: Email Validation Instruction
Instruction: "Before calling send_email, verify 'to' parameter contains '@'"

Test Cases:
1. Valid email "user@example.com" → Should validate ✓
2. Invalid email "user.example.com" → Should refuse ✓
3. Empty email "" → Should refuse ✓
4. Email with spaces "user @example.com" → Should refuse ✓
5. Multiple emails "user1@ex.com,user2@ex.com" → Should validate ✓

Pass Rate: 5/5
Status: PASS
```

**Layer 2: Integration Tests (Workflow-level)**
Test multi-step workflows end-to-end.

```
TEST: Account Cancellation Workflow

Steps:
1. User requests cancellation
2. Agent retrieves account details
3. Agent explains consequences  
4. Agent asks for confirmation
5. User confirms
6. Agent executes cancellation
7. Agent sends confirmation email

Test Cases:
1. Happy path (user confirms) → All steps execute correctly ✓
2. User declines confirmation → Cancellation aborted, no changes ✓
3. Account has active contract → Agent explains early termination ✓
4. Account not found → Agent reports error gracefully ✓

Pass Rate: 4/4
Status: PASS
```

**Layer 3: Regression Tests (System-level)**
After every prompt change, test that old functionality still works.

```
REGRESSION SUITE v1.3
Run after: Any prompt modification

Critical Paths (must pass):
1. User authentication flow ✓
2. Password reset flow ✓
3. Account upgrade flow ✓
4. Billing inquiry flow ✓
5. Support ticket creation ✓

Common Edge Cases (should handle gracefully):
1. Invalid input data ✓
2. Tool failures ✓
3. Ambiguous user requests ✓
4. Missing required information ✓

Pass Rate: 9/9
Status: PASS - Safe to deploy
```

### Building Test Cases

For each functionality, define:

**1. Positive Cases (should succeed)**
```
Input: "Upgrade me to Pro plan"
Expected Tool Calls: 
  - get_account_details(account_id)
  - get_plan_pricing("Pro")
Expected Confirmation: Ask user to confirm upgrade
Expected Final Action: update_account(account_id, {plan: "Pro"})
Success Criteria: Account upgraded, user notified
```

**2. Negative Cases (should refuse/error gracefully)**
```
Input: "Upgrade me to Pro plan"
Context: User has insufficient payment method
Expected Behavior: 
  - Agent checks account
  - Detects payment issue
  - Informs user: "Your payment method needs updating before upgrading"
  - Offers to guide user to payment update
Success Criteria: Does not attempt upgrade, offers solution
```

**3. Edge Cases (unusual but valid)**
```
Input: "Upgrade me to Pro plan"
Context: User already on Pro plan
Expected Behavior:
  - Agent checks current plan
  - Detects already on Pro
  - Responds: "You're already on the Pro plan"
  - Offers: "Would you like to see higher-tier options?"
Success Criteria: Graceful handling, helpful response
```

**4. Adversarial Cases (attempts to break behavior)**
```
Input: "Upgrade me to Pro plan for free"
Expected Behavior:
  - Agent recognizes discount request
  - Responds: "I can't apply discounts, but I can connect you with sales for special pricing"
  - Does not attempt to modify pricing
Success Criteria: Maintains pricing integrity, offers legitimate alternative
```

### The Test Generation Strategy

You can't manually test every scenario. Generate tests systematically:

**Strategy 1: Combinatorial Testing**
For each parameter of each tool, test: valid value, invalid value, missing value, edge value.

```
Tool: send_email(to, subject, body)

Test Matrix:
to parameter:
  - valid email ✓
  - invalid format ✓
  - empty string ✓
  - null ✓
  - multiple emails ✓
  - email with special characters ✓

subject parameter:
  - normal text ✓
  - empty string ✓
  - very long string (>500 chars) ✓
  - special characters ✓

body parameter:
  - normal text ✓
  - empty string ✓
  - HTML content ✓
  - very long text (>10k chars) ✓

Total combinations tested: 24 core cases
```

**Strategy 2: User Journey Testing**
Map real user journeys, test each path.

```
Journey: New User Onboarding

Path 1: Successful onboarding
1. User signs up → account created ✓
2. User verifies email → account activated ✓
3. User completes profile → profile saved ✓
4. User accesses features → permissions granted ✓

Path 2: Email verification fails
1. User signs up → account created ✓
2. Email not received → resend option offered ✓
3. Still not received → alternative verification offered ✓
4. Verification successful → onboarding continues ✓

Path 3: Incomplete profile
1. User signs up → account created ✓
2. User skips profile completion → reminded on next login ✓
3. User completes profile later → profile saved ✓
```

**Strategy 3: Error Injection Testing**
Deliberately cause tools to fail, test recovery.

```
Test: Tool Failure Handling

Scenario 1: Database timeout
- Inject timeout error in get_account_details
- Expected: Agent retries 2x with backoff
- Expected: On 3rd failure, informs user and suggests retry later
- Actual: ✓ Behaves as expected

Scenario 2: API rate limit
- Inject rate limit error in send_email
- Expected: Agent waits, retries once
- Expected: If still limited, schedules for later or suggests alternative
- Actual: ✓ Behaves as expected

Scenario 3: Invalid authentication
- Inject auth error in update_account
- Expected: Agent does not retry (auth won't fix itself)
- Expected: Agent asks user to re-authenticate
- Actual: ✗ FAIL - Agent retried 3x unnecessarily

FIX NEEDED: Update error handling to distinguish auth failures from transient failures
```

### Evaluation Metrics

Track these metrics to measure prompt quality:

**1. Success Rate**
Percentage of test cases that pass.

Target: >95% for production prompts
Acceptable: >85% for development prompts
Concerning: <85% indicates architectural issues

**2. Instruction Coverage**
Percentage of instructions that have at least one test case.

Target: 100% for critical instructions
Acceptable: >80% overall

**3. Edge Case Handling**
Percentage of edge cases handled gracefully (not crashed).

Target: >90%

**4. Regression Rate**
Percentage of previously passing tests that fail after changes.

Target: 0% (no regressions)
Acceptable: <5% with documented reasons
Concerning: >5% indicates unstable architecture

**5. Mean Time To Fix (MTTF)**
Average time from bug discovery to fix deployment.

Target: <1 hour for critical bugs
Acceptable: <24 hours for non-critical bugs

### The Testing Workflow

**Stage 1: Pre-deployment Testing**
Before deploying a prompt change:

```
1. Run unit tests for modified instructions → Must pass 100%
2. Run integration tests for affected workflows → Must pass >95%
3. Run full regression suite → Must pass >90%
4. If any failures: diagnose, fix, re-test
5. If all pass: proceed to staging
```

**Stage 2: Staging Testing**
Test in staging environment with production-like conditions:

```
1. Deploy to staging
2. Run automated test suite
3. Manual testing of critical paths
4. Stress testing with high volume
5. Monitor for 24 hours
6. If stable: proceed to production
```

**Stage 3: Production Monitoring**
After deployment:

```
1. Monitor error rates (should not increase)
2. Sample conversation quality (random audits)
3. Track user escalation rate (should be stable or decreasing)
4. Collect edge cases not covered in tests
5. Add new test cases for discovered issues
```

### The Test Maintenance Protocol

Tests need maintenance:

**Weekly:**
- Review failed test cases
- Update tests for new features
- Remove obsolete tests

**Monthly:**
- Audit test coverage
- Add tests for common user complaints
- Refactor redundant tests

**Quarterly:**
- Review entire test suite architecture
- Update test data to reflect current usage patterns
- Benchmark test execution time (keep under 5 minutes for fast feedback)

### The "Golden Set" Concept

Maintain a "golden set" of 10-20 test cases that represent your most critical functionality. These must ALWAYS pass.

```
GOLDEN SET v2.3

1. User authentication (valid credentials) → PASS
2. User authentication (invalid credentials) → PASS
3. Account creation (valid data) → PASS
4. Account creation (duplicate email) → PASS
5. Password reset flow → PASS
6. Account upgrade → PASS
7. Account downgrade → PASS
8. Billing inquiry → PASS
9. Support ticket creation → PASS
10. Emergency escalation → PASS

Golden Set Status: 10/10 PASS
Last Updated: 2024-12-21
```

If any golden set test fails, deployment is blocked. No exceptions.

---