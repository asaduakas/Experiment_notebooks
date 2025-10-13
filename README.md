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

👉 [View the full notebook here](notebooks/01_faiss_encoder.ipynb)

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
In addition, when comparing embeddings in high-dimenstions, use cosine similarity and NOT Euclidean. When comparing many cosine similarities, matrix multiplication is your friend!

👉 [View the full notebook here](notebooks/02_episodic_memory.ipynb)

#### Planned Next Experiments
- Add contradiction detection between world facts and memories (Prototype 3).
- Introduce summarization module to compress long histories.
- Test evaluation metrics: Recall@K, contradiction rate, grounding fraction.

---
### 3. Zero-Shot Contradiction Checker
**Goal:** Detect logical contradictions between the player's next action and the current world state or past memories.

**Approach:**
- Used `facebook/bart-large-mnli` in a zero-shot setup to classify contradictions.
- For each test case, the player's action is compared against:
  - World Facts (canonical truth of the game world)
  - Retrieved memories (episodic history from past turns)
- Each `(fact, action)` pair is passed through the NLI model, returning a contradiction probability.
- If the probability exceeds a configurable threshold (default = 0.6), it's flaggeed as a potential conflict.

**Automated Test Suite:**
- Test cases are stored in `tests/contradiction_suite_v1.0.jsonl`
- Each entry defines:
  - `player_action`,`world_facts`, `retrieved_memories`
  - Expected behavior: `contradiction` or `no_contradiction`
- The evaluator script runs all cases, compares predicted vs expected behavior, and prints a summary of passes/fails.

**Key Takeaway:**
- World facts are treated as canonical truth, while retrieved memories serve as *subjective* or *possibly outdated* recollections.
- Not all contradictions are equal:
  - **Hard contradictions:** Impossible actions (e.g. "talk to the dead dragon")
  - **Soft contradictions:** Technically possible, but world logic alters outcome (e.g. "cast fireball in protected town" -> spell fizzles)
  - These findings suggest a need for graded contradiction handling, distinguishing between strict impossibility and expectation mismatch.
 
👉 [View the full notebook here](notebooks/03_zero_shot_contradiction_checker.ipynb)
 
#### Planned Next Experiments
Integrate a reasoning layer that classifies contradictions into severity tiers and suggests corrective world updates or narrative clarifications.
