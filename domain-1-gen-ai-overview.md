# Domain 1.0 — Snowflake for Gen AI Overview (18%)

This domain establishes the landscape: what's in the Snowflake Cortex suite, the interfaces
for using it, how to bring your own models, and platform-level capabilities (cross-region
inference, MCP, CKE). The exam tests **differentiation** — knowing which feature solves which
problem — far more than deep syntax here.

---

## 1.1 Define Snowflake's Gen AI principles and features

### Snowflake Cortex
Snowflake Cortex is the umbrella for Snowflake's managed Gen AI/LLM capabilities. You access
hosted LLMs without managing GPUs, and your data never leaves Snowflake's governance boundary.

- **Cortex Models and Functions** — Fully-managed access to leading LLMs (Anthropic Claude,
  OpenAI, Meta Llama, Mistral, Snowflake Arctic, etc.) via SQL/Python functions. No
  infrastructure to provision.
- **Cortex Fine-tuning (Public Preview)** — Customize a base model on your labeled data to
  improve accuracy / lower cost vs. a large general model. Managed via the `FINETUNE` function /
  Snowsight AI & ML Studio.

### The four core Cortex "services" (know these cold)
| Service | What it does | Primary use case |
| --- | --- | --- |
| **Cortex Search** | Hybrid (vector + keyword) retrieval over text/documents | **RAG**, search over **unstructured data** (PDFs, docs, support tickets) |
| **Cortex Analyst** | Managed **text-to-SQL** over structured data via a semantic model | Natural-language BI on tables |
| **Cortex Agents** | Orchestrates LLM reasoning + tools (Search, Analyst, custom) | Multi-step tasks spanning structured + unstructured data |
| **Snowflake Intelligence** | No-code agent UI/experience in Snowsight | Business users chatting with their data |

- **Cortex Search — RAG use cases:** Provides the *retrieval* layer of RAG. Indexes a text
  column, then returns the most relevant chunks to ground an LLM's answer and reduce
  hallucinations.
- **Cortex Search — Unstructured data use cases:** Search/Q&A over PDFs, contracts, knowledge
  bases, logs — content with no fixed schema.
- **Cortex Analyst — Text-to-SQL use cases:** Business users ask questions in natural language;
  Analyst generates and runs SQL against a **semantic view/model**. Returns answers without the
  user writing SQL. Available as a REST API.
- **Cortex Agents:** Agentic orchestration — the agent decides which tool to call (e.g.,
  Cortex Search for documents, Cortex Analyst for tables) to answer a complex question.

> **Key Distinction — Search vs Analyst vs Agents vs Intelligence**
> - **Search** = unstructured retrieval (documents). **Analyst** = structured text-to-SQL (tables).
> - **Agents** = the orchestrator that can call *both* Search and Analyst plus other tools.
> - **Intelligence** = the packaged no-code UI that lets business users talk to agents.
> Exam scenarios hinge on *structured vs unstructured* and *single tool vs orchestration*.

### Developer & assistant tooling
- **Snowflake Cortex Code** — Agentic coding assistant for Snowflake development.
  - **Cortex Code in Snowsight UI** — In-browser assistant.
  - **Cortex Code Command Line (CLI)** — Terminal-based agent (`cortex` commands) for
    Snowflake operations, SQL authoring, and codebase work, with session management.
- **Snowflake Copilot Inline (Public Preview)** — Inline AI assistance (e.g., in worksheets)
  built on Cortex Models/Functions, Cortex Fine-tuning, and Cortex Search (RAG) to help write
  and complete SQL.

### Snowflake Intelligence
A no-code experience in Snowsight where business users interact with **agents** over their
governed data and documents — combining Cortex Analyst (structured) and Cortex Search
(unstructured) behind a chat UI.

### Different interfaces (how you invoke Cortex)
- **AI Studio** — Snowsight's point-and-click "AI & ML Studio" for building/testing
  (e.g., creating Cortex Search services, semantic models, fine-tuning, playground).
- **SQL** — Call functions like `AI_COMPLETE(...)` directly in queries.
- **REST API** — Call Cortex (Analyst, Search, Agents, inference) from any external application.

### Bringing Your Own Models (BYOM)
| Path | When to use |
| --- | --- |
| **Snowflake Model Registry (custom model)** | Log, version, and serve your own/custom ML or LLM models; call them via SQL/Python. Governed like any Snowflake object. |
| **Snowpark Container Services (SPCS)** | Run arbitrary containerized workloads (any model, framework, or runtime incl. GPUs) inside Snowflake — used to host/fine-tune open-source LLMs. |

