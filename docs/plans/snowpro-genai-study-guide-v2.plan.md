# Plan: SnowPro Gen AI Exam Study Guide (v2)

> Revised from `snowpro-genai-study-guide.plan.md` to fold in plan-grade findings:
> corrected file count (7), added a fallback for missing exam metadata, and reconciled
> the output-location wording between the Summary and Metadata.

## Summary
Create an exam-focused study guide for the SnowPro Specialty: Gen AI Certification (C02),
organized as per-domain markdown files written to the repo root, with concise exam-relevant
content and documentation references. The guide follows the official exam study guide
structure (4 domains, weighted 18/38/29/15%).

## User Story
As a certification candidate preparing for the SnowPro Specialty: Gen AI exam,
I want exam-focused study notes organized by domain with documentation references,
So that I can efficiently review the key concepts, distinctions, and decision criteria tested on the exam.

## Problem -> Solution
Raw PDF study guide with topic outlines + scattered doc links -> Self-contained, exam-focused
markdown notes per domain with explanations, key distinctions, common traps, and direct
documentation references.

## Metadata
- **Complexity**: Medium
- **Source PRD**: `docs/reference/SnowProGenAIStudyGuideC02.pdf`
- **Output Directory**: repo root (`/`) — all study guide deliverables created at the top level
- **Phase**: standalone
- **Estimated Files**: 7 (index + 4 domain files + sample questions + objective-checklist validation artifact)
  - Deliverables (6, repo root): README + 4 domain files + sample-questions
  - Validation artifact (1, `docs/reference/`): objective-checklist.md

## External Documentation
| Topic | Source | Key Takeaway |
| --- | --- | --- |
| Cortex AI Functions (AISQL) | Snowflake docs | Function catalog, syntax, model selection |
| Cortex Search | Snowflake docs | RAG architecture, indexing, access control |
| Cortex Analyst | Snowflake docs | Semantic views, VQR, custom instructions |
| Cortex Agents | Snowflake docs | Agent setup, MCP, tool integration |
| Model Registry | Snowflake docs | Logging models, BYOM types, calling models |
| Snowpark Container Services | Snowflake docs | SPCS setup, compute pools, spec files |
| AI Observability | Snowflake docs | TruLens, evaluation metrics, tracing |
| Document Processing | Snowflake docs | AI_PARSE_DOCUMENT modes, AI_EXTRACT schemas |
| Cross-Region Inference | Snowflake docs | CORTEX_ENABLED_CROSS_REGION parameter |
| Access Control / Governance | Snowflake docs | RBAC roles, allowlist, cost tracking views |

## Patterns To Mirror
- Exam weighting proportional to content depth (Domain 2 gets deepest coverage)
- Each objective (1.1, 1.2, etc.) gets its own section
- "Key Distinctions" callouts for topics where exam questions test differentiation
- "Exam Tip" notes for common traps and decision frameworks
- Documentation references as verified hyperlinks (real `docs.snowflake.com` URLs sourced via
  `cortex search docs`, never guessed) in each domain file's reference section

## Files To Create
| File | Location | Purpose |
| --- | --- | --- |
| `README.md` | repo root | Index with exam overview, domain weightings, study strategy |
| `domain-1-gen-ai-overview.md` | repo root | Domain 1.0 (18%) — Snowflake for Gen AI Overview |
| `domain-2-gen-ai-functions.md` | repo root | Domain 2.0 (38%) — Snowflake Gen AI Functions |
| `domain-3-gen-ai-governance.md` | repo root | Domain 3.0 (29%) — Snowflake Gen AI Governance |
| `domain-4-document-processing.md` | repo root | Domain 4.0 (15%) — Snowflake Document Processing |
| `sample-questions.md` | repo root | Practice questions with explained answers |
| `objective-checklist.md` | `docs/reference/` | Validation artifact (not a study deliverable) |

## NOT Building
- Full documentation reproductions (reference links instead)
- Hands-on lab walkthroughs
- Video content or slide decks
- Topics outside the C02 exam scope

## Validation Approach
Before starting implementation, extract the full objective checklist from the source PDF
(every numbered bullet under each domain). Use this checklist as the validation artifact:
after writing each domain file, confirm every extracted bullet has a corresponding section
or paragraph. Cross-reference key claims (function names, role names, parameter names)
against current Snowflake documentation using `cortex search docs` to verify accuracy.

## Step-By-Step Tasks

### Task 0: Extract Objective Checklist
- **ACTION**: Read the source PDF and extract every numbered objective bullet into a
  checklist file (`docs/reference/objective-checklist.md`)
- **IMPLEMENT**: One checkbox per bullet, grouped by domain/objective number
- **VALIDATE**: Checklist count matches PDF bullet count; used as validation artifact for Tasks 2-6

### Task 1: Create README.md (index)
- **ACTION**: Write `README.md` in the repo root
- **IMPLEMENT**: Exam overview (format, question count, passing criteria), domain
  weightings table, links to each domain file, study tips
- **FALLBACK**: If the PDF does not state exam format details (question count, passing
  score, duration), verify them via `cortex search docs`. If they remain unconfirmed,
  omit the unknown fields rather than guessing, and note in the README that they are
  unspecified in the C02 guide.
- **VALIDATE**: All 4 domain files linked, weightings match PDF (18/38/29/15%)

### Task 2: Domain 1 — Snowflake for Gen AI Overview (18%)
- **ACTION**: Write `domain-1-gen-ai-overview.md`
- **IMPLEMENT**: Cover objectives 1.1 (Define principles/features) and 1.2 (Outline capabilities).
  Key topics: Cortex suite overview, Cortex Code (Snowsight + CLI), Copilot Inline, Snowflake
  Intelligence, interfaces (AI Studio/SQL/REST), BYOM (Model Registry + SPCS), cross-region
  inference, MCP, Cortex Knowledge Extensions
