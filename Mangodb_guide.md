# RAG MongoDB Demo - Complete Guide

Welcome! This is a **Retrieval-Augmented Generation (RAG) system** that helps you search for test cases and user stories using both **AI embeddings** and **smart search technologies**. Don't worry if you're new to programming—this guide will explain everything step by step! 🚀

---

## 📚 Table of Contents

1. [What is This Project?](#what-is-this-project)
2. [Key Features](#key-features)
3. [Before You Start](#before-you-start)
4. [Installation & Setup](#installation--setup)
5. [How to Use the Application](#how-to-use-the-application)
6. [Project Structure](#project-structure)
7. [Understanding Key Concepts](#understanding-key-concepts)
8. [Troubleshooting](#troubleshooting)
9. [API Reference](#api-reference)

---

## 🎯 What is This Project?

Imagine you have thousands of test cases in your organization. Now imagine being able to **search through them intelligently using natural language** (like asking a question in plain English) rather than using keywords or filters.

This project does exactly that! It:
- **Stores** test cases and user stories in MongoDB Atlas
- **Converts text into AI embeddings** (mathematical representations that capture meaning)
- **Searches** using multiple intelligent methods (keyword search, semantic search, hybrid search)
- **Ranks** results by relevance
- **Summarizes** results in plain English

### Real-World Example:
> You ask: "How do I test user login with invalid credentials?"
> 
> The system finds ALL relevant test cases, even if they use different wording like "authentication failure" or "incorrect password handling"

---

## ✨ Key Features

### 1. **Data Management**
- Upload test cases from Excel files
- Convert data to JSON format automatically
- Upload user stories directly
- View all uploaded data

### 2. **AI Embeddings** 
- Generate embeddings using **Mistral AI** (cheap and fast!)
- Batch process thousands of test cases
- Track processing progress and costs
- Support for both regular and user story embeddings

### 3. **Multiple Search Methods**
- **BM25 Search**: Traditional keyword-based search (fast and reliable)
- **Vector Search**: AI-powered semantic search (understands meaning)
- **Hybrid Search**: Combines both methods for best results
- **Reranking**: Uses advanced AI to sort results by quality

### 4. **Query Processing**
- **Query Preprocessing**: Normalizes your search queries using:
  - Abbreviation expansion (e.g., "pwd" → "password")
  - Synonym expansion (e.g., "bug" → "defect", "issue", "error")
  - Text normalization
- **Summarization**: AI summarizes search results
- **Deduplication**: Removes duplicate results

### 5. **Settings Management**
- Configure database and collection names
- Manage index names
- Customize search behavior

---

## 🛠️ Before You Start

### Prerequisites (What You Need Installed)

1. **Node.js** (v16 or higher)
   - Download from: https://nodejs.org/
   - This is the runtime that runs JavaScript code
   
2. **MongoDB Atlas Account** (FREE!)
   - Sign up at: https://www.mongodb.com/cloud/atlas
   - Create a free cluster (M0 tier is free forever)
   - This is where your data will be stored in the cloud

3. **API Keys** (FREE!)
   - **Mistral AI**: https://console.mistral.ai/ (for embeddings)
   - **Groq AI**: https://console.groq.com/ (for reranking/summarization)

4. **Code Editor**
   - VS Code recommended: https://code.visualstudio.com/

---

## 📦 Installation & Setup

### Step 1: Clone or Download the Project

```bash
# Navigate to your projects folder
cd path/to/your/project
```

### Step 2: Install Dependencies

Install all required packages:

```bash
# Install main dependencies
npm install

# Install client dependencies (React)
cd client
npm install
cd ..
```

This downloads all the libraries the project needs from the internet.

### Step 3: Create Your `.env` File

The `.env` file stores **sensitive information** like passwords and API keys. **Never share this file!**

Create a file named `.env` in the root directory with this content:

```env
# MongoDB Connection String
MONGODB_URI="mongodb+srv://USERNAME:PASSWORD@cluster.mongodb.net/?appName=Cluster0"

# Database Configuration
DB_NAME="db_stories_tests"
COLLECTION_NAME="test_cases"
VECTOR_INDEX_NAME="vector_index"
BM25_INDEX_NAME="bm25_search"

# User Stories Configuration
USER_STORIES_COLLECTION_NAME="user_stories"
USER_STORIES_VECTOR_INDEX_NAME="vector_index_user_story"

# Mistral AI Configuration (for embeddings)
MISTRAL_API_KEY="your-mistral-api-key-here"
MISTRAL_EMBEDDING_MODEL="mistral-embed"

# Groq AI Configuration (for reranking/summarization)
GROQ_API_KEY="your-groq-api-key-here"
GROQ_RERANK_MODEL="openai/gpt-oss-120b"
GROQ_SUMMARIZATION_MODEL="openai/gpt-oss-120b"
```

### Step 4: Get Your MongoDB Connection String

1. Go to [MongoDB Atlas](https://cloud.mongodb.com/)
2. Log in to your account
3. Click "Connect" on your cluster
4. Choose "Drivers" → "Node.js"
5. Copy the connection string
6. Replace `USERNAME`, `PASSWORD`, and cluster info in your `.env` file

### Step 5: Get Your API Keys

**For Mistral AI:**
1. Go to https://console.mistral.ai/
2. Create an account
3. Go to "API Keys"
4. Copy your API key to `MISTRAL_API_KEY` in `.env`

**For Groq AI:**
1. Go to https://console.groq.com/
2. Create an account
3. Go to "API Keys"
4. Copy your API key to `GROQ_API_KEY` in `.env`

### Step 6: Create MongoDB Indexes

Before creating embeddings, MongoDB needs to know how to organize search indexes. This is done through the MongoDB Atlas UI:

1. Go to MongoDB Atlas
2. Click on your cluster
3. Go to "Search" (or "Atlas Search")
4. Create the vector index as described in the online docs

---

## 🚀 How to Use the Application

### Starting the Application

Open your terminal and run:

```bash
npm run dev
```

This starts **both** the backend server and frontend app:
- **Backend** (server): http://localhost:3001
- **Frontend** (UI): http://localhost:3000

### What Each Section Does

#### 1. **Data Management** 📁

**Convert Excel to JSON:**
- Upload an Excel file containing test cases
- The app automatically converts it to JSON format
- Download the converted file

**Upload Data:**
- Upload Excel or JSON files of test cases
- Upload user stories separately

#### 2. **Create Embeddings** 🤖

**What are embeddings?**
Embeddings are like fingerprints of text—they capture the **meaning** of text in a format that AI can work with.

**Process:**
1. Go to "Create Embeddings"
2. Select your data files
3. Choose "Batch Processing" (it's faster!)
4. Click "Create Embeddings"
5. Track progress with the progress bar
6. Your data is now stored with embeddings in MongoDB!

#### 3. **Search Your Data** 🔍

**Query Search (Vector):**
- Type your question in natural language
- Get results sorted by relevance
- Click on results to see full details

**BM25 Search (Keyword):**
- Traditional search method
- Good for exact matches and keywords

**Hybrid Search:**
- Combines keyword + semantic search
- Best of both worlds!

**Reranking:**
- Reranks results using advanced AI
- Gets the most relevant results to the top

#### 4. **Process Queries** ⚙️

**Query Preprocessing:**
- Cleans up your search query
- Expands abbreviations (e.g., pwd → password)
- Expands synonyms (e.g., bug → issue)

**Summarization:**
- Summarizes search results
- Removes duplicate results

#### 5. **Settings** ⚙️

- Configure which database to use
- Configure which collections to search
- View health status of your connection

---

## 🏗️ Technical Architecture

### System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                      FRONTEND (React)                            │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐            │
│  │  Upload  │ │ Embeddings│ │  Search  │ │ Settings │            │
│  │   Data   │ │   Manager│ │ Interface│ │   Panel  │            │
│  └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘            │
└───────┼─────────────┼─────────────┼─────────────┼──────────────────┘
        │ HTTP        │ HTTP        │ HTTP        │ HTTP
        └─────────────┴─────────────┴─────────────┴─────────────────┐
                                                                      │
┌─────────────────────────────────────────────────────────────────┐  │
│              BACKEND (Express.js Server)                        │  │
│  ┌──────────────────────────────────────────────────────────┐  │  │
│  │  Routes Layer                                             │  │  │
│  │  ├─ Data Management Routes                                │  │  │
│  │  ├─ Embeddings Creation Routes                            │  │  │
│  │  ├─ Search Routes (Vector, BM25, Hybrid, Rerank)         │  │  │
│  │  └─ Query Processing Routes                               │  │  │
│  └───────────────────────────┬────────────────────────────────┘  │  │
│                              │                                    │  │
│  ┌──────────────────────────┴────────────────────────────────┐   │  │
│  │  Business Logic Layer                                     │   │  │
│  │  │                                                         │   │  │
│  │  ├─ SQL Server Operations                                │   │  │
│  │  ├─ Excel to JSON Conversion (XLSX parser)               │   │  │
│  │  ├─ Query Preprocessing Pipeline                         │   │  │
│  │  │ ├─ Normalization                                      │   │  │
│  │  │ ├─ Abbreviation Expansion                             │   │  │
│  │  │ └─ Synonym Expansion                                  │   │  │
│  │  │                                                         │   │  │
│  │  └─ Job Management (Track async operations)              │   │  │
│  └───────────────────────────┬────────────────────────────────┘   │  │
│                              │                                    │  │
│  ┌──────────────────────────┴────────────────────────────────┐   │  │
│  │  Utility Layer                                            │   │  │
│  │  ├─ Mistral Embedding Generator                          │   │  │
│  │  ├─ Groq AI Client (Reranking & Summarization)           │   │  │
│  │  ├─ MongoDB Connection Manager                           │   │  │
│  │  └─ Error Handling & Logging                             │   │  │
│  └────────────┬─────────────────┬──────────────────┬────────┘    │  │
└───────────────┼─────────────────┼──────────────────┼──────────────┘  │
                │                 │                  │                 │
  ┌─────────────┴──────┐  ┌────────────────┐  ┌──────────────────┐   │
  │                    │  │                │  │                  │   │
  ▼                    ▼  ▼                ▼  ▼                  ▼   │
┌──────────┐  ┌──────────────────┐  ┌──────────────────────────────┐ │
│ Mistral  │  │     Groq AI      │  │   MongoDB Atlas              │ │
│   API    │  │  Rerank & Sum    │  │  ┌────────────────────────┐  │ │
│ Embed    │  │  via groq-sdk    │  │  │ Vector Index (Atlas)   │  │ │
│ 1024 dim │  │  High-Speed LLM  │  │  │ BM25 Index (Atlas)     │  │ │
│ $0.11/1M │  │  Inference       │  │  │ Documents Collections  │  │ │
│ Tokens   │  │                  │  │  │ Automatic Sharding     │  │ │
└──────────┘  └──────────────────┘  └──────────────────────────────┘ │
                                                                       │
└───────────────────────────────────────────────────────────────────────┘
```

### Data Flow Examples

**Embedding Creation Flow:**
```
1. User uploads Excel file
   └─→ Server receives via multer
   
2. Excel → JSON conversion (xlsx parser)
   └─→ Stored temporarily in uploads/
   
3. Read JSON file
   └─→ Split into batches of 50 items
   
4. For each batch:
   └─→ Send to Mistral AI API
   └─→ Receive 1024-dimensional embeddings
   └─→ Combined with original data
   
5. Batch insertion to MongoDB
   └─→ 100 documents at a time
   
6. MongoDB indexes updated
   └─→ Vector index for semantic search
   └─→ BM25 index for keyword search
```

**Search Flow:**
```
1. User types search query in UI
   └─→ Sent to backend via HTTP POST
   
2. Query Preprocessing
   └─→ Normalize (lowercase, trim, etc.)
   └─→ Expand abbreviations (pwd → password)
   └─→ Expand synonyms (issue → problem, error, bug)
   
3. Execute search based on type:
   
   Vector Search:
   └─→ Convert query to embedding (Mistral)
   └─→ MongoDB calculates cosine similarity
   └─→ Returns top 10 by vector score
   
   BM25 Search:
   └─→ MongoDB text search on fields
   └─→ Applies field weights
   └─→ Returns top 10 by BM25 score
   
   Hybrid Search:
   └─→ Run both searches in parallel
   └─→ Merge results (deduplicate)
   └─→ Calculate weighted scores
   
4. Optional: Rerank results
   └─→ Send top results to Groq AI
   └─→ AI re-scores by relevance
   └─→ Return reordered results
   
5. Return to frontend
   └─→ Display results to user
```

---

## 📦 Technology Stack & Dependencies

### Frontend Stack

| Package | Version | Purpose |
|---------|---------|---------|
| `react` | 19.1.1 | UI framework - creates interactive components |
| `react-router-dom` | 7.9.3 | Routing - navigates between pages without reload |
| `@mui/material` | 7.3.2 | UI components - professional Material Design |
| `@mui/icons-material` | 7.3.2 | Icons - professional icons for UI |
| `@mui/x-data-grid` | 8.13.1 | Data table - displays search results |
| `axios` | 1.12.2 | HTTP client - makes API calls to backend |
| `notistack` | 3.0.2 | Notifications - shows success/error messages |
| `react-scripts` | 5.0.1 | Build tools - compiles React to JavaScript |

**MUI (Material-UI) Stack Breakdown:**
- `@emotion/react` & `@emotion/styled` - CSS-in-JS styling
- `@mui/system` - Base styling system
- `@mui/lab` - Additional experimental components

### Backend Stack

| Package | Version | Purpose |
|---------|---------|---------|
| `express` | 4.18.2 | Web framework - creates API endpoints |
| `mongodb` | 6.8.0 | Database driver - connects to MongoDB |
| `@mistralai/mistralai` | 0.4.0 | Mistral AI SDK - embedding generation |
| `groq-sdk` | 0.5.0 | Groq SDK - AI reranking & summarization |
| `axios` | 1.12.2 | HTTP client - calls external APIs |
| `dotenv` | 16.4.5 | Environment variables - loads .env safely |
| `cors` | 2.8.5 | CORS middleware - allows cross-origin requests |
| `multer` | 1.4.5-lts.1 | File upload - handles file uploads |
| `xlsx` | 0.18.5 | Excel parser - converts .xlsx to JSON |
| `p-limit` | 7.1.1 | Concurrency control - limits parallel requests |

**Additional Backend Packages:**
- `mysql2` - MySQL database support
- `sequelize` - ORM for database queries
- `@huggingface/inference` - Alternative embeddings (HuggingFace models)
- `openai` - Alternative to Mistral for embeddings

### Node.js Concepts Used

| Concept | Used For |
|---------|----------|
| `async/await` | Non-blocking operations (API calls, database queries) |
| `Promise.allSettled()` | Run multiple embeddings in parallel |
| `dns.setServers()` | Override DNS for network reliability |
| `child_process.spawn()` | Run embedding scripts as child processes |
| `fs` module | File system operations (read/write files) |
| `path` module | Cross-platform path handling |
| `dotenv` | Secure environment variable loading |

---

## 📁 Project Structure

Understanding the folder structure will help you navigate the code:

```
rag-mongo-demo-v8/
│
├── 📄 README.md                          ← You are here!
├── 📄 package.json                       ← Project dependencies
├── 📄 .env                              ← Your API keys (create this!)
│
├── 📁 server/
│   └── index.js                         ← Main backend server (all API endpoints)
│
├── 📁 client/                           ← React frontend application
│   ├── package.json
│   ├── public/
│   │   └── index.html                   ← Main HTML page
│   └── src/
│       ├── App.js                       ← Main app component
│       ├── App.css                      ← Styling
│       ├── index.js                     ← Entry point
│       └── components/
│           ├── data/                    ← Data upload & conversion
│           ├── search/                  ← Search components (BM25, Vector, Hybrid)
│           ├── processing/              ← Query preprocessing, summarization
│           └── settings/                ← Settings page
│
├── 📁 src/
│   ├── config/                          ← Search index configurations (JSON)
│   │   ├── testcases-bm25-index.json
│   │   └── testcases-vector-index.json
│   ├── data/                            ← Sample data files
│   │   ├── converted-*.json             ← Converted test cases
│   │   └── stories-*.json               ← User stories
│   └── scripts/
│       ├── data-conversion/             ← Convert Excel to JSON
│       ├── embeddings/                  ← Generate embeddings using Mistral AI
│       ├── query-preprocessing/         ← Preprocess search queries
│       ├── search/                      ← All search implementations
│       └── utilities/                   ← Helper functions (API calls, etc.)
│
└── 📁 uploads/                          ← Temporary folder for uploaded files
```

### Key Files Explained

| File | Purpose |
|------|---------|
| `server/index.js` | The heart of the backend—contains all API endpoints your React app calls |
| `client/src/App.js` | Main React component that shows the sidebar and different pages |
| `src/scripts/embeddings/create-embeddings-batch-mistral.js` | Converts test cases into embeddings using Mistral AI |
| `src/scripts/search/*.js` | Implements different search algorithms (BM25, Vector, etc.) |
| `src/scripts/query-preprocessing/queryPreprocessor.js` | Cleans and enhances search queries |

---

## 🧠 Understanding Key Concepts

### 1. **What are Embeddings?**

An embedding is a way to represent text as numbers that AI can understand.

**Simple example:**
```
Text: "The cat sat on the mat"
Embedding: [0.1, -0.5, 0.8, 0.3, ... thousand more numbers ...]
```

**What's cool?** Similar texts have similar embeddings!
```
"cat on mat"          → [0.1, -0.5, 0.8, 0.3, ...]  ← Similar
"The sleeping cat"    → [0.1, -0.4, 0.85, 0.2, ...] ← Similar
"The car engine"      → [0.9, 0.2, -0.5, 0.8, ...]  ← Different
```

This is why semantic search works!

### 2. **Search Methods Explained**

**BM25 (Keyword Search):**
- Looks for exact words you typed
- Fast and reliable
- Good if you know specific keywords
```
Search: "login button" → Finds documents with "login" AND "button"
```

**Vector Search (Semantic Search):**
- Uses embeddings to find meaning
- Understands synonyms and context
- Slower but smarter
```
Search: "How to sign in?" → Finds "user authentication", "login", "credentials"
```

**Hybrid Search:**
- Combines both methods
- Takes top 50 keyword results + top 50 semantic results
- Merges them intelligently
- Usually the best option!

**Reranking:**
- Takes your search results
- Uses advanced AI to sort by relevance
- Makes sure the best results are first

### 3. **Batch Processing**

Why does the project use "batches"?

When you have 10,000 test cases:
- Sending 1 by 1 to the API = Very slow (10,000 requests)
- Sending in batches of 50 = Much faster (200 requests)

The project uses batch processing to:
- Be efficient (fewer API calls)
- Be cheap (fewer API calls = less cost)
- Show progress (you know how many are done)

### 4. **Query Preprocessing**

Before searching, the system "cleans up" your query:

```
Your input:          "pwd reset issue"
Abbreviation expand: "password reset issue" (pwd → password)
Synonym expand:      "password reset issue/problem/error/defect"
Normalized:          "password reset issue / password reset problem / ..."
```

This makes searches much better!

---

## 🐛 Troubleshooting

### Error: "Failed to validate database/collection"

**Cause:** MongoDB Atlas database or collection doesn't exist yet

**Solutions:**
1. Create them manually in MongoDB Atlas
2. Or upload data first—it will create them automatically

```bash
# Check MongoDB connection
nslookup cluster0.8fx1c9a.mongodb.net
```

### Error: "Cannot find module"

**Cause:** Dependencies not installed

**Solution:**
```bash
npm install
```

### Error: "API Key Invalid"

**Cause:** Wrong API key in `.env` file

**Solutions:**
1. Double-check your API key hasn't been copied with extra spaces
2. Verify you're using the right API key (not the wrong service)
3. Test the API key directly on the API's console

### Application won't start

**Check:**
1. Node.js installed? `node --version`
2. Dependencies installed? Run `npm install` again
3. `.env` file exists in root directory?
4. Ports 3000 and 3001 are not in use

---

## 🔌 Complete API Reference

### External APIs Used

This project integrates with several third-party AI services:

#### 1. **Mistral AI API** - For Embeddings
- **Base URL:** `https://api.mistral.ai/v1/embeddings`
- **Model:** `mistral-embed`
- **Dimensions:** 1024 (each text becomes a 1024-length array of numbers)
- **Purpose:** Converts text into mathematical vectors that capture meaning
- **Pricing:** ~$0.11 per 1M tokens (VERY cheap!)
- **File Location:** `src/scripts/utilities/mistralEmbedding.js`

**How it works:**
```javascript
"Generate embeddings for test cases" 
    ↓
Sent to Mistral API 
    ↓
Returns array of 1024 numbers per text
    ↓
Stored in MongoDB with documents
```

#### 2. **Groq AI API** - For Reranking & Summarization
- **Base URL:** `https://api.groq.com/`
- **Models Used:**
  - `openai/gpt-oss-120b` (Reranking) - Sorts search results by relevance
  - `openai/gpt-oss-120b` (Summarization) - Summarizes results in plain English
- **Purpose:** High-speed LLM inference for intelligent result processing
- **File Location:** `src/scripts/utilities/groqClient.js`

**How it works:**
```javascript
Search results
    ↓
Sent to Groq for reranking/summarization
    ↓
AI intelligently sorts by relevance
    ↓
Returns sorted/summarized results
```

#### 3. **MongoDB Atlas** - For Vector Search & Storage
- **Connection:** `mongodb+srv://username:password@cluster.mongodb.net`
- **Search Indexes:**
  - **Vector Index** (Atlas Search) - Semantic search on embeddings
  - **BM25 Index** (Atlas Search) - Full-text keyword search
- **Features:**
  - Automatic sharding for scale
  - Free tier with 512MB storage
  - Real-time indexing

---

### Backend API Endpoints

All endpoints run on `http://localhost:3001`

#### Health & Monitoring

**Check Server Health** ✅
```
GET /api/health
Response: { status: "ok", timestamp: "2024-01-15T10:30:00" }
```
Use this to verify your server is running.

**Get Active Jobs**
```
GET /api/jobs/active
Response: [{ jobId: "job123", status: "processing", progress: 45 }, ...]
```
Lists all currently running embedding/processing jobs.

**Get Specific Job Status**
```
GET /api/jobs/:jobId
Response: {
  id: "job123",
  status: "processing" | "completed" | "failed",
  progress: 45,  // percentage
  eta: 120000,   // milliseconds remaining
  created: "2024-01-15T10:00:00",
  started: "2024-01-15T10:05:00",
  completed: null
}
```
Track the progress of embedding creation or data upload jobs.

**Get Metadata Distinct Values**
```
GET /api/metadata/distinct?field=module
Response: { values: ["Authentication", "Dashboard", "Reports", ...] }
Available fields: id, module, title, description, steps, expectedResults, preRequisites, automationManual, priority, risk, type, linkedStories
```
Useful for populating filter dropdowns in the UI.

---

#### Data Management

**Upload Excel File**
```
POST /api/upload-excel
Content-Type: multipart/form-data
Body: { file: <.xlsx file> }

Response: {
  success: true,
  fileName: "uploaded-file.xlsx",
  totalRecords: 150,
  jobId: "job123"
}
```
Converts Excel test cases to JSON and returns a job ID to track progress.

**List Available Files**
```
GET /api/files
Response: {
  uploaded: ["file1.json", "file2.json"],
  data: ["converted-1234567890.json"],
  total: 5
}
```
Shows all available data files you can use for embedding creation.

---

#### Embeddings Creation

**Create Embeddings (Standard Process)**
```
POST /api/create-embeddings
Content-Type: application/json

Body: {
  files: ["converted-1234567890.json"],  // Required
  collectionName: "test_cases",           // Optional, uses env default
  jobType: "embeddings"                   // Optional
}

Response: {
  success: true,
  jobId: "job_abc123xyz",
  message: "Embedding creation started",
  filesCount: 1
}
```

**Create Embeddings (Fast Batch Processing)** ⚡
```
POST /api/create-embeddings-batch
Content-Type: application/json

Body: {
  files: ["converted-1234567890.json"],
  scriptName: "create-embeddings-batch-mistral.js",
  jobType: "embeddings"
}

Response: {
  success: true,
  jobId: "job_def456uvw",
  message: "Embedding creation started",
  filesCount: 1
}
```

**What happens internally when you create embeddings:**

```
1. Your JSON file is read
   │
2. File is split into batches (50 test cases per batch)
   │
3. Each batch is sent to Mistral AI for embedding
   │
4. Returned embeddings are mapped back to test cases
   │
5. Embeddings + metadata are inserted into MongoDB
   │
6. Indexes are updated automatically
   │
7. Results are ready for searching!
```

**Batch Processing Benefits:**
- ✅ 50 test cases per API call (vs 1 at a time)
- ✅ 3 concurrent API calls (parallel processing)
- ✅ 1 second delay between batches (rate limiting)
- ✅ Real-time progress tracking
- ✅ Automatic retry on failure
- ✅ Cost tracking and ETA calculation

---

#### Search Endpoints

**Vector/Semantic Search** 🧠
```
POST /api/search
Content-Type: application/json

Body: {
  query: "How do I test user authentication?",
  limit: 10,
  collectionName: "test_cases",
  threshold: 0.5
}

Response: [
  {
    _id: "ObjectId",
    id: "TC001",
    title: "Login with valid credentials",
    description: "Verify user can login with correct password",
    steps: [...],
    expectedResults: "User logged in successfully",
    embedding: [0.123, -0.456, 0.789, ...],  // 1024 numbers
    score: 0.87,  // Relevance score 0-1
    searchMethod: "vector"
  },
  ...
]
```

**What happens:**
1. Your query is converted to an embedding (same as test cases)
2. MongoDB finds closest vector matches (cosine similarity)
3. Results returned sorted by similarity score
4. Higher score = more relevant

**BM25 Keyword Search** 🔍
```
POST /api/search/bm25
Content-Type: application/json

Body: {
  query: "authentication login password",
  limit: 10,
  searchType: "simple",
  fieldWeights: {
    title: 5.0,
    description: 2.0,
    steps: 1.0
  }
}

Response: [
  {
    _id: "ObjectId",
    id: "TC001",
    title: "Login with valid credentials",
    module: "Authentication",
    bm25Score: 42.5,  // Raw BM25 score
    relevanceScore: 0.92,  // Normalized 0-1
    matchedFields: ["title", "description"],
    searchMethod: "bm25"
  },
  ...
]
```

**BM25 Algorithm Details:**
- Analyzes which FIELDS contain your keywords
- Field weights determine importance (title weighted more than steps)
- Fuzzy matching allows typos (maxEdits: 1)
- Prefix matching for partial words
- Normalized score between 0-1

**Hybrid Search** 🔄
```
POST /api/search/hybrid
Content-Type: application/json

Body: {
  query: "password reset error",
  limit: 10,
  vectorWeight: 0.5,  // Weight vector search 50%
  bm25Weight: 0.5     // Weight BM25 search 50%
}

Response: [
  {
    _id: "ObjectId",
    id: "TC001",
    title: "Reset password with invalid email",
    bm25Score: 0.85,
    vectorScore: 0.92,
    hybridScore: 0.885,  // Combined score
    searchMethods: ["bm25", "vector"],
    searchMethod: "hybrid"
  },
  ...
]
```

**How Hybrid Search Works:**
1. Run Vector Search → Get top 50 results
2. Run BM25 Search → Get top 50 results
3. Merge results (deduplicate)
4. Calculate weighted score: (vectorScore × 0.5) + (bm25Score × 0.5)
5. Re-sort by combined score
6. Return top 10

---

#### Query Processing Endpoints

**Preprocess Query** 🧹
```
POST /api/search/preprocess
Content-Type: application/json

Body: {
  query: "pwd reset issue on login",
  enableAbbreviations: true,
  enableSynonyms: true
}

Response: {
  original: "pwd reset issue on login",
  normalized: "password reset issue on login",
  abbreviationExpanded: "password reset issue on login",
  synonymExpanded: [
    "password reset issue on login",
    "password recovery issue on login",
    "password recovery problem on login",
    "password reset problem on authentication",
    "password recovery issue on authentication"
  ],
  metadata: {
    abbreviationsMapped: { "pwd": "password" },
    synonymsMapped: { "reset": ["recovery"], "issue": ["problem"] },
    processingTimeMs: 25
  }
}
```

**Preprocessing Pipeline:**
```
Raw Query: "pwd reset issue"
    ↓
Step 1: Normalize (lowercase, remove extra spaces)
    ↓
Step 2: Expand abbreviations (pwd → password)
    ↓
Step 3: Extract & preserve test case IDs
    ↓
Step 4: Expand synonyms (issue → problem/error/bug, reset → recovery)
    ↓
Final: "password reset issue / password recovery problem"
```

**Analyze Query** 📊
```
POST /api/search/analyze
Content-Type: application/json

Body: {
  query: "How do users authenticate?",
  includeTokens: true,
  includeSentiment: true
}

Response: {
  query: "How do users authenticate?",
  tokens: ["how", "do", "users", "authenticate"],
  keywords: ["users", "authenticate"],
  sentiment: "neutral",
  intent: "informational",
  suggestedFields: ["description", "steps", "expectedResults"]
}
```

**Deduplicate Results** 🎯
```
POST /api/search/deduplicate
Content-Type: application/json

Body: {
  results: [
    { id: "TC001", title: "Login test" },
    { id: "TC001", title: "Login test" },  // Duplicate!
    { id: "TC002", title: "Logout test" }
  ],
  threshold: 0.9  // 90% similarity = duplicate
}

Response: {
  deduped: [
    { id: "TC001", title: "Login test" },
    { id: "TC002", title: "Logout test" }
  ],
  removed: 1,
  duplicateGroups: [["TC001", "TC001"]]
}
```

**Summarize Results** 📝
```
POST /api/search/summarize
Content-Type: application/json

Body: {
  query: "authentication features",
  results: [
    { title: "Login test", description: "Test user login..." },
    { title: "Password reset test", description: "Test password reset..." }
  ],
  summaryLength: "medium"  // "short" | "medium" | "long"
}

Response: {
  summary: "Found 2 test cases for authentication: Login test validates user authentication with credentials, while Password reset test verifies the password recovery process.",
  keyPoints: [
    "User authentication via login",
    "Password recovery mechanism"
  ],
  relatedTopics: ["security", "user management"]
}
```

---

#### Reranking Endpoint

**Rerank Search Results** 🏆
```
POST /api/search/rerank
Content-Type: application/json

Body: {
  query: "test login functionality",
  results: [
    { id: "TC001", title: "Login test" },
    { id: "TC002", title: "User profile test" },
    { id: "TC003", title: "Authentication test" }
  ],
  topK: 2,  // Return top 2 results
  model: "groq"
}

Response: [
  {
    id: "TC001",
    title: "Login test",
    originalRank: 1,
    rerankScore: 0.95,
    reasoning: "Directly matches 'login' and 'authentication'"
  },
  {
    id: "TC003",
    title: "Authentication test",
    originalRank: 3,
    rerankScore: 0.88,
    reasoning: "Covers authentication which includes login"
  }
]
```

**How Reranking Works:**
1. Takes your search results (potentially unordered)
2. Sends them to Groq AI with your query
3. AI analyzes each result for relevance to query
4. Returns results reordered by AI's relevance judgment
5. Often finds better matches than basic scoring

---

#### Settings & Configuration

**Get Current Configuration**
```
GET /api/env
Response: {
  DB_NAME: "db_stories_tests",
  COLLECTION_NAME: "test_cases",
  VECTOR_INDEX_NAME: "vector_index",
  BM25_INDEX_NAME: "bm25_search",
  MONGODB_URI: "mongodb+srv://...",
  // API keys are NOT returned for security
}
```

**Update Configuration**
```
POST /api/env
Content-Type: application/json

Body: {
  DB_NAME: "new_db_name",
  COLLECTION_NAME: "new_collection"
}

Response: {
  success: true,
  updated: ["DB_NAME", "COLLECTION_NAME"],
  message: "Configuration updated"
}
```

**Get Latest Test Case ID**
```
GET /api/testcases/latest-id
Response: {
  lastId: "TC12456",
  totalCount: 12456,
  latestTimestamp: "2024-01-15T10:30:00"
}
```

---

### Frontend Components (React)

The React app communicates with these APIs through the components:

| Component | Purpose | Calls |
|-----------|---------|-------|
| `ConvertToJson` | Convert Excel → JSON | `POST /api/upload-excel` |
| `EmbeddingsStore` | Create embeddings | `POST /api/create-embeddings-batch` |
| `QuerySearch` | Vector search | `POST /api/search` |
| `BM25Search` | Keyword search | `POST /api/search/bm25` |
| `HybridSearch` | Combined search | `POST /api/search/hybrid` |
| `RerankingSearch` | AI reranking | `POST /api/search/rerank` |
| `QueryPreprocessing` | Process queries | `POST /api/search/preprocess` |
| `SummarizationDedup` | Summarize results | `POST /api/search/summarize` |
| `PromptSchemaManager` | Test prompts | `POST /api/test-prompt` |
| `Settings` | Configuration | `GET/POST /api/env` |

---

## 🎓 Learning Resources

Want to learn more about these technologies?

### About RAG (Retrieval-Augmented Generation):
- https://en.wikipedia.org/wiki/Prompt_engineering#Retrieval-augmented_generation

### About MongoDB:
- https://docs.mongodb.com/manual/
- MongoDB has a free tier forever!

### About AI Embeddings:
- https://platform.openai.com/docs/guides/embeddings
- https://docs.mistral.ai/capabilities/embeddings/

### About React:
- https://react.dev/ (Official React tutorial)

### About Express.js (backend framework):
- https://expressjs.com/

---

## 📞 Common Questions

### Q: Is it free to use this project?
A: The project code is free, but you'll pay for:
- MongoDB Atlas Vector Search (very cheap free tier available)
- Mistral AI Embeddings (approximately $0.11 per 1M tokens—very affordable!)
- Groq AI Reranking (free tier available)

### Q: How much data can I search?
A: MongoDB Atlas free tier gives you 512MB storage. That's ~100,000 test cases!

### Q: Can I modify the search algorithms?
A: Yes! All search code is in `src/scripts/search/`. You can modify and experiment!

### Q: What if I want to use different AI models?
A: You can replace Mistral AI with OpenAI, Google PaLM, or others that support embeddings. Just update the code and `.env`!

### Q: Is my data private?
A: Yes! Your data goes to **your** MongoDB cluster. Only you have access (protected by username/password).

---

## 🚀 Next Steps

1. Complete the [Installation & Setup](#installation--setup) section
2. Start the app: `npm run dev`
3. Upload some test case data
4. Create embeddings
5. Try different search methods
6. Explore the code files to understand how it works!

---

## 📝 Notes for Beginners

- **Don't be afraid to break things!** It's how you learn. Everything is reversible.
- **Read error messages carefully.** They usually tell you exactly what's wrong.
- **Google is your friend.** If you get stuck, search for the error message.
- **Take your time understanding one component at a time.** Don't try to understand everything at once!

---

## 💡 Tips for Success

1. **Start simple:** Upload a small Excel file first (5-10 test cases)
2. **Test each feature:** Try each search method one by one
3. **Read the console logs:** They tell you what's happening behind the scenes
4. **Keep your API keys safe:** Never share your `.env` file
5. **Monitor API usage:** Check Mistral AI and Groq dashboards to see costs

---

## 📄 License & Credits

This project demonstrates RAG architecture using:
- **MongoDB Atlas** for vector search
- **Mistral AI** for embeddings
- **Groq** for reranking/summarization
- **React** for the interface
- **Express.js** for the backend

---

**Happy Learning! 🎉**

If you have questions, check the troubleshooting section or feel free to explore the code files. Each file has comments explaining what it does!
