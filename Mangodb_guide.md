# RAG Pipeline — Healthcare Test Case Intelligence Platform

> An enterprise-grade Retrieval-Augmented Generation (RAG) system for managing, searching, and AI-generating healthcare test cases using MongoDB Atlas Vector Search, Mistral AI embeddings, and Groq LLM.

---

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Backend — API Reference](#backend--api-reference)
- [Frontend — UI Modules](#frontend--ui-modules)
- [AI Prompts & LLM Usage](#ai-prompts--llm-usage)
- [10-Step RAG Pipeline](#10-step-rag-pipeline)
- [Environment Setup](#environment-setup)
- [Running the Application](#running-the-application)
- [MongoDB Atlas Index Configuration](#mongodb-atlas-index-configuration)
- [Key Impacted Code Locations](#key-impacted-code-locations)

---

## Overview

This platform enables QA teams to:

- Upload Excel test cases and user stories and convert them to structured JSON
- Generate Mistral AI vector embeddings and store them in MongoDB Atlas
- Search test cases using **Vector Search**, **BM25 keyword search**, or **Hybrid (BM25 + Vector)**
- Re-rank results using **Groq LLM** (Reciprocal Rank Fusion)
- Deduplicate and summarize results using AI
- Run a **10-step RAG pipeline** to auto-generate new test cases from user stories
- Evaluate generated test case quality using **DeepEval metrics**

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        React Frontend (Port 3000)               │
│  Convert → Embed → Preprocess → Search → Summarize → Generate  │
└────────────────────────────┬────────────────────────────────────┘
                             │ REST API
┌────────────────────────────▼────────────────────────────────────┐
│                    Express Server (Port 3001)                    │
│  File Upload │ Embeddings │ BM25 │ Vector │ Hybrid │ Rerank     │
└──────┬───────────────┬──────────────────────────────────────────┘
       │               │
┌──────▼──────┐  ┌─────▼──────────────────────────────────────┐
│  Mistral AI │  │         MongoDB Atlas                       │
│  (Embeddings│  │  Vector Index + BM25 Atlas Search Index     │
│  mistral-   │  │  Collections: test_cases, user_stories      │
│  embed)     │  └────────────────────────────────────────────┘
└─────────────┘
┌─────────────────────────────────────────────────────────────────┐
│                         Groq AI                                 │
│  Reranking: llama-3.2-3b-preview                                │
│  Summarization: llama-3.3-70b-versatile                         │
└─────────────────────────────────────────────────────────────────┘
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React 19, Material UI v7, notistack |
| Backend | Node.js, Express 4, ES Modules |
| Database | MongoDB Atlas (Vector Search + BM25) |
| Embeddings | Mistral AI (`mistral-embed`, 1024-dim) |
| LLM (Rerank/Summarize/Generate) | Groq AI (`llama-3.2-3b`, `llama-3.3-70b`) |
| File Processing | multer, xlsx |
| Quality Evaluation | DeepEval (external service, port 8000) |

---

## Project Structure

```
rag-mongo-demo-v8/
├── server/
│   └── index.js                  ← All Express API routes (single file)
├── client/
│   └── src/
│       ├── App.js                ← Root layout, sidebar navigation, theme
│       └── components/
│           ├── data/
│           │   ├── ConvertToJson.js       ← Excel upload UI
│           │   └── EmbeddingsStore.js     ← Embedding creation UI
│           ├── processing/
│           │   ├── QueryPreprocessing.js  ← Query normalization UI
│           │   ├── SummarizationDedup.js  ← Summarize & deduplicate UI
│           │   └── PromptSchemaManager.js ← RAG pipeline + prompt editor
│           ├── search/
│           │   ├── QuerySearch.js         ← Vector search UI
│           │   ├── BM25Search.js          ← BM25 keyword search UI
│           │   ├── HybridSearch.js        ← Hybrid search UI
│           │   └── RerankingSearch.js     ← Score fusion + rerank UI
│           └── settings/
│               └── Settings.js            ← .env editor UI
├── src/
│   ├── scripts/
│   │   ├── utilities/
│   │   │   ├── mistralEmbedding.js        ← Mistral API client
│   │   │   └── groqClient.js              ← Groq API client
│   │   ├── embeddings/
│   │   │   ├── create-embeddings-batch-mistral.js
│   │   │   └── create-userstories-embeddings-batch-mistral.js
│   │   └── query-preprocessing/
│   │       ├── queryPreprocessor.js
│   │       ├── abbreviationMapper.js
│   │       ├── synonymExpander.js
│   │       └── normalizer.js
│   ├── config/
│   │   ├── testcases-vector-index.json    ← Atlas vector index definition
│   │   ├── testcases-bm25-index.json      ← Atlas BM25 index definition
│   │   ├── user-stories-vector-index.json
│   │   └── user-stories-bm25-index.json
│   └── data/                              ← Converted JSON files (runtime)
├── uploads/                               ← Temporary Excel uploads
├── .env.example
└── package.json
```

---

## Backend — API Reference

All routes are served from `server/index.js` on **port 3001**.

### System

| Method | Endpoint | Description | Key Lines |
|---|---|---|---|
| GET | `/api/health` | Server health check | `server/index.js:L127` |
| GET | `/api/env` | Read `.env` variables | `server/index.js:L600` |
| POST | `/api/env` | Update `.env` variables | `server/index.js:L618` |

### Job Tracking

| Method | Endpoint | Description | Key Lines |
|---|---|---|---|
| GET | `/api/jobs/active` | List all in-progress jobs | `server/index.js:L133` |
| GET | `/api/jobs/:jobId` | Get status of a specific job | `server/index.js:L139` |

Jobs are tracked in-memory via a `Map`. Each job has: `id`, `status`, `progress`, `results`, `currentFile`.

### Data Ingestion

| Method | Endpoint | Description | Key Lines |
|---|---|---|---|
| GET | `/api/files` | List JSON files in `src/data/` | `server/index.js:L155` |
| POST | `/api/upload-excel` | Upload Excel → convert to JSON | `server/index.js:L172` |
| GET | `/api/metadata/distinct` | Get distinct filter values (module, priority, risk, type) | `server/index.js:L145` |

**`/api/upload-excel` request body:**
```json
{
  "file": "<multipart/form-data>",
  "dataType": "testcases | userstories",
  "sheetName": "Testcases"
}
```
Dynamically generates and executes a Node.js conversion script. Supports both test case and user story Excel formats with flexible column mapping.

### Embeddings

| Method | Endpoint | Description | Key Lines |
|---|---|---|---|
| POST | `/api/create-embeddings` | Create embeddings one-by-one (sequential) | `server/index.js:L370` |
| POST | `/api/create-embeddings-batch` | Create embeddings using batch script (faster) | `server/index.js:L415` |

Both endpoints return a `jobId` immediately and process in the background. Poll `/api/jobs/:jobId` for status.

**Request body:**
```json
{
  "files": ["converted-123.json"],
  "scriptName": "create-embeddings-batch-mistral.js",
  "jobType": "testcases"
}
```

### Search

| Method | Endpoint | Description | Key Lines |
|---|---|---|---|
| POST | `/api/search` | Pure vector search (Mistral embeddings) | `server/index.js:L700` |
| POST | `/api/search/bm25` | BM25 full-text search (Atlas Search) | `server/index.js:L810` |
| POST | `/api/search/hybrid` | Hybrid: BM25 + Vector with weighted score fusion | `server/index.js:L900` |
| POST | `/api/search/rerank` | Hybrid search + Groq LLM reranking | `server/index.js:L1060` |

**Common request body:**
```json
{
  "query": "patient registration OTP validation",
  "limit": 10,
  "filters": { "module": "Patient Registration", "priority": "High" }
}
```

**Hybrid-specific:**
```json
{
  "bm25Weight": 0.4,
  "vectorWeight": 0.6,
  "bm25Fields": ["id", "title", "description", "steps", "expectedResults", "module"]
}
```

### Query Processing

| Method | Endpoint | Description | Key Lines |
|---|---|---|---|
| POST | `/api/search/preprocess` | Normalize + expand abbreviations + synonyms | `server/index.js:L635` |
| POST | `/api/search/analyze` | Analyze query without applying transformations | `server/index.js:L665` |

### Result Processing

| Method | Endpoint | Description | Key Lines |
|---|---|---|---|
| POST | `/api/search/deduplicate` | Remove near-duplicate results (Jaccard similarity) | `server/index.js:L685` |
| POST | `/api/search/summarize` | AI summarization of search results via Groq | `server/index.js:L720` |

### Test Case Generation

| Method | Endpoint | Description | Key Lines |
|---|---|---|---|
| POST | `/api/test-prompt` | Send a raw prompt to Groq LLM and get JSON response | `server/index.js:L800` |
| GET | `/api/testcases/latest-id` | Get the highest TC_XXXX ID from MongoDB | `server/index.js:L1100` |

---

## Frontend — UI Modules

All modules are accessible from the collapsible sidebar in `App.js`.

| Sidebar Label | Component | What It Does |
|---|---|---|
| Convert to JSON | `ConvertToJson.js` | Upload `.xlsx` → convert to JSON via `/api/upload-excel` |
| Embeddings & Store | `EmbeddingsStore.js` | Select JSON files → trigger embedding creation jobs |
| Query Preprocessing | `QueryPreprocessing.js` | Test query normalization, abbreviation & synonym expansion |
| Vector Search | `QuerySearch.js` | Semantic search using Mistral embeddings |
| BM25 Search | `BM25Search.js` | Keyword-based Atlas Search with fuzzy matching |
| Hybrid Search | `HybridSearch.js` | Combined BM25 + Vector with configurable weights |
| Score Fusion | `RerankingSearch.js` | Hybrid search + Groq LLM reranking |
| Summarize & Dedup | `SummarizationDedup.js` | Paste results → deduplicate → AI summarize |
| Prompt & Schema | `PromptSchemaManager.js` | Edit prompt template, JSON schema, run full RAG pipeline |
| Settings | `Settings.js` | Edit `.env` variables from the UI |

### Theme
- Enterprise deep blue (`#0D47A1`) + amber (`#FF6F00`) palette
- Light/dark mode toggle persisted in `localStorage`
- Responsive collapsible sidebar (280px expanded / 72px collapsed)
- Defined in `App.js:L57–L130`

---

## AI Prompts & LLM Usage

### 1. Reranking Prompt — `src/scripts/utilities/groqClient.js:L47`

**Model:** `llama-3.2-3b-preview` (fast, cost-effective)

```
You are a relevance scoring assistant. Score each document's relevance
to the query on a scale of 0-100.

Query: "{query}"
Documents: [0] ..., [1] ...

Return ONLY a valid JSON object:
{"rankings": [{"index": 0, "score": 95}, {"index": 1, "score": 87}]}

Sort by score highest first. Include only top {topK} results.
```

System message enforces: `"Return only valid JSON, no markdown or extra text."`

---

### 2. Summarization Prompt — `src/scripts/utilities/groqClient.js:L120`

**Model:** `llama-3.3-70b-versatile` (high quality)

**Concise style:**
```
You are a helpful assistant that summarizes search results.
Query: "{query}"
Search Results: {docTexts}
Task: Provide a concise overview of the search results.
Keep the summary under {maxLength} words.
```

**Detailed style (healthcare-specific)** — `server/index.js:L730`:
```
You are a senior QA expert specializing in healthcare systems with 10+ years of experience.
Provide:
1. FUNCTIONAL COVERAGE ANALYSIS
2. PRIORITY & RISK ASSESSMENT
3. TEST SCENARIO DEPTH
4. EDGE CASES & NEGATIVE SCENARIOS
5. AUTOMATION READINESS
6. CRITICAL GAPS
7. HEALTHCARE COMPLIANCE
8. INTEGRATION POINTS
```

---

### 3. RAG Test Case Generation Prompt — `client/src/components/processing/PromptSchemaManager.js:L88`

**Framework:** ICEPOT (Instruction, Context, Examples, Persona, Output, Tone)

**Model:** `llama-3.2-3b-preview` via `/api/test-prompt`

```
# HEALTHCARE TEST CASE GENERATION

## INSTRUCTION
Generate 6 high-quality test cases for the user story using retrieved
test case context. Each must:
- Include 5-8 detailed, numbered test steps
- Define measurable expected results
- Cover positive, negative, and edge cases

## CONTEXT
MongoDB database with 6,000+ healthcare test cases covering Patient
Registration, Laboratory, Ward Management, Billing, Prescription, Diagnostics.
Healthcare entities: UHID, PRN, ERN, OTP.

## PERSONA
Senior QA Engineer with healthcare systems expertise (HIPAA, HMS, PHI/PII).

## OUTPUT FORMAT
Valid JSON: { analysis, newTestCases[], rationale[], recommendations }
```

At runtime, the prompt is augmented with:
- The user story text
- RAG summary of top 5 retrieved test cases
- Top 3–5 reference test cases (essential fields only)
- Next sequential test case ID from the database

---

### 4. Answer Generation Prompt — `src/scripts/utilities/groqClient.js:L175`

```
Answer the following question based ONLY on the provided context.
Context: {context}
Question: {query}
Instructions:
1. Provide a direct, accurate answer based on the context
2. Cite source numbers [1], [2] when referencing specific information
3. Be concise but complete
4. If uncertain, acknowledge it
```

---

## 10-Step RAG Pipeline

Triggered by the "Complete RAG Pipeline" button in `PromptSchemaManager.js:L310`.

```
Step 1  → User Story Input (validation)
Step 2  → Query Preprocessing (normalize → abbreviations → synonyms)
            POST /api/search/preprocess
Step 3  → Hybrid Search (BM25 0.4 + Vector 0.6, limit 50)
            POST /api/search/hybrid
Step 4  → RRF Re-Ranking (Groq LLM, top 10 selected)
            POST /api/search/rerank
Step 5  → Deduplication (Jaccard similarity > 0.95)
            POST /api/search/deduplicate
Step 6  → RAG Summarization (Groq llama-3.3-70b, detailed style)
            POST /api/search/summarize
Step 7  → Prompt Assembly (ICEPOT template + RAG summary + reference TCs)
            GET  /api/testcases/latest-id
Step 8  → LLM Generation (Groq llama-3.2-3b, temp=0.5, max 10000 tokens)
            POST /api/test-prompt
Step 9  → JSON Validation (structure check, field validation, step count ≥ 5)
Step 10 → HTML Rendering (table view with export to CSV)
```

---

## Environment Setup

Copy `.env.example` to `.env` and fill in your values:

```env
# MongoDB Atlas
MONGODB_URI="mongodb+srv://<user>:<password>@<cluster>.mongodb.net/"
DB_NAME="db_stories_tests"
COLLECTION_NAME="test_cases"
VECTOR_INDEX_NAME="vector_index_test_cases"
BM25_INDEX_NAME="bm25_search"

# User Stories Collection
USER_STORIES_COLLECTION_NAME="user_stories"
USER_STORIES_VECTOR_INDEX_NAME="vector_index_user_story"

# Mistral AI (embeddings)
MISTRAL_API_KEY="<your_mistral_api_key>"
MISTRAL_EMBEDDING_MODEL="mistral-embed"

# Groq AI (reranking + summarization + generation)
GROQ_API_KEY="<your_groq_api_key>"
GROQ_RERANK_MODEL="llama-3.2-3b-preview"
GROQ_SUMMARIZATION_MODEL="llama-3.3-70b-versatile"
```

Get API keys:
- Mistral AI: https://console.mistral.ai
- Groq AI: https://console.groq.com (free tier available)

---

## Running the Application

```bash
# Install all dependencies (root + client)
npm install

# Start both server and client concurrently
npm run dev

# Server only (port 3001)
npm run server

# Client only (port 3000)
npm run client
```

---

## MongoDB Atlas Index Configuration

Create these indexes in MongoDB Atlas before running searches.

### Vector Index — `src/config/testcases-vector-index.json`

```json
{
  "fields": [{
    "type": "vector",
    "path": "embedding",
    "numDimensions": 1024,
    "similarity": "cosine"
  }]
}
```

### BM25 / Atlas Search Index — `src/config/testcases-bm25-index.json`

```json
{
  "mappings": {
    "fields": {
      "title": [{ "type": "string" }],
      "description": [{ "type": "string" }],
      "steps": [{ "type": "string" }],
      "expectedResults": [{ "type": "string" }],
      "module": [{ "type": "string" }]
    }
  }
}
```

---

## Key Impacted Code Locations

| Feature | File | Lines |
|---|---|---|
| All API routes | `server/index.js` | L127–L1130 |
| Job tracking (create/update/get) | `server/index.js` | L47–L80 |
| DB/collection/index validation | `server/index.js` | L83–L125 |
| Excel → JSON conversion (test cases) | `server/index.js` | L172–L260 |
| Excel → JSON conversion (user stories) | `server/index.js` | L175–L250 |
| Background embedding processing | `server/index.js` | L480–L590 |
| Batch embedding processing | `server/index.js` | L430–L480 |
| Vector search pipeline | `server/index.js` | L700–L810 |
| BM25 search pipeline | `server/index.js` | L810–L900 |
| Hybrid search + score fusion | `server/index.js` | L900–L1060 |
| Groq-only reranking handler | `server/index.js` | L630–L700 |
| Deduplication (Jaccard similarity) | `server/index.js` | L685–L720 |
| Summarization (detailed healthcare prompt) | `server/index.js` | L720–L800 |
| Raw prompt → Groq LLM | `server/index.js` | L800–L870 |
| Mistral embedding (single) | `src/scripts/utilities/mistralEmbedding.js` | L35–L80 |
| Mistral embedding (batch) | `src/scripts/utilities/mistralEmbedding.js` | L90–L140 |
| Groq reranking prompt | `src/scripts/utilities/groqClient.js` | L47–L130 |
| Groq summarization prompt | `src/scripts/utilities/groqClient.js` | L120–L175 |
| Groq answer generation prompt | `src/scripts/utilities/groqClient.js` | L175–L240 |
| App sidebar + theme | `client/src/App.js` | L57–L200 |
| ICEPOT prompt template | `client/src/components/processing/PromptSchemaManager.js` | L88–L145 |
| 10-step RAG pipeline | `client/src/components/processing/PromptSchemaManager.js` | L310–L600 |
| JSON schema definition | `client/src/components/processing/PromptSchemaManager.js` | L30–L87 |
| DeepEval metrics evaluation | `client/src/components/processing/PromptSchemaManager.js` | L620–L680 |
| Test case table renderer | `client/src/components/processing/PromptSchemaManager.js` | L685–L780 |
| CSV export | `client/src/components/processing/PromptSchemaManager.js` | L785–L810 |

---

## Notes

- **DNS fix for macOS:** `server/index.js:L21` sets Google DNS (`8.8.8.8`) to resolve MongoDB Atlas connection issues on macOS.
- **In-memory job store:** Jobs are stored in a `Map` and cleaned up every 10 minutes for jobs older than 1 hour. For production, replace with Redis (`server/index.js:L47`).
- **Embedding cost:** Mistral `mistral-embed` is priced at $0.10 per 1M tokens. Cost is tracked per document and per job.
- **Groq cost:** Currently free tier. Cost tracking is included but set to $0 (`server/index.js:L780`).
- **DeepEval integration:** Requires a separate Python service running on `http://localhost:8000/eval`. Used only in the Metrics Evaluation tab.
