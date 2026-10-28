# Local RAG-Based Question Answering

A Python-based AI assistant that answers questions using information stored in local text documents. It combines **Ollama, text embeddings, cosine similarity, and a chat model** to retrieve relevant information and generate context-aware responses.

This project demonstrates the fundamentals of Retrieval-Augmented Generation (RAG) and semantic search without requiring a cloud-hosted AI API.

## ✨ Features

- **Local AI inference:** Run compatible language models locally using Ollama.
- **Document loading:** Automatically loads `.txt` files from the `knowledge/` directory.
- **Text embeddings:** Converts documents and user questions into numerical vectors using `nomic-embed-text`.
- **Semantic retrieval:** Uses cosine similarity to identify the document whose embedding most closely matches the user's question.
- **Context-aware answers:** Sends the retrieved document and the user's question to a chat model.
- **Configurable models:** Uses environment variables to configure the chat model and API connection.
- **Interactive terminal interface:** Ask questions continuously until you enter `quit`.

## 🛠️ Tech Stack

- **Python** — Application logic and orchestration
- **Ollama** — Local model runtime and API server
- **Nomic Embed Text** — Text embedding generation
- **Qwen3:4b** — Example chat model for answer generation
- **OpenAI Python SDK** — Communicates with Ollama through its OpenAI-compatible API
- **NumPy** — Numerical operations and cosine similarity
- **python-dotenv** — Loads configuration from environment variables

## 🧠 How It Works

The project follows a basic Retrieval-Augmented Generation (RAG) workflow.

1. **Load documents:** Read all `.txt` files from the `knowledge/` directory.
2. **Generate document embeddings:** Convert each document into a numerical vector using `nomic-embed-text`.
3. **Accept a question:** Read the user's question through the terminal.
4. **Generate a question embedding:** Convert the question into a vector using the same embedding model.
5. **Calculate similarity:** Compare the question vector with each document vector using cosine similarity.
6. **Retrieve relevant context:** Select the document with the highest similarity score.
7. **Generate an answer:** Send the selected document and the user's question to the configured chat model.
8. **Display the response:** Print the generated answer in the terminal.

### Architecture

```text
                  User Question
                        |
                        v
              Generate Question Embedding
                        |
                        v
               Cosine Similarity Search
                        |
                        v
             Select Best-Matching Document
                        |
                        v
             Retrieve Document Text
                        |
                        v
              Build Contextual Prompt
                        |
                        v
                 Ollama Chat Model
                        |
                        v
                 Generated Answer
```

### Project Components

| Component | Responsibility |
|---|---|
| `main.py` | Coordinates document loading, retrieval, prompting, and answer generation |
| `retriever.py` | Loads local text files and provides an optional exact-phrase retrieval function |
| `test_embedding.py` | Generates text embeddings using Ollama |
| `similarity.py` | Calculates cosine similarity between vectors |
| `knowledge/` | Stores documents used as the assistant's knowledge base |
| `.env` | Stores local configuration settings |

## 📁 Project Structure

```text
My-AI-Assistant/
│
├── knowledge/
│   ├── python.txt
│   └── ai.txt
│
├── main.py
├── retriever.py
├── test_embedding.py
├── similarity.py
├── .env
├── .gitignore
├── requirements.txt
└── README.md
```

Your actual filenames may differ. Update this structure if your project uses different names or directories.

## ⚙️ Prerequisites

Before running the project, install:

- Python 3.10 or a compatible newer version
- Ollama
- A chat model supported by your local Ollama installation
- The `nomic-embed-text` embedding model

