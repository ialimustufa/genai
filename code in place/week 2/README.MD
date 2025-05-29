# Week 2: Advanced Prompt Engineering with Gemini

## Day 1: Foundations of Prompt Engineering

1. **Concepts & Reading**
   - Read the OpenAI Prompt Engineering Guide: https://platform.openai.com/docs/guides/prompt-engineering  
   - Review examples of effective prompts in the OpenAI Cookbook: https://github.com/openai/openai-cookbook/blob/main/examples/Prompt_Engineering.ipynb  

2. **Hands-On Exploration**
   - In a new Replit Python repl, install the OpenAI package:
     ```bash
     pip install openai
     ```
   - Write a script `prompt_basics.py`:
     ```python
     import os
     import openai

     openai.api_key = os.getenv("OPENAI_API_KEY")

     def simple_prompt(prompt_text):
         response = openai.ChatCompletion.create(
             model="gpt-4o",
             messages=[{"role": "user", "content": prompt_text}]
         )
         return response.choices[0].message.content

     if __name__ == "__main__":
         print(simple_prompt("Translate 'Hello, world!' into Spanish."))
     ```
   - Run and observe variations by changing the prompt wording.

---

## Zero-Shot Prompting

**Description**  
Zero-shot prompting uses direct natural-language instructions without examples, relying on the model’s broad pre-training.

**Gemini Code Example**  
```python
import google.generativeai as genai
import os

genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
response = genai.models.generate_content(
    model="gemini-2.5-pro",
    contents=["Translate 'Welcome' to German."]
)
print(response.text)
```

**Real-World Use**  
On-demand translation in chatbots, where users request arbitrary language conversions without prior fine-tuning.

---

## One-Shot Prompting

**Description**  
One-shot prompting provides exactly one example to guide the model’s style or format.

**Gemini Code Example**  
```python
import google.generativeai as genai
import os

genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
prompt = (
    "Translate to French:\n"
    "English: Hello → French: Bonjour\n\n"
    "English: Good night → French:"
)
response = genai.models.generate_content(
    model="gemini-2.5-pro",
    contents=[prompt]
)
print(response.text.strip())
```

**Real-World Use**  
Customer-support bots tailoring replies to brand tone with a single exemplar.

---

## Few-Shot Prompting

**Description**  
Few-shot prompting embeds multiple input→output pairs (shots) to enable in-context learning without retraining.

**Gemini Code Example**  
```python
import google.generativeai as genai
import os

genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
examples = [
    "Q: 2+2? A: 4",
    "Q: 5*6? A: 30"
]
prompt = "\n".join(examples) + "\nQ: 12/4? A:"
response = genai.models.generate_content(
    model="gemini-2.5-pro",
    contents=[prompt]
)
print(response.text.strip())
```

**Real-World Use**  
Code assistants showing a few input–output pairs before requesting new code snippets.

---

## Chain-of-Thought (CoT) Prompting

**Description**  
CoT prompting asks the model to generate intermediate reasoning steps, improving accuracy on complex tasks.

**Gemini Code Example**  
```python
import google.generativeai as genai
import os

genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
prompt = "Solve step-by-step: If a train travels 60 km/h for 2 hours, how far does it go?"
response = genai.models.generate_content(
    model="gemini-2.5-pro",
    contents=[prompt],
    temperature=0.2
)
print(response.text)
```

**Real-World Use**  
Math tutoring apps that display “show your work” explanations to students.

---

## ReAct Prompting

**Description**  
ReAct interleaves reasoning (“Thought: …”) and actions (e.g., API calls) in a single prompt to enable dynamic tool usage during generation.

**Gemini Code Example**  
```python
import google.generativeai as genai
import os

genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
prompt = (
    "You are an AI agent. When given a question, think then act using a calculator API, "
    "then provide your answer.\n\n"
    "Question: What is 15% of 200?\nThought:"
)
chat = genai.chat.create(model="gemini-2.5-pro", enable_streaming=True)
for part in chat.send(messages=[{"role":"user","content":prompt}]).candidates[0].content.parts:
    print(part.text)
```

**Real-World Use**  
R&D assistants fetching real-time data (e.g., weather, stock prices) mid-prompt for up-to-date answers.

---

## Retrieval-Augmented Generation (RAG)

**Description**  
RAG combines a retrieval step (searching a vector store) with generation, ensuring domain-specific and current context in responses.

**Integration Example**  
```python
from llama_index import GPTVectorStoreIndex, SimpleDirectoryReader
import google.generativeai as genai
import os

# Configure Gemini
genai.configure(api_key=os.getenv("GEMINI_API_KEY"))

# Build index
docs = SimpleDirectoryReader("knowledge_base/").load_data()
index = GPTVectorStoreIndex.from_documents(docs)
query_engine = index.as_query_engine()

# Retrieve and generate
context = query_engine.query("Summarize the Q1 sales report.")
response = genai.models.generate_content(
    model="gemini-2.5-pro",
    contents=[f"Context:\n{context}\n\nSummarize:"]
)
print(response.text)
```

**Real-World Use**  
Enterprise legal-research bots retrieving case law snippets before drafting summaries.

---

## Least-to-Most Prompting

**Description**  
This strategy decomposes a complex task into sub-questions, solving each in sequence to reach the final answer.

**Gemini Code Example**  
```python
import google.generativeai as genai
import os

genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
prompt = (
    "Decompose and solve:\n"
    "1. What is 5 + 3?\n"
    "2. Multiply result by 2.\n"
    "3. Subtract 4.\n"
    "Answer each step."
)
response = genai.models.generate_content(
    model="gemini-2.5-pro",
    contents=[prompt],
    temperature=0.1
)
print(response.text)
```

**Real-World Use**  
Financial-forecasting tools breaking down economic indicators into stepwise analyses.

---

## Capstone Project: Prompt Enhancer Tool

**Description**  
Build a CLI & web app that takes a user’s raw prompt and returns an enhanced version—adding context, specifying roles, and applying best practices—using Gemini.

**Example CLI (`enhancer.py`)**  
```python
import click, json, google.generativeai as genai, os
from evaluate_prompts import evaluate

genai.configure(api_key=os.getenv("GEMINI_API_KEY"))

@click.command()
@click.argument("prompt")
def enhance(prompt):
    enhanced = genai.models.generate_content(
        model="gemini-2.5-pro",
        contents=[f"Enhance this prompt for clarity and context:\n\n{prompt}"]
    ).text
    scores, bleu = evaluate(prompt, enhanced)
    print(json.dumps({"enhanced": enhanced, "rouge": scores, "bleu": bleu}, indent=2))

if __name__ == "__main__":
    enhance()
```

**Deliverables**  
- `enhancer.py` & `app.py` (Streamlit/Gradio UI)  
- `requirements.txt`  
- `README.md` with setup, usage, and examples  