- **EXAM FOCUS**: Differentiation between Cortex Search vs Analyst vs Agents vs Intelligence,
  when to use each interface, cross-region parameter name and considerations
- **REFERENCES**: Cortex docs, Model Registry docs, Cross-Region Inference docs
- **VALIDATE**: Every bullet from PDF objectives 1.1 and 1.2 checked off in objective-checklist.md; key claims verified against `cortex search docs`

### Task 3: Domain 2 — Snowflake Gen AI Functions (38%)
- **ACTION**: Write `domain-2-gen-ai-functions.md`
- **IMPLEMENT**: Cover objectives 2.1-2.5. Catalog all AI functions (general, task-specific,
  vector, helper). Data analysis patterns for structured vs unstructured. Chat interface
  architecture. Pipeline integration. Third-party model deployment (SPCS + Model Registry).
- **EXAM FOCUS**: Function selection decision tree (which function for which use case),
  AI_COMPLETE vs task-specific functions, vector function purposes, Cortex Search chunking/embedding,
  Cortex Analyst VQR + custom instructions, performance considerations (model selection,
  provisioned throughput), SPCS setup steps
- **REFERENCES**: AISQL function docs, SPCS tutorials, Model Registry docs, Streamlit docs
- **VALIDATE**: All functions listed in PDF are covered with use-case context; objectives 2.1-2.5 checked off in objective-checklist.md

### Task 4: Domain 3 — Snowflake Gen AI Governance (29%)
- **ACTION**: Write `domain-3-gen-ai-governance.md`
- **IMPLEMENT**: Cover objectives 3.1-3.4. Model access controls (RBAC, allowlist, application
  roles). Privilege grants for Analyst/Search/Agents/Intelligence. Cost management (usage views,
  quotas, object tagging). AI Observability (TruLens, evaluation metrics, tracing, event tables).
- **EXAM FOCUS**: Role names (CORTEX_USER, CORTEX_ANALYST_USER, CORTEX_AGENT_USER,
  CORTEX_EMBED_USER), which usage history view for which service, guardrails vs AI_REDACT
  distinction, cost optimization strategies per service
- **REFERENCES**: Access control docs, cost views docs, AI Observability docs
- **VALIDATE**: All roles, views, and governance concepts from PDF addressed; objectives 3.1-3.4 checked off in objective-checklist.md

### Task 5: Domain 4 — Snowflake Document Processing (15%)
- **ACTION**: Write `domain-4-document-processing.md`
- **IMPLEMENT**: Cover objectives 4.1-4.4. AI_PARSE_DOCUMENT modes (OCR vs LAYOUT),
  parameters (page_split, page_limit). AI_EXTRACT (response format, prompt engineering).
  Document requirements. Pipeline orchestration (Streams + Tasks). Troubleshooting
  (GET_PRESIGNED_URL, privileges). Fine-tuning arctic-extract.
- **EXAM FOCUS**: OCR vs LAYOUT mode decision criteria, AI_PARSE_DOCUMENT vs AI_EXTRACT
  use case distinction (see sample question 3 from exam), pipeline pattern with Streams+Tasks,
  arctic-extract fine-tuning purpose
- **REFERENCES**: AI_PARSE_DOCUMENT docs, AI_EXTRACT docs, Streams/Tasks docs
- **VALIDATE**: Sample question 3 pattern is clearly taught; objectives 4.1-4.4 checked off in objective-checklist.md

### Task 6: Sample Questions
- **ACTION**: Write `sample-questions.md`
- **IMPLEMENT**: Include the 5 sample questions from the official PDF with explained answers,
  then generate 5 additional practice questions per domain (20 total) in the same style
- **EXAM FOCUS**: Scenario-based questions testing decision-making, not just recall
- **VALIDATE**: Each question maps to a specific objective, correct answers have clear justification

## Testing Strategy
- Objective checklist: every PDF bullet checked off in `docs/reference/objective-checklist.md`
- Accuracy verification: key claims (function names, roles, parameters) cross-referenced against
  current Snowflake documentation via `cortex search docs`
- Cross-reference: documentation links are valid topic references
- Sample question alignment: study content would prepare someone to answer the sample questions

## Acceptance Criteria
- [ ] All 4 domains covered with proportional depth (Domain 2 deepest)
- [ ] Every objective (1.1, 1.2, 2.1-2.5, 3.1-3.4, 4.1-4.4) has its own section
- [ ] Key distinctions and exam tips called out for high-value topics
- [ ] Documentation references provided for further reading
- [ ] Sample questions included with explained answers
- [ ] Files are self-contained — readable without the original PDF
- [ ] Exam format details either confirmed via source/docs or explicitly noted as unspecified

## Risks
| Risk | Likelihood | Impact | Mitigation |
| --- | --- | --- | --- |
| Documentation links become stale | Medium | Low | Use topic-based references rather than exact URLs |
| Exam content evolves beyond April 2026 guide | Low | Medium | Plan is based on C02 version; note version in README |
| Depth imbalance across domains | Medium | Medium | Use exam weightings to calibrate content volume |
| Content accuracy drift from current Snowflake features | Medium | High | Cross-reference key claims against live docs using `cortex search docs` during authoring; flag items with low confidence for manual verification |
| Exam format metadata missing from PDF | Medium | Low | Verify via `cortex search docs`; omit and note as unspecified if unconfirmed (see Task 1 fallback) |
