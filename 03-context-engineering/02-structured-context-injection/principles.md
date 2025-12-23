## **Context Engineering Principles**

### **Principle 1: Clarity Over Brevity**

```
❌ Bad (too terse):
user:john,ord:45782,stat:ship,track:1Z999

✓ Good (clear):
User: John Smith
Order: #45782
Status: Shipped
Tracking: 1Z999AA10123456784
```

**Why:** Extra tokens for clarity worth the cost

---

### **Principle 2: Structure Matches Task**

```
For data lookup → JSON
For document review → Markdown
For code review → Code blocks + comments
For conversations → Dialogue format
```

**Match format to use case**

---

### **Principle 3: Explicit Relationships**

```
❌ Bad (implicit):
Order #45782
User Sarah Chen

✓ Good (explicit):
Order #45782
  ↳ Belongs to: Sarah Chen (user_id: u_12345)
  ↳ Shipping to: 123 Main St, SF, CA
```

**Why:** Don't make AI infer connections

---

### **Principle 4: Progressive Disclosure**

```
Level 1 (Always): Summary
Level 2 (If relevant): Key details
Level 3 (If requested): Full data

Example:
Summary: 3 security issues found
Details: [If user asks] Critical: 1, High: 2
Full: [If requested] Complete vulnerability report
```

**Why:** Conserve context, provide depth on demand

---

### **Principle 5: Metadata Inclusion**

```
CONTEXT:
Data source: Production database
Retrieved: 2024-12-20 10:30:00 UTC
Freshness: Real-time
Confidence: High

[Actual data follows...]
```

**Why:** AI knows how to treat the data

---

### **Principle 6: Explicit Scope**

```
CONTEXT SCOPE:
This context contains:
✓ Current order information
✓ User account details
✓ Recent activity (last 30 days)

This context does NOT contain:
✗ Full order history (available on request)
✗ Payment information (restricted)
✗ Other users' data (privacy)

Query: "What's my order status?"
```

**Why:** AI knows what it can/can't answer

---

## **Complete Pattern: [CRITICAL] [REFERENCE] [OPTIONAL]**

### **Full Implementation:**

```
═══════════════════════════════════════════════
[CRITICAL] - PROCESS FIRST
═══════════════════════════════════════════════

Security Alert: Account locked due to 5 failed login attempts
User: Sarah Chen (user_id: u_12345)
Action Required: Verify identity before unlocking

───────────────────────────────────────────────
[REFERENCE] - KEY CONTEXT
───────────────────────────────────────────────

Account Details:
{
  "user": {
    "name": "Sarah Chen",
    "email": "sarah@email.com",
    "tier": "gold",
    "member_since": "2023-01-15"
  },
  "security": {
    "last_success_login": "2024-12-18T14:30:00Z",
    "failed_attempts": 5,
    "locked_at": "2024-12-20T09:15:00Z",
    "usual_location": "San Francisco, CA",
    "current_location": "San Francisco, CA"
  }
}

───────────────────────────────────────────────
[OPTIONAL] - BACKGROUND
───────────────────────────────────────────────

Historical Context:
- Total orders: 47 (all successful)
- Support tickets: 2 (both resolved quickly)
- Payment history: No issues
- Account standing: Excellent

═══════════════════════════════════════════════
[VERIFICATION REQUIRED] - BEFORE RESPONDING
═══════════════════════════════════════════════

Before unlocking account:
□ Verify user identity through security questions
□ Confirm location matches historical pattern
□ Check for signs of account compromise
□ Follow security protocol Section 4.2

Never unlock without proper verification.
```

---

## **Real-World Example: E-commerce Support**

```
CONTEXT INJECTION:

═══════════════════════════════════════════════
[CRITICAL]
═══════════════════════════════════════════════

User Query: "Where is my order?"
Query Type: Order tracking
Urgency: Standard

Current Order Status:
- Order #45782: Shipped ✓
- Tracking: 1Z999AA10123456784
- Carrier: UPS
- Expected Delivery: Dec 22, 2024
- Current Location: In transit to SF

───────────────────────────────────────────────
[REFERENCE]
───────────────────────────────────────────────

User Profile:
```json
{
  "name": "Sarah Chen",
  "tier": "gold",
  "email": "sarah@email.com",
  "preferences": {
    "notifications": "email",
    "language": "en"
  }
}
```

Order Details:
```json
{
  "order_id": "45782",
  "date": "2024-12-15",
  "total": 299.99,
  "items": [
    {"name": "Widget Pro", "qty": 2},
    {"name": "Gadget X", "qty": 1}
  ]
}
```

───────────────────────────────────────────────
[OPTIONAL]
───────────────────────────────────────────────

Account History:
- Member since: Jan 2023
- Total orders: 47
- Returns: 0
- Satisfaction: High (inferred from behavior)

Last contact:
- Date: Nov 2024
- Type: Product question
- Resolution: Satisfied

═══════════════════════════════════════════════
[RESPONSE GUIDELINES]
═══════════════════════════════════════════════

Format: Professional, concise (2-3 sentences)
Include: Tracking number, delivery date
Offer: Tracking link
Tone: Reassuring
```

**This gives AI everything it needs, prioritized correctly**

---

## **Key Takeaways**

1. **Structure = Comprehension**
   - JSON for data
   - Markdown for documents
   - Format matches task

2. **Priority Matters**
   - Critical first
   - Reference in middle
   - Verification at end

3. **Position Effects Are Real**
   - Start: High attention
   - Middle: Often missed
   - End: Recency effect

4. **Explicit > Implicit**
   - Tag priorities
   - Label relationships
   - Declare scope

5. **Progressive Disclosure**
   - Summary always
   - Details if relevant
   - Full data on request

---