> **Exam Tip:** "Run/fine-tune an *open-source* model with full control of the container/runtime"
> → **SPCS**. "Register, version, and serve a model for SQL calls" → **Model Registry**. They are
> often used together (train/serve on SPCS, govern via Registry).

---

## 1.2 Outline Gen AI capabilities in Snowflake

### Prompting
The instruction/context you send to an LLM. Quality of prompt drives quality of output;
prompting + retrieved context (RAG) is how you reduce hallucinations.

### Cortex AI functions
- **Vector-embedding** — Convert text into numeric vectors (via `AI_EMBED` / `EMBED_TEXT`)
  for semantic similarity, search, and RAG. Stored in the `VECTOR` data type.
- **Context windows** — The maximum tokens (input + output) a model can handle in one call.
  Large documents must be **chunked** to fit; exceeding the window truncates or errors.
  Use `AI_COUNT_TOKENS` to estimate.

### Cortex Search
- **Multi-index queries** — Query across multiple search services/indexes.
- **Access control requirements** — Querying a search service requires appropriate privileges
  (e.g., the `CORTEX_USER` database role plus USAGE on the service); results respect RBAC.
- **Different ways to use Cortex Search** — SQL, REST API, Python, and as a **tool inside a
  Cortex Agent** (retrieval for RAG).

### Cortex Analyst
- **Semantic Views** — The semantic model that maps business terms to tables/columns,
  defining dimensions, metrics, relationships, and synonyms. Analyst queries this, not raw tables.
- **Semantic Views Autopilot** — Assisted/automated generation of a semantic view to bootstrap
  the model from existing tables.
- **YAML Specification for Semantic Views** — Semantic models are defined in a YAML spec
  (tables, dimensions, measures, relationships, verified queries, custom instructions).
- **Verified Query (VQR)** — A repository of human-verified question→SQL pairs that Analyst
  reuses to boost accuracy and consistency for known questions.
- **Custom Instructions** — Free-text guidance that steers Analyst's behavior (business rules,
  defaults, terminology) across all questions.

### Cortex Agents
Agentic orchestration layer: plans steps and calls tools (Cortex Search, Cortex Analyst,
custom tools/functions) to fulfill complex, multi-step requests. Manageable as objects and
exposable via REST API / MCP.

### Snowflake Intelligence
No-code agent surface (see 1.1) — the consumer-facing way to use Agents.

### Cross-region inference
When a requested model isn't available in your account's region, Snowflake can route the
inference request to another region.
- **`CORTEX_ENABLED_CROSS_REGION` parameter** — Account-level parameter controlling whether
  cross-region routing is allowed and to which region group (e.g., `DISABLED`, `ANY_REGION`,
  `AWS_US`). Must be enabled for models not hosted in your region.
- **Considerations** — Added **latency** (request travels cross-region), **availability** of
  models per region, and **data residency/compliance** implications (data leaves the region
  for processing).

> **Exam Tip:** If a model "is not available in your region" error appears, the fix is to set
> `CORTEX_ENABLED_CROSS_REGION`. Remember it's an **account-level** parameter and has data
> residency consequences.

### REST APIs
Cortex Analyst, Search, Agents, and inference are all available as REST APIs, enabling
integration into external apps and services.

### Model Context Protocol (MCP)
An open protocol for connecting LLM agents to external tools/data sources. Snowflake supports
MCP so Cortex Agents can expose/consume tools in a standard way (and external MCP clients can
reach Snowflake Cortex services).

### Snowflake Cortex Code — CLI commands
The Cortex Code CLI provides commands for Snowflake operations (connections, search of catalog
objects and docs, semantic views, agents, SQL execution, artifacts) and persistent agent
sessions for development tasks.

### Cortex Knowledge Extensions (CKE)
A way to **share curated knowledge/document collections** (via Snowflake Marketplace/sharing)
that providers can offer and consumers can plug into RAG/Search workflows — extending an
agent's knowledge with third-party or proprietary content without copying data.

---

## Documentation References
- Snowflake Cortex LLM Functions / AISQL overview
- Cortex Search Overview · Cortex Analyst · Cortex Agents
- Cortex Analyst — Custom Instructions · YAML spec for semantic views · Semantic Views Autopilot
- Model Registry Overview · Model Registry — Bring Your Own Model Types · Snowflake ML Overview
- Cortex Code in Snowsight · Cortex Code CLI (Key Features, Commands, Session Management)
- Cross-Region Inference · Model Context Protocol (MCP) for Cortex Agents
- Cortex Knowledge Extensions Overview · Snowflake Copilot Inline · Vector Embeddings
