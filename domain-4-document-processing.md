# Domain 4.0 — Snowflake Document Processing (15%)

The smallest domain, but high-value because it's concrete. Centers on two functions —
**`AI_PARSE_DOCUMENT`** (parse documents to text/markdown + layout) and **`AI_EXTRACT`**
(pull structured fields) — plus building automated pipelines and troubleshooting.

---

## 4.1 Use document parsing functions

### `AI_PARSE_DOCUMENT`
Extracts text, data, layout elements, and images from documents stored on an **internal or
external stage**. Preserves reading order and structural elements (tables, headers).

**Processing modes:**
| Mode | Use it for |
| --- | --- |
| **`OCR` mode** | Fast, plain **text extraction** from documents/images — when you just need the raw text and don't care about structure. |
| **`LAYOUT` mode** | **Preferred for most/complex documents.** Extracts structured content — **tables, headers, layout relationships** — as markdown. **Required for image extraction** (`extract_images: true`). |

**Key parameters:**
- **`page_split`** — return results **split per page** (boolean) so you get page-level output
  instead of one blob — useful for page-aware chunking/citation.
- **`page_limit`** — cap the number of pages processed (control cost / handle large docs).

Example shape:
```sql
SELECT AI_PARSE_DOCUMENT(
  TO_FILE('@my_stage', 'contract.pdf'),
  {'mode': 'LAYOUT', 'page_split': true}
);
```

### `AI_EXTRACT`
Extracts **specific structured fields** from text/documents using a schema (list or map of
fields/questions). Returns structured (JSON) output.
- **Response format** — define the keys/fields (and optionally questions) you want back; output
  conforms to that structure for easy downstream SQL.
- **How to prompt / Prompt engineering** — phrase each field as a clear question or precise key
  name; specific, unambiguous prompts yield more accurate extractions.

Example shape:
```sql
SELECT AI_EXTRACT(
  file => TO_FILE('@my_stage', 'invoice.pdf'),
  responseFormat => ['vendor_name', 'total_amount', 'line_items']
);
```

> **Key Distinction — `AI_PARSE_DOCUMENT` vs `AI_EXTRACT` (official Q3!):**
> - **`AI_PARSE_DOCUMENT`** = convert a **whole document** into text/markdown, preserving
>   **layout/tables** (LAYOUT mode). Use when you want the **full content** searchable.
> - **`AI_EXTRACT`** = pull **specific named fields** via a schema. Use when you want
>   **structured data** (vendor, total, dates) out of the document.
> - Scenario: invoices needing specific fields → **`AI_EXTRACT` with a schema**; contracts
>   needing full text→searchable markdown with tables → **`AI_PARSE_DOCUMENT` in LAYOUT mode**.

---

## 4.2 Prepare and manage documents and implement extracting workflows

- **Upload documents** — land files in a **stage** (internal named/internal user/table stage,
  or external stage on S3/Azure/GCS). Reference them with `TO_FILE` / scoped file URLs.
- **Requirements (formats, size limits)** — supported types include PDF, DOCX, PPTX, common
  image formats (PNG/JPEG/TIFF), HTML, TXT, etc.; there are per-file **size and page limits**.
  Confirm current limits in the AI_PARSE_DOCUMENT docs before relying on edge cases.

> **Exam Tip:** Documents must be on a **stage** to be processed; you pass a `FILE` reference
> (via `TO_FILE` or `BUILD_SCOPED_FILE_URL`), not raw bytes.

---

## 4.3 Build automated document processing pipelines with Cortex AI integration

Automate "new file arrives → parse/extract → store results" with native orchestration.

### Orchestration of Snowflake tooling
- **Streams** — track **new/changed rows** (e.g., a directory table over a stage, or a staging
  table of file paths). A stream captures newly uploaded documents to process incrementally.
- **Tasks** — **scheduled or triggered** execution. A task consumes the stream and runs
  `AI_PARSE_DOCUMENT`/`AI_EXTRACT`, writing results to a target table. Chain tasks (DAG) or use
  a **triggered task** that fires when the stream has data.

Typical pattern:
```
Stage (directory table) → Stream (new files) → Task (AI_PARSE_DOCUMENT / AI_EXTRACT) → Results table
```

> **Exam Tip:** The canonical Snowflake document-pipeline answer is **Streams + Tasks** for
> incremental, automated processing. (Dynamic Tables are an alternative declarative option.)

---

## 4.4 Troubleshoot and optimize document processing

- **Extracting query errors — `GET_PRESIGNED_URL`** — document functions need a readable file
  reference. Generate a temporary URL with **`GET_PRESIGNED_URL`** (or `BUILD_SCOPED_FILE_URL`)
  when access/URL errors occur; ensure the stage and file path are correct.
- **Requirements and privileges** — caller needs **READ/USAGE on the stage**, the
  `SNOWFLAKE.CORTEX_USER` role for the AI functions, and a warehouse to run the query. Missing
  stage privileges or wrong file URLs are common failure causes.
- **Cost and best practice considerations** — use **`page_limit`** to bound large documents,
  pick **OCR** over LAYOUT when you only need text (cheaper/faster), batch with **`TRY_*`**
  patterns, and parse once → reuse the stored output downstream.
- **Fine-tuning `arctic-extract` models** — for specialized/high-accuracy extraction on your
  document types, **fine-tune the `arctic-extract` model** (Snowflake's document extraction
  model) on your labeled examples to improve field accuracy beyond the base model.

> **Exam Tip:** A document function failing to read a file → check the **file URL / presigned
> URL (`GET_PRESIGNED_URL`)** and **stage privileges** first. For better extraction accuracy on
> your specific documents → **fine-tune `arctic-extract`**.

---

## Documentation References
- [AI_PARSE_DOCUMENT (Parsing Documents)](https://docs.snowflake.com/en/user-guide/snowflake-cortex/parse-document)
- [AI_EXTRACT / Document Extraction](https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-extraction)
- [GET_PRESIGNED_URL](https://docs.snowflake.com/en/sql-reference/functions/get_presigned_url)
- [Introduction to Streams](https://docs.snowflake.com/en/user-guide/streams-intro) · [Introduction to Tasks / Data Pipelines](https://docs.snowflake.com/en/user-guide/tasks-intro)
- [Arctic-Extract Fine-tuning](https://docs.snowflake.com/en/user-guide/snowflake-cortex/arctic-extract-finetuning) · [Cortex Fine-tuning](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-finetuning)
