# Domain 2.0 — Snowflake Gen AI Functions (38%)

**The largest domain — spend the most time here.** It covers the full AI/vector/helper function
catalog, choosing functions for structured vs unstructured use cases, building chat interfaces,
embedding Cortex in pipelines, and running third-party models (SPCS + Model Registry).

---

## 2.1 Apply AI functions in Snowflake

Cortex AISQL functions are callable in SQL (and Python). The newer **`AI_*`** functions are the
current generation; some legacy `SNOWFLAKE.CORTEX.*` names (e.g., `COMPLETE`, `SUMMARIZE`,
`SENTIMENT`, `EXTRACT_ANSWER`) still appear.

### General functions
| Function | Purpose |
| --- | --- |
| **`AI_COMPLETE`** | The core generation function — send a prompt (and optionally a model) and get a completion. Supports text, multimodal (images/files), and structured output. Use for free-form generation, Q&A, classification-by-prompt, summarization-by-prompt, etc. |
| **`COMPLETE` Structured Outputs** | Force the model to return JSON conforming to a supplied **`response_format`** schema — guarantees parseable, typed output for downstream SQL. |

### Task-specific functions (purpose-built; simpler than prompting `AI_COMPLETE`)
| Function | What it does |
| --- | --- |
| **`AI_CLASSIFY`** | Classify text (or images) into your provided categories/labels. |
| **`AI_EXTRACT`** | Extract structured fields from text/documents using a **schema** of questions/keys. |
| **`AI_PARSE_DOCUMENT`** | Parse a document (from a stage) into text/markdown + layout (OCR or LAYOUT mode). |
| **`AI_SENTIMENT`** | Sentiment analysis (overall and/or per-aspect/entity). |
| **`SUMMARIZE`** | Summarize a single text input. |
| **`AI_SUMMARIZE_AGG`** | Aggregate-summarize across **many rows** (a column of texts) into one summary. |
| **`AI_TRANSLATE`** | Translate text between languages. |
| **`AI_EMBED`** | Generate a vector embedding for text (or image) — foundation for similarity/RAG. |
| **`AI_FILTER`** | Returns a boolean per row from a natural-language condition — use in `WHERE`/`CASE` to filter rows by meaning. |
| **`AI_AGG`** | Aggregate/reduce a column of text using a natural-language instruction (no context-window limit on the set, processed server-side). |
| **`AI_SIMILARITY`** | Compute semantic similarity between two inputs (text/image). |
| **`AI_TRANSCRIBE`** | Transcribe audio files to text. |
| **`AI_REDACT`** | Detect and redact sensitive info / PII from text. |

> **Key Distinction — `AI_AGG`/`AI_SUMMARIZE_AGG` vs `SUMMARIZE`:** The `*_AGG` functions
> reduce **across rows** and aren't bound by a single call's context window; `SUMMARIZE`
> works on **one** input value.
> **`AI_FILTER` vs `AI_CLASSIFY`:** `AI_FILTER` returns true/false for a condition;
> `AI_CLASSIFY` assigns one of N labels.

### Vector functions (operate on the `VECTOR` data type)
**Similarity / distance:**
- **`VECTOR_COSINE_SIMILARITY`** — cosine similarity (most common for embeddings/semantic search).
- **`VECTOR_INNER_PRODUCT`** — dot product.
- **`VECTOR_L1_DISTANCE`** — Manhattan distance.
- **`VECTOR_L2_DISTANCE`** — Euclidean distance.

**Transform / aggregate:**
- **`VECTOR_TRUNCATE`** — shorten a vector to fewer dimensions.
- **`VECTOR_NORMALIZE`** — normalize to unit length.
- **`VECTOR_SUM`, `VECTOR_MIN`, `VECTOR_MAX`, `VECTOR_AVG`** — element-wise aggregations across vectors.

> **Exam Tip:** For semantic search/RAG ranking, default to **`VECTOR_COSINE_SIMILARITY`**.
> Know that L1 = Manhattan, L2 = Euclidean.

### Helper functions
| Function | Purpose |
| --- | --- |
| **`AI_COUNT_TOKENS`** | Estimate token count for input → manage context windows and cost. |
| **`TRY_COMPLETE`** | Like `AI_COMPLETE`/`COMPLETE` but returns `NULL` instead of erroring on failure — safe for large batch pipelines. |
| **`SPLIT_TEXT_RECURSIVE_CHARACTER`** | Chunk long text by characters/separators (for embedding/RAG). |
| **`SPLIT_TEXT_MARKDOWN_HEADER`** | Chunk markdown by header structure — keeps sections intact. |
| **`TO_FILE`** | Create a `FILE` reference (from a stage path) to pass to multimodal functions like `AI_PARSE_DOCUMENT`/`AI_COMPLETE`. |
| **`PROMPT`** | Build a prompt object/template (e.g., interpolating file or column inputs) for AI functions. |

