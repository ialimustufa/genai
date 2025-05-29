# Week 4: Retrieval-Augmented Generation (RAG) with LlamaIndex & Gemini API

## Day 1: Environment Setup & LlamaIndex Installation

1. **Install Dependencies**  
   ```bash
   pip install google-genai llama-index faiss-cpu
   ```
2. **Configure Gemini**  
   ```python
   import os
   import google.generativeai as genai

   # Set your Gemini API key
   genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
   ```
3. **Import LlamaIndex**  
   ```python
   from llama_index import SimpleDirectoryReader, GPTVectorStoreIndex

   # Verify installation
   docs = SimpleDirectoryReader("docs/").load_data()
   index = GPTVectorStoreIndex.from_documents(docs)
   print("Index created with", len(docs), "documents.")
   ```

---

## Day 2: Document Ingestion & Preprocessing

1. **Prepare Document Folder**  
   - Create a `docs/` directory and add diverse text files (e.g., PDF extracts, `.txt`, `.md`).

2. **Read and Clean Data**  
   ```python
   from llama_index import SimpleDirectoryReader

   reader = SimpleDirectoryReader("docs/")
   documents = reader.load_data()

   # Example: print first document
   print(documents[0].get_text()[:200])
   ```

3. **Text Preprocessing (Optional)**  
   ```python
   def preprocess(text):
       return text.replace("\n", " ").strip()

   docs_clean = [d for d in documents]
   for d in docs_clean:
       d.text = preprocess(d.get_text())
   ```

---

## Day 3: Building the Vector Index

1. **Create FAISS Vector Store**  
   ```python
   from llama_index import GPTVectorStoreIndex, ServiceContext
   import faiss

   index = GPTVectorStoreIndex.from_documents(docs_clean)
   ```

2. **Persist Index to Disk**  
   ```python
   index.storage_context.persist(persist_dir="index_storage")
   ```

3. **Load Index from Disk**  
   ```python
   from llama_index import load_index_from_storage, StorageContext

   storage = StorageContext.from_defaults(persist_dir="index_storage")
   index = load_index_from_storage(storage)
   ```

---

## Day 4: Querying the RAG Pipeline

1. **Initialize Query Engine**  
   ```python
   query_engine = index.as_query_engine(similarity_top_k=5)
   ```

2. **Perform a Query**  
   ```python
   response = query_engine.query("What are the main findings in the Q2 report?")
   print("Context Retrieved:\n", response)
   ```

3. **Adjust Retrieval Parameters**  
   - Experiment with `similarity_top_k`, `chunk_size_limit`, and re-ranking strategies.

---

## Day 5: Generating Answers with Gemini

1. **Compose Generation Prompt**  
   ```python
   context = response  # from Day 4
   prompt = f"Context:\n{context}\n\nBased on the context, summarize the key points:"
   ```

2. **Call Gemini for Answer**  
   ```python
   gen_response = genai.models.generate_content(
       model="gemini-2.5-pro",
       contents=[prompt]
   )
   print("Generated Answer:\n", gen_response.text)
   ```

3. **Error Handling**  
   ```python
   try:
       gen_response = genai.models.generate_content(...)
   except Exception as e:
       print("Generation error:", e)
   ```

---

## Day 6: Integrating RAG in a Simple Web UI

1. **Create a Flask App**  
   ```python
   from flask import Flask, request, render_template
   from llama_index import load_index_from_storage, StorageContext

   import google.generativeai as genai
   import os

   genai.configure(api_key=os.getenv("GEMINI_API_KEY"))

   app = Flask(__name__)

   storage = StorageContext.from_defaults(persist_dir="index_storage")
   index = load_index_from_storage(storage)
   qe = index.as_query_engine()

   @app.route("/", methods=["GET", "POST"])
   def home():
       answer = ""
       if request.method == "POST":
           query = request.form["query"]
           context = qe.query(query)
           prompt = f"Context:\n{context}\n\nAnswer concisely:"
           gen_resp = genai.models.generate_content(
               model="gemini-2.5-pro", contents=[prompt]
           )
           answer = gen_resp.text
       return render_template("home.html", answer=answer)

   if __name__ == "__main__":
       app.run(host="0.0.0.0", port=8080)
   ```

2. **Create `templates/home.html`**  
   ```html
   <!DOCTYPE html>
   <html>
     <head><title>RAG Demo</title></head>
     <body>
       <form method="post">
         <input name="query" placeholder="Enter your question" />
         <button type="submit">Ask</button>
       </form>
       <pre>{{ answer }}</pre>
     </body>
   </html>
   ```

---

## Day 7: Mini-Project — RAG-Powered Q&A Bot

**Project Description**  
Develop a deployable Q&A application that:
- Indexes a custom document set
- Retrieves relevant context via LlamaIndex
- Generates answers with Gemini
- Serves via a Flask web interface

**Deliverables**  
- `index.py` for ingestion and indexing  
- `app.py` and `templates/home.html` for UI  
- `requirements.txt`  
- **README.md** with setup, usage, and example queries

**Bonus**  
- Add a `/api` endpoint returning JSON answers  
- Implement caching of recent Q&A pairs  
