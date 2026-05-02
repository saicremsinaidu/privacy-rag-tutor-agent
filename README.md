# Privacy-Preserving RAG Tutor & Quiz Agent

A fully offline, privacy-first AI assistant that answers questions and generates quizzes from local documents — no cloud, no data leakage, no GPU required.

---

## What It Does

Most AI tutoring tools send your documents to the cloud (ChatGPT, Gemini, etc.), creating data privacy risks. This system runs entirely locally using a local LLM and a local vector database, making it safe for sensitive or proprietary documents.

**Two core agents:**

**Q&A Tutor Agent**
- Accepts any natural language question
- Converts it into embeddings and retrieves relevant document chunks from Qdrant
- Generates a cited, grounded answer using GPT4All (offline LLM)
- Returns document name + page number with every response

**Quiz Agent**
- Generates topic-specific or random quizzes from your documents
- Supports MCQ, True/False, and open-ended question formats
- Auto-grades answers using fuzzy similarity scoring
- Returns feedback, correct answers, and source citations

---

## Tech Stack

| Layer | Technology |
|---|---|
| Language | Python 3.9+ |
| LLM (offline) | GPT4All |
| Embeddings | sentence-transformers (`all-MiniLM-L12-v2`) |
| Vector Database | Qdrant (Docker) |
| PDF Parsing | PyMuPDF (fitz) |
| Answer Grading | FuzzyWuzzy + python-Levenshtein |
| Web UI | Gradio |

---

## System Requirements

- Python 3.9 or above
- Docker (for Qdrant)
- 8GB RAM minimum
- CPU-only — no GPU required

---

## Quick Start

### 1. Clone the repo

```bash
git clone https://github.com/your-username/privacy-rag-tutor-agent.git
cd privacy-rag-tutor-agent
```

### 2. Set up virtual environment

```bash
# macOS / Linux
python3 -m venv venv
source venv/bin/activate

# Windows
python -m venv venv
venv\Scripts\activate
```

> If PowerShell throws a permission error on Windows:
> `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser`

### 3. Install dependencies

```bash
pip install fuzzywuzzy python-Levenshtein sentence-transformers qdrant-client gradio gpt4all pymupdf
```

### 4. Start Qdrant (vector database)

```bash
docker pull qdrant/qdrant
docker run -p 6333:6333 qdrant/qdrant
```

### 5. Add your documents

Place any PDF files into the `knowledge_base/` folder.

### 6. Initialize and populate the database

```bash
python Scripts/initialise_qdrant.py
python Scripts/Data_insertion_qdrant.py
```

### 7. Launch the app

```bash
python Scripts/chatbot_application.py
```

Open your browser at: `http://127.0.0.1:7860`

---

## Project Structure

```
project/
├── Scripts/
│   ├── initialise_qdrant.py       # Creates Qdrant collection
│   ├── Data_insertion_qdrant.py   # Embeds and stores PDF chunks
│   └── chatbot_application.py     # Launches Gradio UI with both agents
├── knowledge_base/
│   └── (place your PDF documents here)
├── README.md
└── requirements.txt
```

---

## How It Works

1. PDFs are extracted page-by-page using PyMuPDF
2. Each page is chunked into ~500–700 character segments
3. Chunks are embedded using `all-MiniLM-L12-v2` (384-dim vectors, cosine similarity)
4. Embeddings are stored in a local Qdrant collection with metadata: `text`, `document_name`, `page_number`
5. At query time, the question is embedded and the top-k most relevant chunks are retrieved
6. GPT4All generates a response grounded in the retrieved context, with citations

---

## Architecture

![System Architecture](image.png)

---

## Troubleshooting

| Issue | Fix |
|---|---|
| Qdrant not connecting | Restart Docker: `docker run -p 6333:6333 qdrant/qdrant` |
| PyMuPDF import error | `pip install pymupdf` |
| venv not activating on Windows | `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` |

---

## Roadmap

- [ ] Support for additional document formats (DOCX, TXT, HTML)
- [ ] Exportable quiz reports (PDF)
- [ ] Dark mode UI
- [ ] Swap GPT4All for other local models (Ollama, LlamaCpp)
- [ ] Metadata filtering (query by document or topic)

---

## License

MIT
