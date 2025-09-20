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
### 2. Episodic Memory
**Goal:** Store player turns as episodic memories with metadata and retrieve them to ground a Dungeon Master prompt.  
- Implemented `add_turn()` to save events as embeddings + metadata (players, NPCs, tags, scene_id, timestamp).  
- Indexed with FAISS (`IndexFlatL2`) for similarity search.  
- `query_memory()` returns top-K most relevant past events with metadata.
- Combined **world facts + retrieved memories + player action** into a structured Dunegon Master prompt.
- Used a local Mistralai-7B model as decoder.

**Key takeaway:**  
Episodic memory with metadata lets the DM model recall past events and enrich context beyond raw surface similarity. But contradictions still appear if world facts and memories clash (e.g. “dragon is alive” vs “dragon was killed”). This highlights the need for conflict resolution in the memory pipeline.

👉 [View the full notebook here](Episodic_Memory+RAG_barebones.ipynb)

#### Planned Next Experiments
- Add contradiction detection between world facts and memories (Prototype 3).
- Introduce summarization module to compress long histories.
- Test evaluation metrics: Recall@K, contradiction rate, grounding fraction.
