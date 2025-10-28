# RAG (Retrieval Augmented Generation) System

A robust implementation of a Retrieval Augmented Generation system that uses vector similarity search to find and retrieve relevant documents for given queries.

[View detailed architecture diagram](docs/architecture.md)

## Components

### 1. Document Processing
- **Document Loader**: Supports multiple document types (PDF, TXT)
- **Text Splitter**: Chunks documents into manageable segments with overlap

### 2. Embedding System
- **EmbeddingManager**:
  - Uses Sentence Transformers for text embedding
  - Manages model loading and embedding generation
  - Default model: "all-MiniLM-L6-v2"

### 3. Vector Storage
- **VectorStore**:
  - Persistent storage using ChromaDB
  - Efficient similarity search
  - Document metadata management
  - Automatic ID generation

### 4. Retrieval System
- **RAGRetriever**:
  - Query embedding generation
  - Similarity-based document retrieval
  - Configurable top-k results
  - Score threshold filtering

## Setup

1. Install dependencies:
```bash
pip install -r requirements.txt
```

2. Project structure:
```
rag/
├── data/
│   ├── pdf/            # PDF documents
│   ├── text_files/     # Text documents
│   └── vector_store/   # ChromaDB storage
├── notebook/
│   └── document.ipynb  # Example notebook
├── README.md
├── requirements.txt
└── main.py
```

## Usage

1. Load and process documents:
```python
from langchain_community.document_loaders import DirectoryLoader
loader = DirectoryLoader("data/pdf", glob="**/*.pdf", loader_cls=PyMuPDFLoader)
documents = loader.load()
```

2. Split into chunks:
```python
from langchain_text_splitters import RecursiveCharacterTextSplitter
splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)
chunks = splitter.split_documents(documents)
```

3. Generate embeddings and store:
```python
embedding_manager = EmbeddingManager()
embeddings = embedding_manager.generate_embeddings([doc.page_content for doc in chunks])
vector_store = VectorStore()
vector_store.add_documents(chunks, embeddings)
```

4. Retrieve relevant documents:
```python
retriever = RAGRetriever(vector_store, embedding_manager)
results = retriever.retrieve("your query here", top_k=5, score_threshold=0.0)
```

## Key Features

- **Persistent Storage**: Documents and embeddings are stored persistently using ChromaDB
- **Flexible Document Loading**: Support for various document formats
- **Configurable Chunking**: Adjustable chunk size and overlap
- **Efficient Retrieval**: Fast similarity search with customizable parameters
- **Rich Metadata**: Comprehensive document metadata tracking
- **Error Handling**: Robust error handling and logging throughout

## Dependencies

- langchain
- sentence-transformers
- chromadb
- numpy
- PyMuPDF (for PDF processing)
