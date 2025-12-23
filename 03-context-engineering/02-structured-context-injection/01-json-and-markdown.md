## **JSON Context Injection**

### **When to Use JSON:**
- Structured data (databases, APIs)
- Multiple entities with relationships
- Need exact field referencing
- Programmatic context generation

---

### **Basic JSON Pattern:**

```
CONTEXT:
{
  "user": {
    "id": "u_12345",
    "name": "Sarah Chen",
    "tier": "gold",
    "member_since": "2023-01-15"
  },
  "order": {
    "id": "ord_45782",
    "status": "shipped",
    "tracking": "1Z999AA10123456784",
    "items": [
      {"name": "Widget Pro", "qty": 2, "price": 149.99},
      {"name": "Gadget X", "qty": 1, "price": 99.99}
    ],
    "total": 299.99
  },
  "preferences": {
    "communication": "email",
    "language": "en",
    "timezone": "America/Los_Angeles"
  }
}

Instructions: Reference fields using dot notation (e.g., user.name, order.status)
```

---

### **JSON with Metadata:**

```
CONTEXT:
{
  "metadata": {
    "retrieved_at": "2024-12-20T10:30:00Z",
    "data_freshness": "realtime",
    "confidence": "high"
  },
  "data": {
    "user": {...},
    "order": {...}
  }
}
```

**Why metadata matters:** AI knows how current/reliable the data is

---

### **Nested JSON for Complex Relationships:**

```
CONTEXT:
{
  "pull_request": {
    "id": 1234,
    "author": "alex_dev",
    "files_changed": [
      {
        "path": "src/auth.js",
        "changes": {
          "additions": 45,
          "deletions": 12
        },
        "issues": [
          {
            "line": 23,
            "severity": "critical",
            "type": "security",
            "description": "Hardcoded API key"
          }
        ]
      }
    ],
    "tests": {
      "passed": 45,
      "failed": 0,
      "coverage": 87
    }
  }
}

Instructions: Address issues by file path and line number.
```

---

## **Markdown Context Injection**

### **When to Use Markdown:**
- Documents and long-form content
- Need readability (humans AND AI)
- Hierarchical information
- Mixed content types (text, code, lists)

---

### **Basic Markdown Pattern:**

```
CONTEXT:

# User: Sarah Chen

## Account Details
- **Member Since:** 2023-01-15
- **Tier:** Gold
- **Email:** sarah@email.com
- **Phone:** (555) 123-4567

## Current Order (#45782)
- **Status:** Shipped ✓
- **Date:** December 15, 2024
- **Tracking:** 1Z999AA10123456784
- **Total:** $299.99

### Items
1. Widget Pro (×2) - $149.99
2. Gadget X (×1) - $99.99

## Recent Activity
- Last login: December 19, 2024
- Last order: December 15, 2024
- Support tickets: 0 open, 3 resolved
```

**Benefits:**
- Human-readable
- Clear hierarchy (headers)
- Visual separators
- Works well for documentation

---

### **Markdown with Code Blocks:**

```
CONTEXT:

# Code Review: Pull Request #1234

## Changes Summary
Author: alex_dev
Files changed: 3
Lines: +45 / -12

## Security Issue Found

**File:** `src/auth.js`
**Line:** 23
**Severity:** Critical

```javascript
// Current code (VULNERABLE):
const API_KEY = "sk_live_abc123def456";

// Should be:
const API_KEY = process.env.API_KEY;
```

**Recommendation:** Move API key to environment variables immediately.
```

---

### **Markdown Tables:**

```
CONTEXT:

# Recent Orders

| Order ID | Date       | Status    | Total   |
|----------|------------|-----------|---------|
| #45782   | 2024-12-15 | Shipped   | $299.99 |
| #45123   | 2024-11-20 | Delivered | $149.99 |
| #44892   | 2024-10-05 | Delivered | $79.99  |

**Note:** All orders delivered successfully with no issues.
```

---

