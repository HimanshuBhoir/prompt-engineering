# Structured Context Injection

**Core idea:** HOW you structure context matters as much as WHAT you include. Well-structured context = better comprehension, faster processing, fewer errors.

---

## **Why Structure Matters**

### **Unstructured (Hard to parse):**
```
CONTEXT:
John Smith ordered a widget on December 15 for $29.99 and it shipped 
on December 16 with tracking number 1Z999AA10123456784 and he's a gold 
member since 2023 and has ordered 12 times before with no returns and 
his email is john@email.com and his last login was yesterday...
```

**Problems:**
- Dense wall of text
- Key info buried
- Relationships unclear
- Hard to scan quickly

---

### **Structured (Easy to parse):**
```
CONTEXT:
User Profile:
  Name: John Smith
  Email: john@email.com
  Member Since: 2023-01-15
  Tier: Gold
  
Current Order:
  Order ID: #45782
  Date: 2024-12-15
  Status: Shipped
  Shipped: 2024-12-16
  Tracking: 1Z999AA10123456784
  Amount: $29.99
  
Account History:
  Total Orders: 12
  Returns: 0
  Last Login: 2024-12-19
```

**Benefits:**
- Hierarchical organization
- Key info visible at a glance
- Clear relationships
- Easy to reference specific fields

---