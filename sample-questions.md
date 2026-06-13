# Sample & Practice Questions — SnowPro Specialty: Gen AI (C02)

Scenario-based practice in the style of the exam. **Answers and explanations follow each
section** — try to answer before scrolling. Correct option marked with `*` in the answer key.

---

## Part 1 — Official Sample Questions (from the study guide)

**1. A Gen AI Specialist needs to analyze the daily costs incurred for AI services in Snowflake.
Which query retrieves credit consumption from Snowflake's metadata objects for data usage?**
- A. `SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY WHERE SERVICE_TYPE='AI_SERVICES';`
- B. `SELECT * FROM SNOWFLAKE.INFORMATION_SCHEMA.METERING_HISTORY WHERE SERVICE_TYPE='AI_SERVICES';`
- C. `SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.METERING_HISTORY WHERE SERVICE_TYPE='AI_SERVICES';`
- D. `SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.METERING_DAILY_HISTORY WHERE SERVICE_TYPE='AI_SERVICES';`

**2. What is the primary role of memory in a multi-turn chat conversation using a Gen AI model
in Snowflake Cortex Analyst?**
- A. To securely store user credentials
- B. To increase the speed of response generation
- C. To maintain context throughout multiple requests
- D. To limit the number of tokens processed for each request

**3. A company processes multiple document types with different extraction logic. Invoices need
specific fields (vendor name, total, line items). Legal contracts need full text converted to
searchable Markdown with table structures preserved. How should they structure the workflow?**
- A. `AI_PARSE_DOCUMENT` for all docs, then SQL filters to extract invoice fields and preserve contract layouts
- B. `AI_EXTRACT` for all docs, with separate response-format schemas for invoices and contracts
- C. `AI_PARSE_DOCUMENT` in OCR mode for invoices; `AI_EXTRACT` for contracts to pull full content
- D. `AI_EXTRACT` with a schema for invoices; `AI_PARSE_DOCUMENT` in LAYOUT mode for contracts

**4. An organization needs a chatbot answering customer queries by searching unstructured PDF
documents. Which Snowflake capability enables this?**
- A. Cortex Search
- B. Cortex Analyst
- C. Cortex Agent
- D. Snowflake Intelligence

**5. Which Snowflake Cortex LLM function should generate an instructional lesson plan based on a
prompt?**
- A. `AI_COMPLETE`
- B. `AI_EXTRACT`
- C. `SUMMARIZE`
- D. `AI_TRANSLATE`

### Answer Key — Part 1
1. **D*** — Daily AI-services credit consumption lives in **`ACCOUNT_USAGE.METERING_DAILY_HISTORY`**
   filtered on `SERVICE_TYPE='AI_SERVICES'`. A (QUERY_HISTORY) has no credit/service-type cost
   data; B uses INFORMATION_SCHEMA (no METERING_HISTORY there + not account-wide); C
   (METERING_HISTORY) is hourly, not the *daily* object asked for. *(Domain 3.3)*
2. **C*** — Memory exists to **maintain context across turns**. The model is stateless per call;
   you preserve context by resending the conversation history (messages array). Not credentials
   (A), speed (B), or token limiting (D). *(Domain 2.3)*
3. **D*** — Two different needs: invoices = **structured fields** → `AI_EXTRACT` with a schema;
   contracts = **full text + tables as markdown** → `AI_PARSE_DOCUMENT` in **LAYOUT** mode.
   OCR mode (C) loses table structure; AI_EXTRACT can't return full-fidelity layout markdown (B). *(Domain 4.1)*
4. **A*** — RAG/search over **unstructured PDF documents** = **Cortex Search**. Analyst (B) is
   text-to-SQL over structured data; Agent (C) orchestrates tools (often *using* Search but not
   the base retrieval capability); Intelligence (D) is the no-code UI layer. *(Domains 1.1 / 2.2)*
5. **A*** — Free-form generation from a prompt = **`AI_COMPLETE`**. `AI_EXTRACT` pulls fields,
   `SUMMARIZE` condenses existing text, `AI_TRANSLATE` changes language. *(Domain 2.1)*

---

## Part 2 — Domain 1 Practice (Snowflake for Gen AI Overview)

