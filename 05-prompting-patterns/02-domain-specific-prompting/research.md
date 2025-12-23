### Research

**The unique challenge:** Research requires finding accurate information, synthesizing multiple sources, maintaining academic rigor, and proper citation practices.

**Key principles for research prompts:**

**1. Specify research methodology**

Research shows that well-designed prompts with proper structure significantly improve information extraction from literature.

```
Conduct a literature review on [topic]:

Methodology:
1. Identify 10-15 peer-reviewed papers from the last 5 years
2. For each paper, extract: research question, methodology, key findings, limitations
3. Synthesize common themes and contradictions
4. Identify research gaps
5. Suggest future research directions

Output format: Structured summary with citation in APA format
```

**2. Structured information extraction**

For scientific literature, prompts should specify exact information types needed, desired output format, and how to handle edge cases.

```
Extract data from abstracts about white phosphor materials:

For each abstract, create a table with these exact columns:
- Material composition
- Synthesis method
- Excitation wavelength (nm)
- Emission wavelength (nm)
- Quantum efficiency (%)
- Color coordinates (CIE)

If a field is not mentioned, write "Not reported"
If values are given as ranges, record as "X-Y"
Maintain consistent units throughout
```

**3. Chain-of-Thought for literature analysis**

```
Analyze this research paper and evaluate its contribution:

Step 1: Summarize the research question and hypothesis
Step 2: Evaluate the methodology - is it appropriate for the question?
Step 3: Assess the evidence - do results support conclusions?
Step 4: Identify limitations acknowledged by authors and ones they missed
Step 5: Determine novelty - what's genuinely new vs. incremental?
Step 6: Assess broader impact - why does this matter?

Provide your reasoning for each step before moving to the next.
```

**4. Comparative analysis prompts**

```
Compare these three approaches to [research problem]:

Study A: [summary]
Study B: [summary]  
Study C: [summary]

Create a comparison matrix analyzing:
- Theoretical framework
- Methodology strengths/weaknesses
- Sample size and composition
- Key findings and effect sizes
- Practical implications
- Which approach is most promising and why?

Highlight where studies agree, disagree, and what might explain differences.
```

**5. Hypothesis generation**

```
Based on the following findings:
- Finding 1: [data]
- Finding 2: [data]
- Finding 3: [data]

Generate 5 testable hypotheses that:
1. Address gaps in current knowledge
2. Are feasible to test with available methods
3. Have theoretical grounding
4. Could have practical implications

For each hypothesis:
- State it clearly (if X, then Y, because Z)
- Explain the reasoning
- Suggest how to test it
- Predict potential outcomes
```

**6. Citation and verification prompts**

```
Fact-check this claim: "[claim]"

Process:
1. Identify the core factual assertions
2. Search for peer-reviewed sources supporting or refuting each assertion
3. For each source: Assess credibility (journal impact factor, author credentials, sample size)
4. Synthesize findings: Does evidence support, contradict, or is it inconclusive?
5. Provide properly formatted citations

Output: Evidence-based verdict with citation trail
```

**7. Research gap identification**

```
After reviewing literature on [topic], identify research gaps:

Analysis framework:
1. What questions are well-answered by existing research?
2. What questions remain unanswered or under-studied?
3. What populations/contexts are underrepresented?
4. What methodological approaches are missing?
5. What are emerging areas that need more investigation?

For each gap:
- Explain why it matters
- Suggest specific research questions to address it
- Propose appropriate methodologies
```

**8. Research proposal generation**

```
Create a research proposal outline for [topic]:

Background & Significance:
- What is known?
- What is unknown?
- Why does filling this gap matter?

Research Question: [Specific, focused question]

Hypotheses: [Testable predictions]

Methodology:
- Study design
- Participants/sample
- Measures and instruments
- Data analysis plan
- Timeline
- Expected challenges and mitigation

Expected Outcomes and Impact
```

**Hallucination prevention for research:**

Research on using LLMs for scientific tasks emphasizes the need to verify outputs due to risks of plagiarism and hallucinations (false or misleading information).

```
When providing research information:
1. Only cite papers you can verify exist
2. If uncertain about a fact, say "This requires verification"
3. Distinguish between established facts and emerging findings
4. Note consensus vs. controversial topics
5. Flag when extrapolating beyond data

If you cannot find a relevant source, say so rather than inventing one.
```

---

