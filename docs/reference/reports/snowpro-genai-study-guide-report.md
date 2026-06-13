# Implementation Report: SnowPro Specialty: Gen AI (C02) Study Guide

## Summary
Built a complete, exam-focused study guide for the SnowPro Specialty: Gen AI Certification (C02),
organized as per-domain markdown files at the repo root. Extracted every objective from the
source PDF into a validation checklist, authored four domain files with depth proportional to
exam weighting (18/38/29/15%), a README index, and a sample-questions file containing the 5
official questions (with explanations) plus 20 original practice questions. Key claims were
cross-referenced against live Snowflake documentation via `cortex search docs`.

## Assessment Vs Reality
| Metric | Predicted | Actual |
| --- | --- | --- |
| Complexity | Medium | Medium |
| Files Changed | 6 (index + 4 domains + samples) | 7 created (6 planned + objective-checklist.md per Task 0) |

## Tasks Completed
| # | Task | Status | Notes |
| --- | --- | --- | --- |
| 0 | Extract objective checklist | Done | `docs/reference/objective-checklist.md`, 161 checkable bullets across 4 domains + 5 sample Qs |
| 1 | README.md index | Done | Overview, weightings table (18/38/29/15%), file links, decision cheat sheet, study tips |
| 2 | Domain 1 (18%) | Done | Objectives 1.1–1.2; Cortex suite differentiation, interfaces, BYOM, MCP, CKE, cross-region |
| 3 | Domain 2 (38%) | Done | Objectives 2.1–2.5; full AI/vector/helper function catalog, RAG vs text-to-SQL, chat apps, SPCS + Registry |
| 4 | Domain 3 (29%) | Done | Objectives 3.1–3.4; access control, 4 Cortex roles, 7 usage views, AI observability/TruLens |
| 5 | Domain 4 (15%) | Done | Objectives 4.1–4.4; AI_PARSE_DOCUMENT modes, AI_EXTRACT, Streams+Tasks, troubleshooting, arctic-extract |
| 6 | Sample questions | Done | 5 official (verified answers D/C/D/A/A) + 20 practice (5 per domain) with explanations |

## Validation Results
| Check | Result | Notes |
| --- | --- | --- |
| Checklist term coverage | PASS | Programmatic scan: 161/161 objective terms present in the correct domain file (1 wording gap "token cost implications" found and fixed) |
| README links resolve | PASS | All 5 .md links exist |
| Domain weightings match PDF | PASS | 18/38/29/15% |
| Proportional depth | PASS | Domain 2 deepest (204 lines); Domain 3 table-dense |
| Key distinctions / exam tips | PASS | 18 callouts across the 4 domain files |
| Official sample answers | PASS | Match PDF answer key (D, C, D, A, A) |
| Doc accuracy spot-checks | PASS | Verified AI_PARSE_DOCUMENT LAYOUT/OCR behavior and `CORTEX_MODELS_ALLOWLIST` against live docs |

## Files Changed
| File | Action | Notes |
| --- | --- | --- |
| `docs/reference/objective-checklist.md` | Create | Validation artifact, all boxes checked |
| `README.md` | Create | Index + cheat sheet |
| `domain-1-gen-ai-overview.md` | Create | 18% domain |
| `domain-2-gen-ai-functions.md` | Create | 38% domain |
| `domain-3-gen-ai-governance.md` | Create | 29% domain |
| `domain-4-document-processing.md` | Create | 15% domain |
| `sample-questions.md` | Create | 25 questions w/ explanations |

## Deviations From Plan
- **Checklist marking timing:** The plan suggested checking off bullets after each domain file.
  I authored all content first, then verified coverage programmatically and checked off all 161
  boxes in one validation pass. Net result is identical and more reliable (machine-verified).
- **Doc verification scope:** Cross-referenced high-uncertainty claims (parsing modes, allowlist
  parameter name, Cortex roles) rather than every claim, since most content is directly grounded
  in the authoritative source PDF.

## Issues Encountered
- One wording gap ("token cost implications" in Domain 3) surfaced by the coverage scan; fixed
  by making the phrase explicit.
- Note on accuracy confidence: a few items reflect rapidly-evolving preview features
  (Copilot Inline, Cortex Fine-tuning, REST API auth specifics). These are described at the
  conceptual level the exam tests; verify exact syntax against current docs before exam day.

## Next Step
- code review
- (optional) commit the new study-guide files