---

## 2.2 Perform data analysis given a use case

Pick the right service/function based on **structured vs unstructured** data.

### Unstructured data
**Functions:**
- **`AI_PARSE_DOCUMENT`** — turn documents into text/markdown for downstream use.
- **`AI_EXTRACT`** — pull specific structured fields out of documents/text.
- **`AI_SIMILARITY`** — compare items semantically.
- **`AI_COMPLETE`** — generate answers/analysis grounded in the text.

**Cortex Search (RAG over unstructured data):**
- **Recursive split text / markdown** — chunk documents with `SPLIT_TEXT_RECURSIVE_CHARACTER`
  or `SPLIT_TEXT_MARKDOWN_HEADER` before indexing.
- **Chunk sizing** — smaller chunks = more precise retrieval but more rows/cost and possible
  loss of context; larger chunks = more context but noisier matches and context-window pressure.
  Tune to the content and the model's window.
- **Embedding models** — Cortex Search embeds chunks (e.g., Snowflake Arctic-embed / supported
  models); choose based on quality, language, and dimensionality.
- **Semantic reranking** — Search re-ranks initial (hybrid vector+keyword) candidates for
  relevance before returning top results to ground the LLM.

**Multi-modal Analytics:**
- **Audio and Image Processing** — `AI_TRANSCRIBE` (audio→text), `AI_COMPLETE`/`AI_CLASSIFY`/
  `AI_EXTRACT` on images, and `AI_PARSE_DOCUMENT` for image-bearing documents.

### Structured data
**Functions:** `AI_COMPLETE` (e.g., generate narratives/labels from row values).

**Cortex Analyst (text-to-SQL):**
- **Verified Query Repository (VQR)** — store verified question→SQL pairs to improve accuracy
  and consistency.
- **Integration with Cortex Search** — Analyst can use a Cortex Search service to resolve
  literal/dimension values (e.g., map "NYC" to the exact stored value), improving SQL accuracy.
- **Suggested Questions** — surface example questions to guide users.
- **`CUSTOM_INSTRUCTIONS`** — embed business rules/defaults in the semantic model to steer SQL generation.

> **Key Distinction — RAG vs text-to-SQL:** Unstructured documents → **Cortex Search + RAG**.
> Structured tables answered in natural language → **Cortex Analyst (text-to-SQL)**. Need both,
> orchestrated → **Cortex Agents**.

### Performance considerations
- **Choosing a model** — match capability to task; bigger isn't always better.
- **Latency** — larger models are slower; smaller/distilled models or fine-tuned smaller models
  can hit latency targets.
- **Accuracy** — improve via **fine-tuning**, better prompts, and **RAG to reduce hallucinations**.
- **Model capability** — some tasks need reasoning/multimodal/long-context models.
- **Provisioned Throughput** — reserve dedicated capacity for predictable, high-volume,
  low-latency workloads (vs pay-per-token on-demand).

> **Exam Tip:** To **reduce hallucinations**, ground the model with retrieved facts (RAG /
> Cortex Search) and/or fine-tune; for **predictable high-volume latency/cost**, use
> **Provisioned Throughput**.

---

## 2.3 Build or interact with interfaces to chat with data

### Set up the Snowflake environment
- **Required privileges** — grant the **`SNOWFLAKE.CORTEX_USER`** database role to use Cortex
  LLM functions; add USAGE on warehouses, databases/schemas, and any Cortex Search service /
  semantic model the app uses.

### Invoke Cortex functions within application code
- Build chat apps with **Streamlit in Snowflake** calling `AI_COMPLETE` (or Cortex Analyst /
  Agents REST APIs) — runs inside Snowflake's governance boundary.

### Chat conversations
- **Multi-turn architecture** — to hold a conversation, you must pass prior turns back to the
  model; the model itself is stateless between calls.
- **Update parameters (messages array)** — maintain history by appending each user/assistant
  turn to a **`messages` array** sent with every request. This preserves **context** across turns.

> **Exam Tip (official Q2):** The role of "memory" in multi-turn chat is **to maintain context
> across multiple requests** — implemented by resending the conversation history (messages array).

### Snowflake Intelligence
The no-code chat surface for agents (see Domain 1) — a ready-made multi-turn interface over
governed data.

---

## 2.4 Apply Snowflake Cortex functions in data pipelines

Cortex functions are just SQL — embed them anywhere SQL runs (Dynamic Tables, Streams+Tasks,
stored procs, dbt).
- **Snowflake Cortex** — managed functions, no infra.
- **SQL interface** — call `AI_*` functions in `SELECT`/`INSERT`/`UPDATE`/`MERGE`.
- **Data extraction** — `AI_EXTRACT` / `AI_PARSE_DOCUMENT` to pull data from raw inputs.
- **Data enrichment** — add sentiment, classification, translation, embeddings as new columns.
- **Data augmentation** — generate synthetic/derived content with `AI_COMPLETE`.
- **Data transformations** — summarize, redact, normalize text at scale.

