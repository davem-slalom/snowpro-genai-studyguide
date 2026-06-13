# SnowPro Specialty: Gen AI (C02) — Objective Checklist

Validation artifact extracted from `SnowProGenAIStudyGuideC02.pdf` (Last Updated: April 30, 2026).
Every numbered bullet/sub-bullet from the exam guide is captured below. Check off each item
once it has a corresponding section/paragraph in the study guide files.

---

## Domain 1.0 — Snowflake for Gen AI Overview (18%)

### 1.1 Define Snowflake's Gen AI principles and features
- [x] Snowflake Cortex
- [x] Cortex Models and Functions
- [x] Cortex Fine-tuning (Public Preview)
- [x] Cortex Search — RAG use cases
- [x] Cortex Search — Unstructured data use cases
- [x] Cortex Analyst — Text-to-SQL use cases
- [x] Cortex Agents
- [x] Snowflake Cortex Code
- [x] Cortex Code in Snowsight UI
- [x] Cortex Code Command Line (CLI)
- [x] Snowflake Copilot Inline (Public Preview)
- [x] Copilot Inline — Cortex Models and Functions
- [x] Copilot Inline — Cortex Fine-tuning (Public Preview)
- [x] Copilot Inline — Cortex Search (RAG use cases)
- [x] Snowflake Intelligence
- [x] Different interfaces — AI Studio
- [x] Different interfaces — SQL
- [x] Different interfaces — REST API
- [x] Bringing your own models — Snowflake Model Registry (custom model)
- [x] Bringing your own models — Snowpark Container Services

### 1.2 Outline Gen AI capabilities in Snowflake
- [x] Prompting
- [x] Cortex AI functions — Vector-embedding
- [x] Cortex AI functions — Context Windows
- [x] Cortex Search — Multi-index queries
- [x] Cortex Search — Access control requirements
- [x] Cortex Search — Different ways to use Cortex Search
- [x] Cortex Analyst — Semantic Views
- [x] Cortex Analyst — Semantic Views Autopilot
- [x] Cortex Analyst — YAML Specification for Semantic Views
- [x] Cortex Analyst — Verified Query
- [x] Cortex Analyst — Custom Instructions
- [x] Cortex Agents
- [x] Snowflake Intelligence
- [x] Cross-region inference — CORTEX_ENABLED_CROSS_REGION parameter
- [x] Cross-region inference — Considerations (latency, availability)
- [x] REST APIs
- [x] Model Context Protocol (MCP)
- [x] Snowflake Cortex Code — Cortex Code CLI commands
- [x] Cortex Knowledge Extensions (CKE)

---

## Domain 2.0 — Snowflake Gen AI Functions (38%)

### 2.1 Apply AI functions in Snowflake
General functions:
- [x] AI_COMPLETE
- [x] COMPLETE Structured Outputs

Task-specific functions:
- [x] AI_CLASSIFY
- [x] AI_EXTRACT
- [x] AI_PARSE_DOCUMENT
- [x] AI_SENTIMENT
- [x] SUMMARIZE
- [x] AI_SUMMARIZE_AGG
- [x] AI_TRANSLATE
- [x] AI_EMBED
- [x] AI_FILTER
- [x] AI_AGG
- [x] AI_SIMILARITY
- [x] AI_TRANSCRIBE
- [x] AI_REDACT

Vector functions:
- [x] VECTOR_INNER_PRODUCT
- [x] VECTOR_L1_DISTANCE
- [x] VECTOR_L2_DISTANCE
- [x] VECTOR_COSINE_SIMILARITY
- [x] VECTOR_TRUNCATE
- [x] VECTOR_NORMALIZE
- [x] VECTOR_SUM
- [x] VECTOR_MIN
- [x] VECTOR_MAX
- [x] VECTOR_AVG

Helper functions:
- [x] AI_COUNT_TOKENS
- [x] TRY_COMPLETE
- [x] SPLIT_TEXT_RECURSIVE_CHARACTER
- [x] SPLIT_TEXT_MARKDOWN_HEADER
- [x] TO_FILE
- [x] PROMPT

### 2.2 Perform data analysis given a use case
Unstructured data:
- [x] Functions — AI_PARSE_DOCUMENT
- [x] Functions — AI_EXTRACT
- [x] Functions — AI_SIMILARITY
- [x] Functions — AI_COMPLETE
- [x] Cortex Search — Recursive split text markdown
- [x] Cortex Search — Chunk sizing
- [x] Cortex Search — Embedding models
- [x] Cortex Search — Semantic reranking
- [x] Multi-modal Analytics — Audio and Image Processing

Structured data:
- [x] Functions — AI_COMPLETE
- [x] Cortex Analyst — Verified Query Repository (VQR)
- [x] Cortex Analyst — Integration with Cortex Search
- [x] Cortex Analyst — Suggested Questions
- [x] Cortex Analyst — CUSTOM_INSTRUCTIONS

