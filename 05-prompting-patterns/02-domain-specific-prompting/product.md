### Product Management

**The unique challenge:** Product management requires balancing user needs, business goals, technical constraints, and stakeholder alignment - all while making decisions with incomplete information.

**Key principles for product prompts:**

**1. PRD (Product Requirements Document) generation**

When creating PRDs, provide comprehensive context about business situation, objectives, required output, format, and constraints.

```
You are a Senior Product Manager at [Company Name].

Context:
- Industry: [e.g., B2B SaaS, FinTech]
- Target Users: [Enterprise finance teams, etc.]
- Current Challenge: [Improving user onboarding]
- Business Objective: [Reduce time-to-value from 30 to 10 days]

Generate a comprehensive PRD for [feature name] including:

1. Feature Overview
   - Problem statement
   - Success criteria
   - Target metrics

2. User Stories (3-5)
   - As a [user type], I want [goal] so that [benefit]

3. Functional Requirements
   - Core functionality
   - Edge cases
   - User flows

4. Non-Functional Requirements
   - Performance benchmarks
   - Security requirements
   - Accessibility standards

5. Technical Considerations
   - Integration points
   - Data requirements
   - Scalability concerns

6. Success Metrics
   - Leading indicators
   - Lagging indicators
   - How to measure

7. Risks & Mitigation
   - Technical risks
   - Business risks
   - User adoption risks

Use industry-standard PRD format with clear sections.
```

**2. User story generation**

```
Generate user stories for [feature description]:

Target users: [specific user personas]
Jobs to be done: [what users are trying to accomplish]

For each user story:
- Use standard format: "As a [type of user], I want [goal] so that [benefit]"
- Include acceptance criteria (Given/When/Then format)
- Estimate story points (S/M/L)
- Note dependencies on other stories
- Flag technical complexity

Prioritize stories using MoSCoW method (Must have, Should have, Could have, Won't have)
```

**3. Competitive analysis**

AI can rapidly gather competitive intelligence from various sources, but be aware of data recency limitations.

```
Conduct a competitive analysis of [your product] versus top 5 competitors in [market segment]:

For each competitor, analyze:
1. Core Features
   - What do they offer?
   - What's their unique value prop?
   - Feature comparison matrix

2. Pricing Strategy
   - Pricing tiers
   - Free vs. paid features
   - Positioning (premium vs. value)

3. User Experience
   - Onboarding flow
   - Key workflows
   - Design approach

4. Market Position
   - Target customers
   - Market share (if known)
   - Brand perception

5. Strengths & Weaknesses
   - What are they great at?
   - Where do they fall short?
   - Opportunities for differentiation

Output: SWOT analysis for each competitor + strategic recommendations
```

**4. Feature prioritization**

LLMs can calculate prioritization scores using frameworks like RICE (Reach, Impact, Confidence, Effort).

```
Prioritize these features using RICE scoring:

Feature list:
1. [Feature name]: [Brief description]
   - Estimated Reach: [# users affected]
   - Estimated Impact: [High/Medium/Low]
   - Confidence: [% certainty]
   - Estimated Effort: [person-months]

2. [Continue for all features...]

Calculate RICE score for each: (Reach × Impact × Confidence) / Effort

Provide:
- Ranked list with scores
- Explanation of scoring rationale
- Recommendations on which to build first
- Trade-offs and considerations
```

**5. Roadmap planning**

Use scenario planning to account for uncertainty.

```
Create an 18-month product roadmap:

Current state:
- Product: [description]
- Team size: [number]
- Resource constraints: [list]
- Strategic goals: [list]

Generate three scenarios:

OPTIMISTIC (Best case):
- Market conditions: [favorable]
- User adoption: [exceeds targets]
- Revenue impact: [high growth]
- Resource allocation: [as planned or better]
- Recommended features and timeline

BASE CASE (Most likely):
- Market conditions: [stable]
- User adoption: [meets targets]
- Revenue impact: [steady growth]
- Resource allocation: [as planned]
- Recommended features and timeline

CONSERVATIVE (Challenging case):
- Market conditions: [difficult]
- User adoption: [below targets]
- Revenue impact: [slow growth]
- Resource constraints: [reduced resources]
- Recommended features and timeline (focus on must-haves)

For each scenario: Quarter-by-quarter feature releases, dependencies, risks
```

**6. Stakeholder communication**

Different stakeholders need different levels of detail and framing.

```
Draft a product update for [stakeholder group]:

Update content:
- Features shipped last quarter: [list]
- Current progress: [summary]
- Upcoming milestones: [list]
- Blockers/risks: [if any]

Customize for audience:

FOR EXECUTIVES:
- Focus on business impact and metrics
- High-level roadmap
- Strategic decisions needed
- Keep to 1 page

FOR ENGINEERING:
- Technical details and architecture decisions
- Dependencies and technical debt
- Resource allocation
- Timeline and sprints

FOR SALES:
- Customer-facing benefits
- Competitive positioning
- Demo-ready features
- Objection handling

FOR CUSTOMERS:
- Value delivered
- How to use new features
- What's coming next
- How to provide feedback
```

**7. User feedback analysis**

LLMs can synthesize large amounts of user feedback to identify patterns.

```
Analyze this collection of user feedback and extract insights:

[Paste 20-50 pieces of user feedback]

Extract:
1. Top 3 pain points by frequency
   - Quote representative feedback
   - Estimate % of users affected
   - Severity (Critical/High/Medium/Low)

2. Emerging patterns
   - Feature requests clustering around themes
   - User behavior signals
   - Unmet needs

3. Sentiment analysis
   - Overall satisfaction trend
   - Specific features with negative sentiment
   - Delighters (features users love)

4. Actionable recommendations
   - Quick wins (high impact, low effort)
   - Strategic opportunities
   - Features to deprecate or improve

5. User segmentation insights
   - Do different user types have different needs?
   - Enterprise vs. SMB patterns
   - Power users vs. casual users
```

**8. Go-to-Market (GTM) strategy**

```
Develop a go-to-market strategy for [new product/feature]:

Product: [description]
Target market: [ICP description]
Differentiation: [unique value prop]

Create GTM plan covering:

1. Positioning & Messaging
   - Core message
   - Value propositions by persona
   - Proof points

2. Pricing Strategy
   - Pricing model (subscription, usage-based, etc.)
   - Tier structure
   - Rationale for pricing decisions

3. Launch Plan
   - Beta program (if applicable)
   - Launch phases
   - Success criteria per phase
   - Timeline

4. Channel Strategy
   - Primary channels (direct sales, self-serve, partners)
   - Marketing channels (content, paid, events)
   - Sales enablement needs

5. Customer Success
   - Onboarding plan
   - Support resources
   - Adoption metrics

6. Launch Risks
   - Technical risks
   - Market risks
   - Mitigation strategies
```

**Product Management anti-patterns:**

```
DON'T: "Write a PRD for a mobile app"
DO: "Write a PRD for a mobile expense tracking app for freelancers who need to separate personal and business expenses for tax purposes. Key pain point: current solutions are too complex."

DON'T: "Prioritize these features"
DO: "Prioritize these features using RICE scoring framework. Context: B2B SaaS with 2,000 active customers, engineering team of 8, strategic goal is reducing churn by 20%."

DON'T: "Analyze competitors"
DO: "Analyze top 3 competitors in project management space, focusing on their async communication features. Our hypothesis: there's a gap in async-first PM tools for remote teams."
```

---

