# Experiment Notebooks
This repo is a collection of small experiments on LLM internals, embeddings, and memory systems. Each notebook is self-contained and documented in markdown.
## 1. Simple Encoder + FAISS
**Goal:** Learn how embeddings capture semantics and how FAISS can retrieve similar sentences.  
- Encoded 5 events + 5 queries using `SentenceTransformer("all-MiniLM-L6-v2")`.  
- Indexed with FAISS (`IndexFlatL2`).  
- Queries return nearest events by Euclidean distance.  

**Key takeaway:**  
Embeddings capture *surface similarity*, but not reasoning.  
E.g. FAISS fails to infer “dragon owns treasure” since ownership is not explicit.  

👉 [View the full notebook here](Simple_Encoder+FAISS.ipynb)

#### Planned Next Experiments
- Episodic memory with retrieval chains  
- Simple reasoning on top of embeddings 

---


