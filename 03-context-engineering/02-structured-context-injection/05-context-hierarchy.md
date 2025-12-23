## **The Context Hierarchy**

**Complete pattern for context organization:**

```
CONTEXT:

═══════════════════════════════════════════════
[CRITICAL] - ABSOLUTE PRIORITY
═══════════════════════════════════════════════

Security alerts, blockers, required actions
Must be processed first

───────────────────────────────────────────────
[HIGH PRIORITY] - QUERY-RELEVANT
───────────────────────────────────────────────

Information directly answering user's question
Most relevant context for this specific request

───────────────────────────────────────────────
[REFERENCE] - SUPPORTING CONTEXT
───────────────────────────────────────────────

Background information, historical data
Available if needed but not primary focus

───────────────────────────────────────────────
[OPTIONAL] - SUPPLEMENTARY
───────────────────────────────────────────────

Nice-to-have information
Can be ignored if token budget tight

═══════════════════════════════════════════════
[FINAL CHECKS] - MUST VERIFY BEFORE RESPONDING
═══════════════════════════════════════════════

Reminder of critical constraints, security checks
Last thing AI sees before generating response
```

---