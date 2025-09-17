# Experiment Notebooks
### 1. Simple Encoder+FAISS
Embeddings are vector representations of words or sentences. They are used in LLM’s to represent language in a format that the model can learn from and understand. Since they are vectors that hold contextual information, many interesting operations can be made with them.

For instance, in this notebook I’ve encoded a set of 5 events and 5 queries about these events into embeddings using the lightweight all-MiniLM encoder model. 

```
events = [
    "The player entered the dark cave.",
    "The dragon sleeps on a pile of gold.",
    "The merchant offered a healing potion.",
    "The knight swore loyalty to the kings.",
    "The village was attacked by bandits."
]
model = SentenceTransformer("all-MiniLM-L6-v2")
embeddings = model.encode(events, convert_to_numpy=True)
```
Now, we need a way to store these vectors and query them to do interesting things. FAISS is a database which allows for fast index search, instead of looping through manually. 
```
d = embeddings.shape[1]
index = faiss.IndexFlatL2(d)
index.add(embeddings)
print("Number of vectors indexed:", index.ntotal)
```
Since they are vectors, I can compare them using FAISS to find shortest euclidian distance between them and find which event has an answer to which query, because similar sentences will be close to each other in this vector space!

<img width="1112" height="1041" alt="image" src="https://github.com/user-attachments/assets/35173421-0948-417d-89a6-5536c484f221" />

As we can see, the results are not that bad! One thing I noticed, in the dragon-treasure query, the model failed. That's a clear indication that this simple setup just captures surface similarity, not reason or inference. Though, it is still impressive as  all of the converting, lookup, attention, and pooling happens inside just one line of `model.encode`

In conclusion, we translated the sentences into mathematically represented vector space, which is pretty awesome!