> **Exam Tip:** For large batch pipelines use **`TRY_COMPLETE`** so one bad row returns `NULL`
> instead of failing the whole job.

---

## 2.5 Run third-party models in Snowflake

Two complementary paths for bringing/serving your own (often open-source) models.

### Using Snowpark Container Services (SPCS)
Run any containerized workload (any framework, GPUs) inside Snowflake. Typical setup order:
1. **Environment setup** — create the database/schema, role, and **compute pool** + image repo.
2. **Docker images** — build your model-serving image and push it to the image repository.
3. **Specification files** — a YAML **service spec** defines containers, images, compute pool,
   endpoints, volumes, env vars.
4. **Create compute pool** — `CREATE COMPUTE POOL` (CPU/GPU node family + min/max nodes).
5. **Create image repository** — `CREATE IMAGE REPOSITORY` to host Docker images.

Use SPCS to **fine-tune and host open-source LLMs** with full runtime control.

### Using Snowflake Model Registry
Govern, version, and serve models for SQL/Python calls.
- **Logging the model** — `reg.log_model(...)` registers the model (artifacts, signature,
  dependencies, version) as a schema-level object.
- **Calling the model** — invoke `model.run(...)` (Python) or the generated SQL function to
  score data; can be served on a warehouse or SPCS.

> **Key Distinction — SPCS vs Model Registry:** SPCS = the **compute/runtime** to run/fine-tune
> containers (incl. open-source LLMs on GPUs). Model Registry = the **governance/serving catalog**
> for versioned models callable from SQL/Python. They're frequently combined.

---

## Documentation References
- [Snowflake Cortex AISQL Functions](https://docs.snowflake.com/en/user-guide/snowflake-cortex/aisql) · [Choosing a Model / Cortex Playground](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-playground) · [Provisioned Throughput](https://docs.snowflake.com/en/user-guide/snowflake-cortex/provisioned-throughput)
- [AI_COMPLETE](https://docs.snowflake.com/en/sql-reference/functions/ai_complete) · [Complete Structured Outputs](https://docs.snowflake.com/en/user-guide/snowflake-cortex/complete-structured-outputs) · [TRY_COMPLETE](https://docs.snowflake.com/en/sql-reference/functions/try_complete-snowflake-cortex)
- [AI_CLASSIFY](https://docs.snowflake.com/en/sql-reference/functions/ai_classify) · [AI_EXTRACT / Document Extraction](https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-extraction) · [AI_PARSE_DOCUMENT](https://docs.snowflake.com/en/user-guide/snowflake-cortex/parse-document) · [AI_SENTIMENT](https://docs.snowflake.com/en/sql-reference/functions/ai_sentiment)
- [AI_SUMMARIZE_AGG](https://docs.snowflake.com/en/sql-reference/functions/ai_summarize_agg) · [AI_TRANSLATE](https://docs.snowflake.com/en/sql-reference/functions/ai_translate) · [AI_AGG](https://docs.snowflake.com/en/sql-reference/functions/ai_agg) · [AI_FILTER](https://docs.snowflake.com/en/sql-reference/functions/ai_filter) · [AI_SIMILARITY](https://docs.snowflake.com/en/sql-reference/functions/ai_similarity) · [AI_TRANSCRIBE](https://docs.snowflake.com/en/sql-reference/functions/ai_transcribe) · [AI_REDACT](https://docs.snowflake.com/en/sql-reference/functions/ai_redact)
- [AI_EMBED](https://docs.snowflake.com/en/sql-reference/functions/ai_embed) · [AI_COUNT_TOKENS](https://docs.snowflake.com/en/sql-reference/functions/ai_count_tokens) · [SPLIT_TEXT_RECURSIVE_CHARACTER](https://docs.snowflake.com/en/sql-reference/functions/split_text_recursive_character-snowflake-cortex) · [VECTOR_COSINE_SIMILARITY](https://docs.snowflake.com/en/sql-reference/functions/vector_cosine_similarity) · [Vector Embeddings](https://docs.snowflake.com/en/user-guide/snowflake-cortex/vector-embeddings)
- [Cortex Analyst](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst) · [Verified Query Repository](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst/verified-query-repository) · [Custom Instructions](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst/custom-instructions)
- [Cortex Search Overview](https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-search/cortex-search-overview) · [About Streamlit in Snowflake](https://docs.snowflake.com/en/developer-guide/streamlit/about-streamlit)
- [Snowpark Container Services Overview](https://docs.snowflake.com/en/developer-guide/snowpark-container-services/overview) · [Model Registry Overview](https://docs.snowflake.com/en/developer-guide/snowflake-ml/model-registry/overview)