**1.1** You need business users to ask natural-language questions about a curated set of
internal policy **PDFs** and get cited answers. Which is the best fit?
- A. Cortex Analyst   B. Cortex Search   C. Model Registry   D. AI_TRANSLATE

**1.2** Calling `AI_COMPLETE` returns an error that the requested model is not available in your
account's region. What is the correct remediation?
- A. Switch warehouses to a larger size
- B. Set the `CORTEX_ENABLED_CROSS_REGION` account parameter
- C. Re-create the model in your region with the Model Registry
- D. Grant the `CORTEX_USER` role to the user

**1.3** A team wants to run and **fine-tune an open-source LLM** with full control over the
container, framework, and GPU runtime inside Snowflake. Which should they use?
- A. Cortex Analyst   B. Cortex Search   C. Snowpark Container Services   D. Cortex Guard

**1.4** Which Cortex Analyst artifact maps business terms to physical tables/columns and defines
dimensions, metrics, and relationships?
- A. Verified Query Repository   B. Semantic view (YAML spec)   C. Event table   D. Compute pool

**1.5** A developer wants Cortex Agents to expose and consume tools using an open,
interoperable standard. Which capability supports this?
- A. Model Context Protocol (MCP)   B. Cross-region inference   C. AI Studio   D. Cortex Guard

### Answer Key — Domain 1
1.1 **B*** Cortex Search = RAG/search over unstructured PDFs. Analyst is for structured tables. *(1.1)*
1.2 **B*** `CORTEX_ENABLED_CROSS_REGION` routes inference to a region that hosts the model; mind data residency/latency. *(1.2)*
1.3 **C*** SPCS gives container/GPU/runtime control to host and fine-tune open-source models. *(1.1)*
1.4 **B*** The **semantic view**, defined in a YAML spec, is what Analyst queries. VQR stores verified Q→SQL pairs. *(1.2)*
1.5 **A*** **MCP** is the open protocol for agent↔tool interoperability. *(1.2)*

---

## Part 3 — Domain 2 Practice (Snowflake Gen AI Functions)

**2.1** You must return JSON that strictly conforms to a fixed schema so downstream SQL can parse
it reliably. Which approach?
- A. `SUMMARIZE`   B. `AI_COMPLETE` with structured outputs (`response_format`)   C. `AI_TRANSLATE`   D. `VECTOR_COSINE_SIMILARITY`

**2.2** A nightly pipeline runs an LLM function over millions of rows; a few malformed rows must
not fail the whole job. Which function?
- A. `AI_COMPLETE`   B. `TRY_COMPLETE`   C. `AI_AGG`   D. `PROMPT`

**2.3** For ranking text chunks by semantic relevance in a RAG retrieval step, which function is
the typical default?
- A. `VECTOR_L1_DISTANCE`   B. `VECTOR_COSINE_SIMILARITY`   C. `VECTOR_SUM`   D. `AI_SENTIMENT`

**2.4** A high-volume customer chat needs **predictable low latency** and reserved capacity for a
specific model. Which option addresses this?
- A. Cross-region inference   B. Provisioned Throughput   C. Object tagging   D. Semantic reranking

**2.5** You need a single summary that captures themes across **thousands of survey responses**
stored in one column. Which function is purpose-built for this?
- A. `SUMMARIZE`   B. `AI_SUMMARIZE_AGG`   C. `AI_CLASSIFY`   D. `AI_FILTER`

### Answer Key — Domain 2
2.1 **B*** Structured outputs force schema-conformant JSON. *(2.1)*
2.2 **B*** `TRY_COMPLETE` returns NULL on failure instead of erroring — safe for batch. *(2.1 / 2.4)*
2.3 **B*** Cosine similarity is the standard for embedding relevance ranking. *(2.1 / 2.2)*
2.4 **B*** Provisioned Throughput reserves dedicated capacity for predictable latency/cost. *(2.2)*
2.5 **B*** `AI_SUMMARIZE_AGG` aggregates across many rows; `SUMMARIZE` handles one input. *(2.1)*

---

## Part 4 — Domain 3 Practice (Snowflake Gen AI Governance)

