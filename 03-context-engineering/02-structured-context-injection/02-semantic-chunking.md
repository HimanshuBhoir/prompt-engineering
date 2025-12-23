## **Semantic Chunking**

**Core principle:** Break information into meaningful, self-contained units based on semantic boundaries, not arbitrary lengths.

---

### **Bad Chunking (Arbitrary):**

```
CONTEXT:
Chunk 1 (first 500 tokens):
"Our authentication system uses JWT tokens which are valid for 24
hours and can be refreshed using the refresh endpoint located at
/api/auth/refresh and the tokens are signed using RS256 algorithm..."

Chunk 2 (next 500 tokens):
"...which requires a public key that can be obtained from the..."
```

**Problem:** Splits mid-concept, context lost between chunks

---

### **Good Chunking (Semantic):**

```
CONTEXT:

Chunk 1 - Authentication Overview:
Our authentication system uses JWT tokens.
- Validity: 24 hours
- Refresh: POST /api/auth/refresh
- Algorithm: RS256

Chunk 2 - Token Structure:
JWT tokens contain:
- user_id: Unique identifier
- exp: Expiration timestamp
- roles: Array of user permissions

Chunk 3 - Security:
All tokens are signed with RS256.
Public keys available at: /.well-known/jwks.json
Tokens must be sent in Authorization header.
```

**Benefits:** Each chunk is complete and self-contained

---

### **Semantic Boundary Detection:**

Look for natural breaks:
- **Topic changes:** New subject = new chunk
- **Document structure:** Sections, paragraphs, code blocks
- **Conceptual units:** Complete ideas, not mid-sentence
- **Hierarchical levels:** Headers indicate boundaries

---

### **Chunking Pattern for Long Documents:**

```
CONTEXT:

[DOCUMENT OVERVIEW]
Title: API Authentication Guide
Sections: 5
Key topics: JWT, OAuth, API Keys
Total length: ~15K tokens

[SECTION 1: JWT Authentication]
(2K tokens - complete section)

[SECTION 2: OAuth Flow]
(3K tokens - complete section)

[METADATA]
Related sections available on request:
- Section 3: API Key Management
- Section 4: Error Handling
- Section 5: Best Practices

User query: "How does JWT work?"
Retrieved: Section 1 (most relevant)
```

**Pattern:** Load overview + relevant sections, not everything

---

