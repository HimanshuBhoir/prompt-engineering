
## **What Belongs in Context vs Prompt**

### **PROMPT (System Instructions):**
- Identity and role
- Behavior rules
- Output format
- Conditional logic
- Safety boundaries

**Characteristics:** Static, reusable, applies to all requests

---

### **CONTEXT (Dynamic Information):**
- User's current question
- Retrieved documents
- Database records
- Conversation history
- User preferences
- Real-time data
- Files uploaded

**Characteristics:** Changes per request, specific to this interaction

---

## **The Decision Framework**

```
Ask yourself:

1. Does this change per request?
   YES → Context
   NO → Prompt

2. Is this information or instruction?
   Information → Context
   Instruction → Prompt

3. Will this scale to 1000 users?
   NO (user-specific data) → Context
   YES (same for everyone) → Prompt

4. Does this need to be updated frequently?
   YES → Context
   NO → Prompt
```

---

## **Examples**

### **Example 1: E-commerce Support**

**PROMPT:**
```
You are ShopCo support AI.
When answering about orders:
1. Check order status in context
2. Provide tracking info if available
3. Offer refund link if eligible
```

**CONTEXT (injected per user):**
```
User: Sarah Chen
Account since: 2023-01-15
Loyalty tier: Gold

Current order:
- Order ID: #45782
- Date: 2024-12-15
- Status: Shipped
- Tracking: 1Z999AA10123456784
- Items: 2x Widget Pro, 1x Gadget X
- Total: $299.99

Previous orders: 12 (all delivered successfully)
Return history: None
```

---

### **Example 2: Code Review AI**

**PROMPT:**
```
You are a code reviewer.
Check for:
- Security issues
- Test coverage
- Documentation
- Performance concerns

Flag critical issues first.
```

**CONTEXT (injected per PR):**
```
Pull Request: #1234
Author: alex_dev
Target: main branch

Files changed:
[Full code diff here - could be 10K+ tokens]

CI Results:
- Tests: 45/45 passed
- Coverage: 87% (+2% from base)
- Linting: 2 warnings

Recent commits:
- feat: add caching layer
- fix: handle null case
- refactor: extract helper
```

---