## 🚀 Installation and Setup

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
cd YOUR_REPOSITORY
```

Replace the repository URL with your actual GitHub repository URL.

### 2. Create a virtual environment

On Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

If you use Command Prompt instead:

```bat
.venv\Scripts\activate.bat
```

### 3. Install dependencies

Create a `requirements.txt` file containing:

```text
openai
python-dotenv
numpy
```

Install the dependencies:

```bash
pip install -r requirements.txt
```

### 4. Install Ollama models

Download the embedding model:

```bash
ollama pull nomic-embed-text
```

Download the example chat model:

```bash
ollama pull qwen3:4b
```

You can substitute another compatible chat model if your configuration and hardware support it.

### 5. Configure environment variables

Create a `.env` file in the project root:

```env
BASE_URL=http://localhost:11434/v1
API_KEY=ollama
MODEL=qwen3:4b
```

The example uses Ollama's local OpenAI-compatible API endpoint. The `API_KEY` value is a placeholder for local use, not a real OpenAI API key.

Ensure Ollama is running before starting the application.

### 6. Add knowledge documents

Create a folder named `knowledge` in the project root.

Example: `knowledge/python.txt`

```text
Python is a high-level programming language.
It is widely used in automation, web development,
data analysis, and artificial intelligence.
```

Example: `knowledge/ai.txt`

```text
Artificial Intelligence enables machines to perform
tasks that typically require human intelligence.
Machine learning is one approach used to build AI systems.
```

You can add more `.txt` files to expand the assistant's knowledge base.

### 7. Run the application

```bash
python main.py
```

The assistant will start in your terminal and wait for questions.

## 💬 Example Usage

```text
========================================
      My AI Assistant
========================================

You : What is Python?

AI : Python is a high-level programming language
     used for automation, web development, data
     analysis, and artificial intelligence.

You : What is machine learning?

AI : Machine learning is an approach used to build
     AI systems that learn patterns from data.

You : quit

AI  : Goodbye! Have a great day.
```

*Example output only. Actual answers depend on your knowledge documents, model, and similarity results.*

## 🔍 Understanding the Core Concepts

### 1. Embeddings

An embedding represents text as a numerical vector.

The project uses:

```python
client.embeddings.create(
    model="nomic-embed-text",
    input=content
).data[0].embedding
```

The standard `nomic-embed-text` model produces a 768-dimensional embedding. The same model is used for documents and user questions so that their vectors can be compared.

### 2. Cosine Similarity

Cosine similarity compares the direction of two vectors to estimate their similarity.

The implementation uses NumPy:

```python
np.dot(v1, v2) / (
    np.linalg.norm(v1) *
    np.linalg.norm(v2)
)
```

A higher score generally indicates greater vector similarity. However, a score alone does not guarantee that a document contains the correct answer.

### 3. Retrieval-Augmented Generation

RAG combines information retrieval with language generation.

Rather than relying only on the chat model's existing knowledge, the application retrieves a document and includes its text in the prompt. The model then generates an answer using that context.

## ⚠️ Current Limitations

- The system retrieves only one document per question.
- It always selects the highest-scoring document, even if every document is irrelevant.
- Documents are embedded again whenever the application starts.
- Entire documents are embedded as single units; long documents are not split into smaller chunks.
- The chat model may still generate information that is not supported by the retrieved context.
- Only `.txt` files in the configured knowledge directory are loaded.
- The optional `retrieve()` function in `retriever.py` performs exact-phrase matching and is separate from the embedding-based retrieval flow.

## 🔮 Future Improvements

- Add a configurable minimum similarity threshold.
- Retrieve multiple relevant documents instead of just one.
- Split long documents into smaller chunks before embedding.
- Cache or persist embeddings to reduce startup time.
- Add support for PDF, Markdown, and DOCX documents.
- Store and search embeddings using a vector database.
- Add conversation history and multi-turn context.
- Build a web interface using Flask, FastAPI, or Streamlit.
- Add error handling, logging, and automated tests.
- Improve source attribution so answers identify the documents used.

## 🔐 Security

- Keep `.env` out of version control.
- Do not commit real API keys, passwords, or private documents.
- Review your `.gitignore` before publishing the repository.
- Be aware that local inference does not automatically guarantee that every application component is private; review any external services you configure.

Example `.gitignore` entries:

```gitignore
.env
.venv/
__pycache__/
*.py[cod]
```

## 🎯 Learning Objectives

This project helps develop practical understanding of:

- Python modules and imports
- Reading and organizing local files
- Environment-variable configuration
- Embedding generation
- Vector dimensions and numerical representations
- Cosine similarity
- Semantic search and document retrieval
- Prompt construction
- Local language-model inference
- The fundamentals of RAG architecture


