# 📄 PDF RAG Chatbot

> Chat with any PDF using Retrieval-Augmented Generation — ask questions, get grounded answers with page citations.

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)
![LangChain](https://img.shields.io/badge/LangChain-RAG-1C3C3C?style=flat)
![OpenAI](https://img.shields.io/badge/OpenAI-GPT--4o--mini-412991?style=flat&logo=openai&logoColor=white)
![Streamlit](https://img.shields.io/badge/UI-Streamlit-FF4B4B?style=flat&logo=streamlit&logoColor=white)
![FAISS](https://img.shields.io/badge/VectorStore-FAISS-blue?style=flat)

---

## What it does

Upload any PDF — research paper, legal doc, product manual, textbook — and ask questions in natural language. The app retrieves the most relevant sections using vector search and generates accurate, grounded answers via GPT-4o-mini. Every answer includes source citations with page numbers so you can verify the response.

---

## Demo

![PDF RAG Chatbot Demo](https://raw.githubusercontent.com/meghanakotte1/pdf-rag-chatbot/main/.streamlit/demo.gif)

> *Upload a PDF → Ask a question → Get an answer with cited page numbers*

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    INGESTION PIPELINE                   │
│                                                         │
│  PDF Upload → PyPDFLoader → RecursiveCharacterSplitter  │
│       → OpenAIEmbeddings → FAISS Vector Index           │
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│                    RETRIEVAL PIPELINE                   │
│                                                         │
│  User Question → Embed Query → Top-k FAISS Search (k=4) │
│       → ConversationalRetrievalChain                    │
│       → GPT-4o-mini (temp=0) → Answer + Source Docs     │
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│                    STREAMLIT UI                         │
│                                                         │
│  Chat interface + Source expander + Conversation memory │
└─────────────────────────────────────────────────────────┘
```

**Key design decisions:**
- `chunk_size=1000, chunk_overlap=200` — small enough to be specific, large enough to preserve sentence context
- `temperature=0` — forces factual, deterministic responses (critical for RAG accuracy)
- `gpt-4o-mini` — better instruction-following than gpt-3.5-turbo at lower cost
- `ConversationBufferMemory` — enables natural follow-up questions across turns
- `return_source_documents=True` — every answer is traceable to a page

---

## Features

- **Multi-turn conversation** — ask follow-up questions with full context retention
- **Page-level citations** — every answer shows which chunks/pages it drew from
- **Clean Streamlit UI** — sidebar upload, chat interface, collapsible source viewer
- **Temp file cleanup** — uploaded PDFs are deleted after processing, no data stored
- **Model choice** — uses GPT-4o-mini for cost-efficient, high-quality responses

---

## Setup

### Prerequisites

- Python 3.10+
- An [OpenAI API key](https://platform.openai.com/api-keys)

### Installation

```bash
# Clone the repo
git clone https://github.com/meghanakotte1/pdf-rag-chatbot.git
cd pdf-rag-chatbot

# Install dependencies
pip install -r requirements.txt
```

### Configure API key

Create `.streamlit/secrets.toml`:

```toml
OPENAI_API_KEY = "sk-your-key-here"
```

> Never commit this file — it's already in `.gitignore`.

### Run

```bash
streamlit run app.py
```

Open [http://localhost:8501](http://localhost:8501) in your browser.

---

## Usage

1. Upload a PDF using the sidebar uploader
2. Click **Process PDF** and wait for indexing to complete
3. Type a question in the chat input
4. Expand **📚 Sources** under any answer to see the retrieved chunks and page numbers

---

## Project structure

```
pdf-rag-chatbot/
├── app.py              # Streamlit UI — upload, chat, source display
├── rag_engine.py       # Core RAG logic — PDF loading, chunking, embedding, chain
├── .streamlit/
│   ├── config.toml     # Streamlit theme config
│   └── secrets.toml    # API key (not committed)
├── requirements.txt
└── README.md
```

---

## Requirements

```
streamlit>=1.32.0
langchain>=0.1.0
langchain-community>=0.0.20
langchain-openai>=0.0.8
faiss-cpu>=1.7.4
pypdf>=3.17.0
```

---

## Limitations & planned improvements

| Area | Current | Planned |
|------|---------|---------|
| Memory | `ConversationBufferMemory` (full history) | `ConversationSummaryBufferMemory` to handle long sessions |
| Vector store | FAISS (in-memory, ephemeral) | PGVector for persistent storage |
| Multi-document | Single PDF per session | Multiple PDFs with document filtering |
| Deployment | Local only | Streamlit Cloud / Docker |
| Embeddings | OpenAI `text-embedding-ada-002` | Explore `text-embedding-3-small` for cost reduction |

---

## Tech stack

| Layer | Technology |
|-------|-----------|
| LLM | OpenAI GPT-4o-mini |
| Embeddings | OpenAI text-embedding-ada-002 |
| Vector store | FAISS (in-memory) |
| RAG framework | LangChain |
| PDF parsing | PyPDFLoader |
| Text splitting | RecursiveCharacterTextSplitter |
| Conversation memory | ConversationBufferMemory |
| UI | Streamlit |
| Language | Python 3.10+ |

---

## Related projects

This project is part of a broader exploration of AI-powered backend systems:

- **[Online Banking Microservices](https://github.com/meghanakotte1/online-banking-microservices)** — event-driven Java/Spring Boot system with Kafka
- **AI Document Chat (Spring AI version)** — rebuilding this concept in Java using Spring AI + PGVector + PostgreSQL *(in progress)*

---

## Author

**Meghana Kotte** — AI Software Engineer

[![LinkedIn](https://img.shields.io/badge/LinkedIn-meghanakotte-0A66C2?style=flat&logo=linkedin)](https://linkedin.com/in/meghanakotte)
[![GitHub](https://img.shields.io/badge/GitHub-meghanakotte1-181717?style=flat&logo=github)](https://github.com/meghanakotte1)
[![Email](https://img.shields.io/badge/Email-kottemeghana250@gmail.com-EA4335?style=flat&logo=gmail)](mailto:kottemeghana250@gmail.com)

Open to full-time roles in Java/AI engineering — remote, hybrid, or on-site across the US.