Performance considerations:
- [x] Choosing a model
- [x] Latency (e.g., model size)
- [x] Accuracy (e.g., fine-tuning, reducing hallucinations)
- [x] Model capability
- [x] Provisioned Throughput

### 2.3 Build or interact with interfaces to chat with data
- [x] Set up the Snowflake environment — Required privileges
- [x] Invoke Cortex functions within application code (e.g., Streamlit in Snowflake)
- [x] Chat conversations — Multi-turn architecture
- [x] Chat conversations — Update parameters (messages array for conversation history)
- [x] Snowflake Intelligence

### 2.4 Apply Snowflake Cortex functions in data pipelines
- [x] Snowflake Cortex
- [x] SQL interface
- [x] Data extraction
- [x] Data enrichment
- [x] Data augmentation
- [x] Data transformations

### 2.5 Run third-party models in Snowflake
Using Snowpark Container Services:
- [x] Environment setup
- [x] Docker images
- [x] Specification files
- [x] Create compute pool
- [x] Create image repository

Using Snowflake Model Registry:
- [x] Logging the model
- [x] Calling the model

---

## Domain 3.0 — Snowflake Gen AI Governance (29%)

### 3.1 Set up model access controls
- [x] Limits on which models can be used — Restrict access to specific models
- [x] Application roles — Control model access
- [x] Role-Based Access Control (RBAC)
- [x] Account-level allowlist parameter
- [x] Data safety/security — Cross region inference
- [x] Data safety/security — Guardrails
- [x] Data safety/security — Sensitive data management (e.g., AI_REDACT)
- [x] Data safety/security — Methods to reduce model hallucinations and bias
- [x] REST API authentication methods

### 3.2 Grant and revoke RBAC and privileges
- [x] Individual privileges — Specific requirements for Analyst, Search, Agents, Snowflake Intelligence
- [x] Role — CORTEX_USER
- [x] Role — CORTEX_ANALYST_USER
- [x] Role — CORTEX_AGENT_USER
- [x] Role — CORTEX_EMBED_USER

### 3.3 Manage, monitor, and optimize Snowflake Cortex costs
- [x] Cortex Agents — Limit token usage
- [x] Cortex Search — Different types of costs (virtual warehouse, EMBED_TEXT, serving, indexing)
- [x] Cortex Analyst (costs)
- [x] Cortex AI functions — Minimize tokens
- [x] Cortex AI functions — Token cost implications
- [x] Tracking costs of Snowpark Container Services — Compute pools
- [x] Tracking model usage — Usage quotas
- [x] CORTEX_ANALYST_USAGE_HISTORY
- [x] CORTEX_AISQL_USAGE_HISTORY
- [x] CORTEX_SEARCH_DAILY_USAGE_HISTORY
- [x] CORTEX_REST_API_USAGE_HISTORY
- [x] CORTEX_PROVISIONED_THROUGHPUT_USAGE_HISTORY
- [x] METERING_DAILY_HISTORY
- [x] METERING_HISTORY
- [x] Object tagging to monitor AI services costs

### 3.4 Use Snowflake AI observability tools
- [x] AI observability features — Evaluation metrics
- [x] AI observability features — Comparisons
- [x] AI observability features — Tracing
- [x] AI observability features — Logging
- [x] AI observability features — Event tables
- [x] Implementation methods — TruLens SDK

---

## Domain 4.0 — Snowflake Document Processing (15%)

### 4.1 Use document parsing functions
- [x] AI_PARSE_DOCUMENT — OCR mode
- [x] AI_PARSE_DOCUMENT — LAYOUT mode
- [x] AI_PARSE_DOCUMENT — page_split
- [x] AI_PARSE_DOCUMENT — page_limit
- [x] AI_EXTRACT — Response format
- [x] AI_EXTRACT — How to prompt / Prompt engineering

### 4.2 Prepare and manage documents and implement extracting workflows
- [x] Upload documents
- [x] Requirements (e.g., formats, size limits)

### 4.3 Build automated document processing pipelines with Cortex AI integration
- [x] Orchestration of Snowflake tooling — Streams
- [x] Orchestration of Snowflake tooling — Tasks

### 4.4 Troubleshoot and optimize document processing
- [x] Extracting query errors — GET_PRESIGNED_URL function
- [x] Requirements and privileges
- [x] Cost and best practice considerations
- [x] Fine-tuning arctic-extract models

---

## Sample Questions (official)
- [x] Q1 — METERING_DAILY_HISTORY for daily AI services cost (D)
- [x] Q2 — Memory maintains context across multi-turn chat (C)
- [x] Q3 — AI_EXTRACT (schema) for invoices vs AI_PARSE_DOCUMENT LAYOUT for contracts (D)
- [x] Q4 — Cortex Search for PDF document chatbot (A)
- [x] Q5 — AI_COMPLETE to generate a lesson plan from a prompt (A)
