## **Priority Tagging**

**Core principle:** Explicitly label context importance so AI knows what to prioritize.

---

### **Priority Tag Pattern:**

```
CONTEXT:

[CRITICAL - MUST READ FIRST]
User reported: "Cannot log in"
Account status: LOCKED (failed attempts: 5)
Security alert: YES

[IMPORTANT - KEY CONTEXT]
User: Sarah Chen (Gold tier)
Last successful login: 2024-12-18
Recent activity: Normal pattern until today

[REFERENCE - BACKGROUND INFO]
Account age: 2 years
Total orders: 47
Support history: 2 tickets (both resolved)

[OPTIONAL - SUPPLEMENTARY]
User location: San Francisco, CA
Preferred contact: Email
Device: iPhone 15, iOS 17
```

**Why this works:** AI processes in priority order, focuses on critical info first

---

### **Inline Priority Tags:**

```
CONTEXT:

Pull Request #1234

[CRITICAL] Security vulnerability found in auth.js line 23
[CRITICAL] No tests added for new authentication method

[HIGH] Code coverage dropped from 89% to 87%
[HIGH] Missing documentation for new API endpoints

[MEDIUM] Some variable names could be more descriptive
[MEDIUM] Consider extracting large function into smaller ones

[LOW] Minor formatting inconsistencies
[LOW] Could add more inline comments
```

---

### **Confidence Tagging:**

```
CONTEXT:

Order Status: Shipped [VERIFIED - from database]
Estimated delivery: Dec 22 [PREDICTED - based on averages]
Customer satisfaction: High [INFERRED - from past behavior]
```

**Why confidence matters:** AI can caveat uncertain information appropriately

---