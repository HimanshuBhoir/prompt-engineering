## **Context Ordering Effects**

**Critical finding (2024 research):** Models don't process context uniformly — position matters significantly.

---

### **The Attention Curve:**

```
Attention Level
     ↑
High |█████                           █████
     |█████                           █████
     |█████                           █████
Med  |█████                           █████
     |█████   ███                     █████
     |█████   ███                     █████
Low  |█████   ███   ███   ███        █████
     |─────────────────────────────────────→
       Start         Middle            End
       
"Primacy Effect"   "Lost Valley"   "Recency Effect"
```

**Key insight:** Start and end get most attention, middle is often "lost"

---

### **Ordering Principle 1: Critical First**

```
CONTEXT:

[CRITICAL - READ FIRST]
SECURITY ISSUE: Hardcoded credentials in line 45
Action required: IMMEDIATE

[Additional context follows...]
```

**Why first:** Ensures it's processed even if context truncated

---

### **Ordering Principle 2: Query-Relevant Near Start**

```
User asks: "What's my order status?"

CONTEXT:

Current Order Status: Shipped (Dec 16)
Tracking: 1Z999AA10123456784
Expected delivery: Dec 22

[Then other less relevant info...]
```

**Why near start:** Most relevant to query gets processed first

---

### **Ordering Principle 3: Critical Also at End**

```
CONTEXT:

[Normal context in middle...]

[FINAL REMINDER - MUST FOLLOW]
Before responding:
- Verify you checked the security issue on line 45
- Confirm tracking number was provided
- Ensure professional tone maintained
```

**Why also at end:** Recency effect catches it before response generation

---

### **Ordering Principle 4: Less Important in Middle**

```
CONTEXT:

[CRITICAL] Current order status...

[REFERENCE] Account history:
- 47 previous orders (middle section - less critical)
- All delivered successfully
- Average order value: $85

[CRITICAL] Current security concern...
```

**Why middle for reference:** Still available if needed, but not prioritized

---