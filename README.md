# 🤖 RAG-MCP: Retrieval-Augmented Generation with Model Context Protocol

A lightweight RAG pipeline exposed as an MCP (Model Context Protocol) server, enabling AI assistants like Claude to retrieve relevant ML knowledge and perform live web searches.

---

## 📌 Overview

This project combines:
- **Qdrant** as a vector database for storing and retrieving ML FAQ embeddings
- **HuggingFace embeddings** (`nomic-ai/nomic-embed-text-v1.5`) for semantic search
- **Bright Data** for live web search when queries fall outside the local knowledge base
- **FastMCP** to expose both capabilities as MCP tools consumable by any MCP-compatible client

---

## 🗂️ Project Structure
```text
.
├── server.py
├── rag_code.py
└── notebook.ipynb
```
---
## 🛠️ How It Works
```text
User Query
│
▼
MCP Server (server.py)
│
├── ML-related? ──► machine_learning_faq_retrieval_tool
│                        │
│                        ▼
│                  Embed Query (nomic-embed-text-v1.5)
│                        │
│                        ▼
│                  Qdrant Vector Search (top-3 results)
│                        │
│                        ▼
│                  Return relevant FAQ context
│
└── Other topics? ──► bright_data_web_search_tool
│
▼
Bright Data Google Search Proxy
│
▼
Return organic search results
```
---

## ⚙️ Setup & Installation

### 1. Install dependencies

```bash
pip install mcp llama-index-embeddings-huggingface qdrant-client tqdm python-dotenv requests
```

### 2. Start Qdrant locally

```bash
docker run -p 6333:6333 qdrant/qdrant
```

### 3. Configure environment variables

Create a `.env` file in the root directory:

```env
BRIGHT_DATA_USERNAME=your_username
BRIGHT_DATA_PASSWORD=your_password
```

### 4. Ingest data into Qdrant

Run through `notebook.ipynb` to embed the ML FAQ data and populate the Qdrant collection (`ml_faq_collection`).

### 5. Start the MCP server

```bash
python server.py
```

---

## 🔧 MCP Tools

### `machine_learning_faq_retrieval_tool(query: str) → str`
Performs semantic search over the local ML FAQ vector database and returns the top 3 most relevant Q&A pairs.

**Use when:** The user asks anything related to machine learning concepts, best practices, or workflows.

### `bright_data_web_search_tool(query: str) → list[str]`
Routes the query through a Bright Data proxy to fetch live Google search results.

**Use when:** The query is outside the scope of the local ML knowledge base.

---

## 🧠 Core Components (`rag_code.py`)

| Class | Responsibility |
|---|---|
| `EmbedData` | Loads HuggingFace model, generates and stores text embeddings in batches |
| `QdrantVDB` | Manages Qdrant collection creation and vector ingestion |
| `Retriever` | Embeds a query and retrieves the top-k matching documents from Qdrant |

---

## 📋 Knowledge Base

The current knowledge base contains **20 ML FAQ entries** covering:
- Data preprocessing & cleaning
- Feature engineering & selection
- Overfitting, underfitting & regularization
- Cross-validation & train-test splits
- Hyperparameter tuning
- Ensemble methods & deep learning
- Model evaluation metrics & interpretability

---

## 📎 Requirements

- Python 3.10+
- Docker (for Qdrant)
- Bright Data account (for web search tool)
- MCP-compatible client (e.g., Claude Desktop)
