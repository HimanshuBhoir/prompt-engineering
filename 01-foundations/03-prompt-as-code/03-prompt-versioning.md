# Prompt Versioning

**Core idea:** Prompts are code. Version them like code.

---

## Why Version Prompts?

**Without versioning:**
- ❌ Can't rollback when new prompt breaks things
- ❌ Can't A/B test different versions
- ❌ Can't track what changed and why
- ❌ No audit trail for production issues

**With versioning:**
- ✅ Rollback instantly if quality drops
- ✅ Compare performance across versions
- ✅ Debug by knowing exact prompt used
- ✅ Collaborate without overwriting changes

---

## Basic Versioning Pattern

```
# v1.0.0 - Initial version
system_prompt_v1 = """
You are a code reviewer.
Review for bugs and style issues.
"""

# v1.1.0 - Added security focus
system_prompt_v1_1 = """
You are a code reviewer.
Review for: bugs, style, security vulnerabilities.
Priority: security > bugs > style
"""

# v2.0.0 - Complete restructure
system_prompt_v2 = """
ROLE: Senior security-focused code reviewer

PRIORITY:
1. Security vulnerabilities
2. Logic bugs
3. Style issues

OUTPUT FORMAT:
[severity] [type] [description]
"""
```

---

## Semantic Versioning for Prompts

**Use semver: MAJOR.MINOR.PATCH**

**MAJOR (2.0.0):**
- Complete restructure
- Changed output format
- Breaking changes
- Example: switching from text to JSON output

**MINOR (1.1.0):**
- Added new capabilities
- Enhanced existing behavior
- Backward compatible
- Example: added security checks to existing reviews

**PATCH (1.0.1):**
- Bug fixes
- Wording improvements
- No behavior change
- Example: fixed typo, clarified constraint

---

## Tracking Changes

**Include metadata in your prompt file:**

```python
"""
PROMPT: Code Review Assistant
VERSION: 2.1.0
LAST_UPDATED: 2024-12-14
AUTHOR: team@company.com
CHANGELOG:
  - v2.1.0: Added React-specific checks
  - v2.0.0: Restructured with explicit format
  - v1.1.0: Added security focus
  - v1.0.0: Initial version
"""

SYSTEM_PROMPT = """
[Your prompt here]
"""
```

---

## Production Pattern

**Store version with each request:**

```python
# Log which version was used
{
  "request_id": "abc123",
  "prompt_version": "2.1.0",
  "timestamp": "2024-12-14T10:30:00Z",
  "output": "...",
  "model": "claude-sonnet-4"
}
```

**Why?** When debugging bad outputs, you need to know which prompt version caused them.

---

## Git for Prompts

**Best practice: Store prompts in version control**

```
/prompts
  /code_reviewer
    v1.0.0.txt
    v2.0.0.txt
    v2.1.0.txt
    CHANGELOG.md
  /summarizer
    v1.0.0.txt
    ...
```

**Or single file with Git history:**
```
/prompts
  code_reviewer.txt  # Git tracks changes
```

---

## Quick Rules

1. **Never edit production prompts directly** → Create new version
2. **Always tag versions** → Know what's running where
3. **Keep old versions** → Rollback capability
4. **Document changes** → Know why it changed
5. **Test before deploying** → Compare v1 vs v2 on test set

---

## The Mental Model

```
Prompt = Code
Version = Deploy
Test = A/B test
Rollback = Use previous version
```

**Treat prompts with the same rigor as code.**

---

**Key takeaway:** If you can't rollback your prompt, you're not ready for production.

---

Next topic?