**3.1** An admin must ensure only two approved models can be used account-wide. Which mechanism?
- A. `CORTEX_MODELS_ALLOWLIST` parameter   B. `AI_REDACT`   C. Event tables   D. `page_limit`

**3.2** Which database role specifically enables use of **Cortex Analyst**?
- A. `CORTEX_USER`   B. `CORTEX_ANALYST_USER`   C. `CORTEX_AGENT_USER`   D. `CORTEX_EMBED_USER`

**3.3** You must remove **PII from text** before it is sent to an LLM. Which feature?
- A. Cortex Guard   B. `AI_REDACT`   C. Provisioned Throughput   D. VQR

**3.4** A team wants to evaluate the **groundedness and relevance** of their RAG application's
answers. Which Snowflake capability and SDK?
- A. METERING_HISTORY   B. AI Observability with the TruLens SDK   C. Object tagging   D. Cortex Search

**3.5** Which view tracks **Cortex Search** daily usage/credits specifically?
- A. `CORTEX_ANALYST_USAGE_HISTORY`   B. `CORTEX_SEARCH_DAILY_USAGE_HISTORY`   C. `METERING_DAILY_HISTORY`   D. `CORTEX_REST_API_USAGE_HISTORY`

### Answer Key — Domain 3
3.1 **A*** `CORTEX_MODELS_ALLOWLIST` (account-level) controls which models are usable; pair with RBAC for per-role control. *(3.1)*
3.2 **B*** Service→role mapping: Analyst = `CORTEX_ANALYST_USER`. *(3.2)*
3.3 **B*** `AI_REDACT` removes sensitive/PII data; Cortex Guard filters harmful output (different purpose). *(3.1)*
3.4 **B*** AI Observability + **TruLens SDK** computes RAG-triad metrics (context relevance, groundedness, answer relevance). *(3.4)*
3.5 **B*** `CORTEX_SEARCH_DAILY_USAGE_HISTORY` is Search-specific; METERING_DAILY_HISTORY is all services. *(3.3)*

---

## Part 5 — Domain 4 Practice (Snowflake Document Processing)

**4.1** You need to convert scanned contracts into searchable **Markdown that preserves tables
and headers**. Which function and mode?
- A. `AI_EXTRACT` with a schema   B. `AI_PARSE_DOCUMENT` in OCR mode   C. `AI_PARSE_DOCUMENT` in LAYOUT mode   D. `SUMMARIZE`

**4.2** You want to automatically process **newly uploaded** documents on a stage with no manual
intervention. Which native orchestration pattern?
- A. Streams + Tasks   B. Object tagging   C. Cross-region inference   D. Compute pools only

**4.3** An `AI_PARSE_DOCUMENT` call fails because the function can't read the file. What should
you check first?
- A. The warehouse size   B. The presigned/scoped file URL and stage privileges (`GET_PRESIGNED_URL`)   C. The model allowlist   D. The semantic view

**4.4** To bound cost when parsing very large PDFs where only the first pages matter, which
parameter helps?
- A. `page_limit`   B. `response_format`   C. `guardrails`   D. `CORTEX_ENABLED_CROSS_REGION`

**4.5** A company wants higher extraction accuracy on its niche document layouts. What is the
recommended optimization?
- A. Increase chunk size   B. Fine-tune the `arctic-extract` model   C. Use OCR mode always   D. Switch to Cortex Analyst

### Answer Key — Domain 4
4.1 **C*** LAYOUT mode preserves tables/headers and outputs markdown; OCR is plain text. *(4.1)*
4.2 **A*** Streams (detect new files) + Tasks (run the AI functions) is the canonical pipeline. *(4.3)*
4.3 **B*** File-read failures are usually a bad file URL or missing stage privileges; use `GET_PRESIGNED_URL`/scoped URLs. *(4.4)*
4.4 **A*** `page_limit` caps pages processed, controlling cost. *(4.1 / 4.4)*
4.5 **B*** Fine-tune `arctic-extract` on labeled examples for document-specific accuracy. *(4.4)*

---

> **Study reminder:** The exam rewards reasoning about *which* feature/function fits a scenario.
> For every question, ask: **structured vs unstructured data? single function vs orchestration?
> generate vs extract vs parse? safety vs privacy vs cost vs quality?**
