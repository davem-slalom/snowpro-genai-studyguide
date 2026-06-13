# Domain 3.0 — Snowflake Gen AI Governance (29%)

The second-largest domain. Covers controlling **which models** can be used and **who** can use
them (access control + RBAC roles), **tracking and optimizing cost**, and **AI observability**
(evaluating Gen AI app quality). Expect heavy recall of role names and usage-history views.

---

## 3.1 Set up model access controls

### Limits on which models can be used
- **Restrict access to specific models** — control the set of LLMs available in the account.
- **Account-level allowlist parameter** — **`CORTEX_MODELS_ALLOWLIST`** (set by ACCOUNTADMIN)
  defines which models are usable (e.g., `'All'`, `'None'`, or a comma-separated list like
  `'mistral-large2,llama3.1-70b'`). Combine with RBAC for per-role model access.
- **Application roles** — used to control model/feature access where features ship as
  applications (grant/revoke the app's roles).

### Control model access
- **Role-Based Access Control (RBAC)** — grant the relevant Cortex **database roles**
  (e.g., `SNOWFLAKE.CORTEX_USER`) to allow function use; revoke to deny. Standard Snowflake
  RBAC governs every Cortex object (services, semantic models, agents).

### Data safety and security considerations
- **Cross-region inference** — enabling `CORTEX_ENABLED_CROSS_REGION` sends data to another
  region for processing → weigh data-residency/compliance.
- **Guardrails (Cortex Guard)** — filters unsafe/harmful model responses; enable via the
  `guardrails` option on completion functions.
- **Sensitive data management (`AI_REDACT`)** — detect and redact PII before/after model
  processing to avoid leaking sensitive data into prompts or outputs.
- **Methods to reduce hallucinations and bias** — ground responses with **RAG / Cortex Search**,
  use **verified queries** (Analyst), fine-tune, constrain with **structured outputs**, and
  add **custom instructions/guardrails**.
- **REST API authentication methods** — authenticate Cortex REST calls via key-pair (JWT),
  OAuth, or programmatic access tokens (PAT).

> **Key Distinction — Guardrails vs AI_REDACT:** **Guardrails (Cortex Guard)** filter
> **harmful/unsafe model output**; **`AI_REDACT`** removes **sensitive/PII data** from text.
> Different problems — safety vs privacy.

---

## 3.2 Grant and revoke RBAC and privileges

### Individual privileges
Each Cortex service has specific privilege requirements:
- **Cortex Analyst** — `SNOWFLAKE.CORTEX_USER` + access to the semantic model/view and
  underlying tables (and any Search service it integrates with).
- **Cortex Search** — `SNOWFLAKE.CORTEX_USER` + USAGE on the search service (and warehouse to
  create/refresh it).
- **Cortex Agents** — privileges on the agent object plus access to every tool it calls
  (Search service, semantic model, functions).
- **Snowflake Intelligence** — access to the underlying agents and their tools/data.

### Roles (memorize these database roles in the `SNOWFLAKE` database)
| Role | Grants ability to… |
| --- | --- |
| **`CORTEX_USER`** | Use Cortex LLM/AISQL functions (AI_COMPLETE, etc.). Granted to PUBLIC by default in many accounts; revoke to restrict. |
| **`CORTEX_ANALYST_USER`** | Use Cortex Analyst. |
| **`CORTEX_AGENT_USER`** | Use Cortex Agents. |
| **`CORTEX_EMBED_USER`** | Use embedding functionality (e.g., EMBED_TEXT). |

Grant with `GRANT DATABASE ROLE SNOWFLAKE.CORTEX_USER TO ROLE <your_role>;` and revoke to
remove access. Use `REVOKE` on application roles to pull back app-granted access.

> **Exam Tip:** Map service → role: Analyst→`CORTEX_ANALYST_USER`, Agents→`CORTEX_AGENT_USER`,
> embeddings→`CORTEX_EMBED_USER`, general LLM functions→`CORTEX_USER`.

---

## 3.3 Manage, monitor, and optimize Snowflake Cortex costs

### Cost levers per service
- **Cortex Agents** — **limit token usage** (cap input/output tokens, restrict tools/models).
- **Cortex Search** — multiple cost components: **virtual warehouse** (build/refresh),
  **`EMBED_TEXT`** (embedding generation), **serving** (query-time compute), and **indexing/storage**.
- **Cortex Analyst** — priced per request/message; control via question volume and model.
- **Cortex AI functions** — **minimize tokens** (shorter prompts/inputs). **Token cost
  implications:** cost scales directly with **tokens processed** (input + output), so trimming
  prompt/context and choosing right-sized models lowers spend.
- **Snowpark Container Services** — costs driven by **compute pools** (node type × uptime);
  suspend/auto-resume idle pools.

### Tracking model usage and consumption (ACCOUNT_USAGE views — memorize)
| View | Tracks |
| --- | --- |
| **`CORTEX_ANALYST_USAGE_HISTORY`** | Cortex Analyst request usage |
| **`CORTEX_AISQL_USAGE_HISTORY`** | AISQL / Cortex AI function usage (tokens, credits) |
| **`CORTEX_SEARCH_DAILY_USAGE_HISTORY`** | Cortex Search daily usage/credits |
| **`CORTEX_REST_API_USAGE_HISTORY`** | Cortex REST API usage |
| **`CORTEX_PROVISIONED_THROUGHPUT_USAGE_HISTORY`** | Provisioned throughput usage |
| **`METERING_DAILY_HISTORY`** | Daily credit consumption by service type (incl. AI_SERVICES) |
| **`METERING_HISTORY`** | Hourly credit consumption by service |

- **Usage quotas** — set/monitor consumption limits.
- **Object tagging** — apply tags to attribute and monitor AI service costs by team/project.

> **Exam Tip (official Q1):** For **daily** AI services credit consumption, query
> **`SNOWFLAKE.ACCOUNT_USAGE.METERING_DAILY_HISTORY`** filtered on
> `SERVICE_TYPE='AI_SERVICES'`. Note: it's in **ACCOUNT_USAGE**, not INFORMATION_SCHEMA, and
> the **DAILY** variant for daily costs.

---

## 3.4 Use Snowflake AI observability tools

Evaluate and monitor the quality of Gen AI applications (RAG, agents, LLM apps) in Snowflake.

### AI observability features
- **Evaluation metrics** — e.g., the **RAG triad**: context relevance, groundedness, answer
  relevance; plus correctness, harmfulness, etc. (LLM-as-a-judge).
- **Comparisons** — compare runs/app versions side by side to pick the best config.
- **Tracing** — capture each step of an app's execution (retrieval, prompt, response) for debugging.
- **Logging** — record inputs/outputs/intermediate steps.
- **Event tables** — traces and logs are stored in **event tables** for analysis.

### Implementation methods
- **TruLens SDK** — open-source library integrated with Snowflake AI Observability to define
  evaluations, run them, and view metrics/traces in Snowsight.

> **Key Distinction:** **Observability/TruLens = evaluating app quality** (is the RAG answer
> grounded/relevant?), which is different from **cost monitoring** (usage-history views) and
> **safety** (Cortex Guard / AI_REDACT).

---

## Documentation References
- Overview of Access Control · Governance Overview · Control Model Access · Cortex LLM Allowlist
- LLM Functions — Required Privileges / Limiting Access to Specific Roles · Snowflake Database Roles
- GRANT DATABASE ROLE · REVOKE PRIVILEGE on APPLICATION ROLE · Cortex Analyst — Required Privileges
- LLM Functions — Cortex Guard · AI_REDACT / Redact PII · Opting Out (ACCOUNTADMIN and AI Features)
- Cost: Cortex Search — Cost Categories/Costs · Cortex Analyst — Cost Considerations · Model Registry — Cost Considerations · Complete Structured Outputs — Cost Considerations · Cortex Agents — Cost Considerations
- Usage views: CORTEX_SEARCH_DAILY_USAGE_HISTORY (Columns) · CORTEX_FUNCTIONS_USAGE_HISTORY
- AI Observability — Key Concepts / Reference / Evaluation Metrics · Evaluate AI Applications · TruLens (Blog) · LLM-as-a-Judge RAG Triad Metrics (Blog)
