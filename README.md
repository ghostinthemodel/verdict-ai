# VerdictAI — LLM-as-Judge Evaluation Platform

> Automated quality evaluation for LLM-powered systems.
> Detect hallucinations, score faithfulness, measure bias — with evidence, not just scores.

**Live demo:** https://ghostinthemodel.github.io/verdict-ai/

---

## What It Does

VerdictAI is a browser-based LLM evaluation platform that demonstrates five
core quality metrics for AI-generated responses. Every evaluation returns a
scored verdict with an evidence chain explaining the reasoning — not just a
number.

| Metric | What It Catches |
|---|---|
| **Hallucination Detection** | Factual claims that contradict known facts or context |
| **Faithfulness Scoring** | RAG responses that fabricate details beyond retrieved context |
| **Answer Relevancy** | Responses that deflect, go off-topic, or answer a different question |
| **Bias Detection** | Differential treatment across demographic groups (counterfactual analysis) |
| **Custom Rubric (G-Eval)** | Any quality criteria you define in plain language |

---

## Live Demo

The demo is fully interactive with pre-loaded scenarios — no API key needed.

Each scenario shows:
- A question and model response (pre-loaded or custom)
- Animated score bars across relevant sub-metrics
- A judge reasoning block with specific evidence citations
- A claim-by-claim analysis showing exactly what passed and what failed

**Try it:** https://ghostinthemodel.github.io/verdict-ai/

### Example scenarios included

**Hallucination:**
- Space facts with a fabricated date (⚠ WARN)
- Medical Q&A with a non-existent drug "Gliptozin" (✗ FAIL)
- Historical facts — accurate response (✓ PASS)

**Faithfulness (RAG):**
- Product FAQ with faithful grounded answer (✓ PASS)
- Product FAQ where model fabricates a refund policy not in context (✗ FAIL)

**Answer Relevancy:**
- Direct question with relevant answer (✓ PASS)
- Customer query deflected with generic reassurances (✗ FAIL)

**Bias:**
- Gender bias in salary negotiation advice — counterfactual pair (✗ FAIL)
- Historical contributions — balanced response across groups (✓ PASS)

**Custom Rubric:**
- Legal advice boundary check — edges toward specific legal advice (⚠ WARN)
- Brand voice compliance — significant slang violations (✗ FAIL)

---

## Why LLM-as-Judge

Traditional QA uses deterministic assertions: `assert output == expected`.
This breaks completely for LLM outputs, where:

- The same input produces different outputs on every run
- There is often no single correct answer
- Quality dimensions like tone, faithfulness, and bias can't be reduced to string matching

LLM-as-Judge solves this by using a calibrated judge model to evaluate outputs
against defined criteria — the same way a human expert reviewer would, but at
scale and with structured output.

This approach is used in production by teams at OpenAI, Google, and Anthropic,
and is the foundation of frameworks like DeepEval and RAGAS.

---

## Design Decisions

**Why mock data instead of live API calls?**
The demo runs entirely in the browser with no backend — making it instantly
accessible to anyone without an API key or account. In a production
implementation, each evaluation would call a judge model (e.g. GPT-4,
Claude) via API and return structured scores.

**Why five metrics?**
These five cover the most common failure modes in production LLM systems:

1. Hallucination — the most insidious failure (plausible but false)
2. Faithfulness — critical for any RAG-powered feature
3. Relevancy — catches deflection and off-topic responses
4. Bias — required for fairness and regulatory compliance
5. Custom rubric — covers business-specific quality requirements no standard metric addresses

**Why evidence chains, not just scores?**
A score of 0.31 is not actionable. Knowing that the model fabricated a
drug name called "Gliptozin" that does not exist is. Every verdict includes
the specific claim that caused the failure.

---

## Technical Stack

- Vanilla HTML, CSS, JavaScript — no framework dependencies
- Zero backend — runs entirely in the browser
- Hosted on GitHub Pages
- All evaluation logic is client-side with pre-computed mock results

---

## Production Implementation

This demo illustrates the evaluation concepts. A production version would:

```
User submits: question + response + optional context
        ↓
Judge model call (GPT-4 / Claude / open-source)
        ↓
Structured score output with reasoning
        ↓
Verdict: PASS / WARN / FAIL + evidence chain
        ↓
CI/CD integration — fail the build if score < threshold
```

The same pattern is implemented in the companion test suite using DeepEval:

```python
from deepeval.metrics import HallucinationMetric, FaithfulnessMetric
from deepeval.test_case import LLMTestCase
from deepeval import assert_test

test_case = LLMTestCase(
    input="Does DataSync Pro support Linux?",
    actual_output=model_response,
    context=retrieved_docs,
)
assert_test(test_case, [
    HallucinationMetric(threshold=0.8),
    FaithfulnessMetric(threshold=0.8),
])
```

See the full test suite: [ai-qa-portfolio/tests](https://github.com/ghostinthemodel/ai-qa-portfolio/tree/main/tests)

---

## Related Work

This platform is part of a broader AI/ML QA portfolio:

| Resource | Description |
|---|---|
| [AI/ML QA Portfolio](https://ghostinthemodel.github.io/ai-qa-portfolio/) | Full QA methodology, test cases, interactive demos |
| [Test Suite](https://github.com/ghostinthemodel/ai-qa-portfolio/tree/main/tests) | 17 runnable pytest tests targeting a real LLM app |
| [Defect Report BUG-001](https://github.com/ghostinthemodel/ai-qa-portfolio/blob/main/tests/DEFECT_REPORT_BUG001.md) | Real safety bypass found during exploratory testing |

---

## Author

**Tapan Shah** — Senior QA Engineer · AI/ML Testing Specialist · Brisbane, Australia

10+ years of QA experience across fintech, e-commerce, SaaS, and consulting.
Specialising in quality engineering for AI-powered systems.

[LinkedIn](https://linkedin.com/in/tapanshah) · [Portfolio](https://ghostinthemodel.github.io/ai-qa-portfolio/) · [GitHub](https://github.com/ghostinthemodel)

---

*Built to demonstrate LLM evaluation platform design and AI/ML QA engineering skills.*
