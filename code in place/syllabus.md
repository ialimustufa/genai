# Generative AI Tools Syllabus

This tool‐first syllabus equips advanced-beginner Python developers with hands-on experience using industry-leading platforms and APIs—Replit Agent, Google AI Studio’s Gemini API, LangChain, LlamaIndex, Lovable.dev, Replicate, Streamlit, Gradio, Bolt.new, and DVC/CML—alongside best practices for prompt engineering, multi-modality, and security/compliance. Over eight modules, you’ll complete mini-projects that yield a portfolio of deployable Generative AI applications.

---

## Module 1: Rapid Prototyping with Replit Agent & Gemini API

**Tools & Platforms**
- Replit Agent  
- Google AI Studio (ai.google.dev) with Gemini API  

**Learning Objectives**
1. Transform natural-language prompts into live app prototypes using Replit Agent.  
2. Obtain and manage Gemini API keys; call `generateContent` endpoints for text, image, and code generation.  

**Exercise**
- Build and deploy a CLI tool on Replit that sends prompts to Gemini and displays responses in a minimal web UI.

---

## Module 2: Prompt Engineering & Evaluation

**Tools & Concepts**
- Prompt templates, Chain-of-Thought, ReAct patterns  
- Evaluation metrics: perplexity, ROUGE, BLEU  

**Learning Objectives**
1. Craft and refine prompts for GPT-4o and Gemini models.  
2. Implement automated evaluation scripts to compare prompt variants.  

**Mini-Project**
- Design three prompt variants for a summarization task and select the best using ROUGE scoring.

---

## Module 3: Building LLM Applications with LangChain

**Tool**
- LangChain  

**Learning Objectives**
1. Chain LLM calls into multi-step workflows.  
2. Manage conversational memory.  
3. Integrate external tools (e.g., REST APIs, calculators) via the agent interface.  

**Hands-On**
- Create a LangChain agent that answers queries, performs arithmetic via an external API, and persists session context.

---

## Module 4: Retrieval-Augmented Generation with LlamaIndex

**Tool**
- LlamaIndex  

**Learning Objectives**
1. Index documents (PDFs, Markdown) into a vector store (FAISS or Pinecone).  
2. Build a RAG pipeline that retrieves context and drives LLM generation.  
3. Evaluate retrieval quality (precision@k, semantic similarity).  

**Exercise**
- Develop a PDF Q&A chatbot: upload documents, retrieve passages via LlamaIndex, and generate answers with Gemini or GPT-4o.

---

## Module 5: Multi-Modality with Lovable & Replicate

**Tools**
- Lovable.dev (Edit Mode & Chat Mode)  
- Replicate API (access to Stable Diffusion, Whisper, CLIP, etc.)  

**Learning Objectives**
1. Scaffold full-stack multimodal web apps supporting image, video, and speech from text prompts.  
2. Integrate Replicate models into your applications without managing infrastructure.  

**Mini-Project**
- Build a Lovable prototype that ingests user text, calls a Replicate video model, and returns a short clip or illustrated storyboard.

---

## Module 6: Rapid UI Prototyping with Streamlit, Gradio & Bolt.new

**Tools**
- Streamlit (`st.chat`, `st.image`)  
- Gradio (`Interface`, `Blocks`)  
- Bolt.new cloud IDE  

**Learning Objectives**
1. Create interactive dashboards and chat interfaces in Streamlit and Gradio.  
2. Use Bolt.new to generate, edit, and deploy full-stack demos in the browser.  

**Hands-On**
- Prototype a multimodal chat app in Streamlit that uses both Gemini API and a Replicate image model; refine and deploy on Bolt.new.

---

## Module 7: Versioning & CI/CD with DVC & CML

**Tools**
- DVC for data and model version control  
- Continuous Machine Learning (CML) with GitHub Actions  

**Learning Objectives**
1. Track datasets and model artifacts alongside code.  
2. Automate training, evaluation, and metric reporting on each commit or pull request.  

**Exercise**
- Set up a GitHub repository that uses DVC to version image data, trains an LLM summarizer in CI, and comments evaluation metrics via CML.

---

## Module 8: Monitoring, Security & Compliance

**Tools & Concepts**
- Prometheus & Grafana for metrics and dashboards  
- Prompt-injection defenses, input sanitization  
- GDPR compliance: data minimization, transparency, user rights  

**Learning Objectives**
1. Monitor latency, error rates, and token usage in production.  
2. Secure APIs against prompt-injection and data leakage.  
3. Ensure ethical data practices and compliance with EU AI Act and GDPR.  

**Discussion**
- Review real-world AI incidents, draft an incident response plan, and create a compliance checklist.

---
