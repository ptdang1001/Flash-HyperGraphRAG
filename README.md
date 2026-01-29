# Flash-HyperGraphRAG

**A High-Throughput, Scalable Framework for Knowledge Hypergraph RAG**

![Python](https://img.shields.io/badge/Python-3.14%2B-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Active_Development-orange)

## Abstract

**Flash-HyperGraphRAG** addresses the computational bottlenecks inherent in current graph-based Retrieval-Augmented Generation (RAG) systems. While existing implementations demonstrate the theoretical efficacy of hypergraphs for complex reasoning, they often struggle with scalability due to serial processing patterns and high I/O latency during index construction.

This project introduces a **system-level optimization framework** designed for massive-scale text corpora. By decoupling the indexing pipeline and introducing parallelized graph construction algorithms, Flash-HyperGraphRAG achieves significant improvements in throughput and cost-efficiency without sacrificing retrieval quality.

## Key Optimizations

This framework introduces optimizations across three key layers, bridging the gap between theoretical research and production-grade systems.

### 1. System Engineering & Concurrency
* **Asynchronous Parallel Pipeline:** Replaces sequential API interactions with a fully asynchronous, multi-stage pipeline (Chunking $\rightarrow$ Embedding $\rightarrow$ Extraction). This design maximizes GPU and API utilization rates.
* **Buffered Bulk I/O:** Implements a buffered I/O mechanism for database interaction. Instead of atomic transactions per node, data is aggregated into micro-batches for bulk insertion into Neo4j and Milvus, reducing network overhead by orders of magnitude.
* **Resource-Aware Scheduling:** Features dynamic worker allocation based on available CPU cores and API rate limits to prevent throttling while maximizing throughput.

### 2. Algorithmic Enhancements
* **Optimized Graph Traversal:** Refines the graph walking algorithm to prioritize high-confidence paths, effectively reducing noise in the retrieval set.
* **Hybrid Retrieval Logic:** Implements a weighted fusion of **Dense Vector Retrieval** (Milvus) and **Hypergraph Connectivity** (Neo4j). This dual-pathway approach captures both semantic similarity and structural relationships more effectively than single-modal methods.
* **Dynamic Context Pruning:** Algorithms designed to intelligently prune irrelevant sub-graphs during the inference phase, ensuring the LLM context window is utilized by only the most topologically relevant information.

### 3. Workflow & Process Optimization
* **Offline/Online Decoupling:** Strictly separates the computationally expensive **Index Construction Phase** (Offline Batch) from the **Inference Phase** (Online Real-time).
* **Resume-Capable Indexing:** The pipeline supports state checkpointing. Large-scale indexing jobs can be paused and resumed without data loss—crucial for processing terabyte-scale datasets.

## Performance Comparison

| Feature | Original Implementation | Flash-HyperGraphRAG |
| :--- | :--- | :--- |
| **Processing Paradigm** | Streaming / Serial | **Offline Batch / Parallel** |
| **I/O Strategy** | Transactional (ACID per node) | **Bulk Loading / Buffered** |
| **Throughput** | Low (Bottlenecked by RTT) | **High (Bottlenecked by Compute)** |
| **Scalability** | Small/Medium Corpora | **Massive / PB-Scale Corpora** |
| **Recovery** | Restart on failure | **Checkpoint & Resume** |

## Architecture Overview

The system is composed of three primary modules:

1.  **Flash-ETL Engine:** A distributed processing engine responsible for raw text ingestion, parallel chunking, and graph extraction.
2.  **Storage Layer:** * **Neo4j:** Stores the Hypergraph topology and semantic relationships.
    * **Milvus:** Stores high-dimensional vector embeddings for entities and text chunks.
3.  **Inference Server:** A FastAPI-based retrieval engine with modular ranking logic and a React-based visualization dashboard.

## Getting Started

### Prerequisites

* **Python 3.10+**
* **Docker & Docker Compose** (for Neo4j and Milvus orchestration)
* **OpenAI API Key** (or compatible local LLM endpoint)

### Installation

```bash
git clone [https://github.com/your-username/Flash-HyperGraphRAG.git](https://github.com/your-username/Flash-HyperGraphRAG.git)
cd Flash-HyperGraphRAG

# uv 
