# RAG System Architecture

```mermaid
graph TD
    %% Document Processing Pipeline
    subgraph DocumentProcessing[Document Processing Layer]
        A[Source Documents] -->|Load| B[Document Loader]
        B -->|Raw Text| C[Text Splitter]
        C -->|Text Chunks| D[Document Chunks]
        
        %% Document Types
        A1[PDF Files] -.->|PyMuPDFLoader| B
        A2[Text Files] -.->|TextLoader| B
        A3[Other Formats] -.->|Custom Loaders| B
    end

    %% Embedding Pipeline
    subgraph EmbeddingLayer[Embedding Layer]
        D -->|Text| E[Embedding Manager]
        E1[Sentence Transformer Model] -.->|all-MiniLM-L6-v2| E
        E -->|Vector| F[Document Vectors]
    end

    %% Storage Layer
    subgraph StorageLayer[Storage Layer]
        F -->|Store| G[Vector Store Manager]
        G -->|Persist| H[(ChromaDB)]
        H -->|Index| I[Vector Index]
        
        %% Metadata Management
        M1[Document Metadata] -.->|Enrich| G
        M2[Vector Metadata] -.->|Track| G
    end

    %% Retrieval Pipeline
    subgraph RetrievalLayer[Retrieval Layer]
        J[User Query] -->|Input| K[RAG Retriever]
        K -->|Query Text| E
        K -->|Query Vector| L[Similarity Search]
        L -->|Search| I
        L -->|Filter| N[Score Threshold]
        N -->|Rank| O[Results]
    end

    %% Data Flow Lines
    classDef pipeline fill:#f9f,stroke:#333,stroke-width:2px
    classDef storage fill:#bbf,stroke:#333,stroke-width:2px
    classDef process fill:#bfb,stroke:#333,stroke-width:2px
    
    class A,A1,A2,A3 pipeline
    class H storage
    class E,K process
```

## Component Details

### Document Processing Layer
- **Document Loaders**: Multiple loaders for different file formats
  - PyMuPDFLoader: PDF processing
  - TextLoader: Plain text files
  - Extensible for other formats
- **Text Splitter**: RecursiveCharacterTextSplitter
  - Configurable chunk size (default: 1000)
  - Adjustable overlap (default: 200)
  - Maintains document coherence

### Embedding Layer
- **Embedding Manager**: Central embedding generation
  - Model: all-MiniLM-L6-v2
  - Dimension: 384
  - Batched processing
  - Progress tracking
- **Vector Generation**: Efficient text-to-vector conversion
  - Numpy array output
  - Shape validation
  - Error handling

### Storage Layer
- **Vector Store**: ChromaDB integration
  - Persistent storage
  - Collection management
  - Metadata tracking
  - Efficient indexing
- **Data Management**:
  - UUID-based document tracking
  - Rich metadata support
  - Content length tracking
  - Document indexing

### Retrieval Layer
- **RAG Retriever**: Core retrieval logic
  - Query processing
  - Vector similarity search
  - Score thresholding
  - Result ranking
- **Result Processing**:
  - Metadata enrichment
  - Distance calculation
  - Similarity scoring
  - Result formatting

## Data Flow

1. **Document Ingestion**:
   ```
   Source Documents → Document Loader → Text Chunks
   ```

2. **Embedding Processing**:
   ```
   Text Chunks → Embedding Manager → Document Vectors
   ```

3. **Storage Process**:
   ```
   Document Vectors + Metadata → Vector Store → ChromaDB
   ```

4. **Retrieval Flow**:
   ```
   Query → Embedding → Similarity Search → Filtered Results
   ```

## System Requirements
- Python 3.8+
- CUDA support (optional, for GPU acceleration)
- Sufficient storage for vector database
- RAM for embedding processing