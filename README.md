# SnowPro Specialty: Gen AI (C02) — Exam Study Guide

Exam-focused study notes for the **SnowPro® Specialty: Gen AI Certification Exam**, organized
by the four official exam domains. Each file is self-contained: concepts, key distinctions,
common traps, decision frameworks, and documentation references.

> Based on the official Snowflake study guide, **C02 version, Last Updated: April 30, 2026**.
> Snowflake Gen AI features evolve quickly — verify function names, parameters, and previews
> against current [Snowflake documentation](https://docs.snowflake.com/) before exam day.

---

## Exam Overview

| Attribute | Detail |
| --- | --- |
| Exam | SnowPro Specialty: Gen AI (C02) |
| Format | Scenario-based, interactive, and real-world example questions |
| Target audience | AI/ML Engineers, Data Scientists, Data Engineers, Data App Developers, Data Analysts w/ programming experience |
| Assumed knowledge | Snowflake Cortex suite, Snowpark Container Services, Model Registry, SQL, Python |
| Recommended prep | ~10–13 hours of self-study |

The exam tests your ability to:
- Define and implement Snowflake Gen AI principles, capabilities, and best practices
  (infrastructure, data governance, cost governance).
- Leverage Snowflake Cortex AI features and functions (incl. LLMs) for customer use cases
  (Cortex Analyst, Cortex Search, Cortex Code).
- Build and fine-tune open-source models with Snowpark Container Services and the
  Snowflake Model Registry.

---

## Domain Weightings & Study Files

| Domain | Weighting | File | Focus |
| --- | --- | --- | --- |
| 1.0 Snowflake for Gen AI Overview | **18%** | [domain-1-gen-ai-overview.md](domain-1-gen-ai-overview.md) | The Cortex suite, interfaces, BYOM, MCP, cross-region |
| 2.0 Snowflake Gen AI Functions | **38%** | [domain-2-gen-ai-functions.md](domain-2-gen-ai-functions.md) | AI/vector/helper functions, RAG, text-to-SQL, chat apps, SPCS |
| 3.0 Snowflake Gen AI Governance | **29%** | [domain-3-gen-ai-governance.md](domain-3-gen-ai-governance.md) | Access control, RBAC roles, cost tracking, AI observability |
| 4.0 Snowflake Document Processing | **15%** | [domain-4-document-processing.md](domain-4-document-processing.md) | AI_PARSE_DOCUMENT, AI_EXTRACT, pipelines, troubleshooting |

Domain 2.0 (38%) is by far the largest — spend the most time there, followed by Domain 3.0 (29%).

**Practice:** [sample-questions.md](sample-questions.md) — the 5 official sample questions with
explanations plus 20 additional practice questions (5 per domain).

---

## How to Use This Guide

1. **Start with weightings.** ~67% of the exam is Domains 2 + 3. Prioritize accordingly.
2. **Learn the distinctions, not just definitions.** The exam is scenario-based: it tests
   *which* feature/function to pick for a use case. Watch for the "Key Distinction" and
   "Exam Tip" callouts in each domain file.
3. **Master the four Cortex services.** Know cold when to use **Cortex Search** (RAG over
   unstructured docs) vs **Cortex Analyst** (text-to-SQL over structured data) vs
   **Cortex Agents** (orchestration over both) vs **Snowflake Intelligence** (no-code agent UI).
4. **Memorize the AI function catalog** (Domain 2.1) and the governance roles/views
   (Domain 3.2–3.3) — these are high-frequency recall items.
5. **Drill the sample questions** until the reasoning (not just the answer letter) is automatic.

---

## Decision Cheat Sheet (memorize)

| If the scenario is… | The answer is usually… |
| --- | --- |
| Chatbot/search over unstructured PDFs/text (RAG) | **Cortex Search** |
| Natural-language questions over structured tables (text-to-SQL) | **Cortex Analyst** (+ semantic view) |
| Orchestrate tools across structured + unstructured data | **Cortex Agents** |
| No-code, business-user agent experience in Snowsight | **Snowflake Intelligence** |
| Generate free-form text/answers from a prompt | **AI_COMPLETE** |
| Extract structured fields with a defined schema | **AI_EXTRACT** |
| Convert a document to text/markdown preserving layout/tables | **AI_PARSE_DOCUMENT (LAYOUT mode)** |
| Daily AI services credit consumption | **METERING_DAILY_HISTORY** |
| Run/fine-tune your own open-source model | **Snowpark Container Services + Model Registry** |
| Evaluate a Gen AI app's quality | **AI Observability (TruLens SDK)** |

---

## Reference

- Source study guide: `docs/reference/SnowProGenAIStudyGuideC02.pdf`
- Objective coverage checklist: `docs/reference/objective-checklist.md`
- Snowflake Cortex docs: https://docs.snowflake.com/en/guides-overview-ai-features
