# Week 3: Building LLM Applications with LangChain & Gemini

## Day 1: LangChain Setup & Gemini Integration

1. **Install Dependencies**  
   ```bash
   pip install langchain google-genai
   ```  
2. **Configure Gemini in LangChain**  
   ```python
   import os
   import google.generativeai as genai
   from langchain.llms import VertexAI

   # Configure Gemini API key
   genai.configure(api_key=os.getenv("GEMINI_API_KEY"))  # citeturn0search0

   # Initialize LangChain LLM using Gemini
   llm = VertexAI(model_name="gemini-2.5-pro")              # citeturn0search0
   print(llm("Hello, LangChain with Gemini!"))             # citeturn0search1
   ```

---

## Day 2: Simple LLMChain Application

1. **Create a PromptTemplate & LLMChain**  
   ```python
   from langchain import LLMChain, PromptTemplate
   from langchain.llms import VertexAI
   import google.generativeai as genai, os

   genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
   llm = VertexAI(model_name="gemini-2.5-pro")              # citeturn0search1

   template = "Translate to {language}: {text}"
   prompt = PromptTemplate(input_variables=["language","text"], template=template)
   chain = LLMChain(llm=llm, prompt=prompt)

   result = chain.run(language="French", text="Good morning!")
   print(result)                                           # citeturn0search1
   ```

---

## Day 3: Conversational Memory

1. **Set up ConversationChain**  
   ```python
   from langchain.chains import ConversationChain
   from langchain.memory import ConversationBufferMemory
   from langchain.llms import VertexAI
   import google.generativeai as genai, os

   genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
   llm = VertexAI(model_name="gemini-2.5-pro")              # citeturn0search2

   memory = ConversationBufferMemory()
   conversation = ConversationChain(llm=llm, memory=memory)

   print(conversation.predict(input="Hello!"))             # citeturn0search2
   print(conversation.predict(input="How are you?"))       # citeturn0search2
   ```

---

## Day 4: Building Agents with Tools

1. **Load Built-In Tools & Initialize Agent**  
   ```python
   from langchain.agents import load_tools, initialize_agent
   from langchain.llms import VertexAI
   import google.generativeai as genai, os

   genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
   llm = VertexAI(model_name="gemini-2.5-pro")              # citeturn0search3

   tools = load_tools(["serpapi"], llm=llm, serpapi_api_key=os.getenv("SERPAPI_API_KEY"))
   agent = initialize_agent(tools, llm, agent="zero-shot-react-description", verbose=True)
   result = agent.run("Who is the president of France?")   # citeturn0search3
   print(result)
   ```

---

## Day 5: Custom Tools & Agents

1. **Define a Custom Tool**  
   ```python
   from langchain.tools import BaseTool
   import requests

   class JokeTool(BaseTool):
       name = "joke_tool"
       description = "Returns a random joke"
       def _run(self, query: str):
           r = requests.get("https://official-joke-api.appspot.com/random_joke")
           return r.json()["setup"] + " — " + r.json()["punchline"]
       def _arun(self, query: str):
           raise NotImplementedError

   # citeturn0search7
   ```

2. **Use Custom Tool in Agent**  
   ```python
   from langchain.agents import initialize_agent, AgentType

   tools = [JokeTool()]
   agent = initialize_agent(tools, llm, agent=AgentType.OPENAI_FUNCTIONS, verbose=True)
   print(agent.run("Tell me a joke"))                       # citeturn0search7
   ```

---

## Day 6: Retrieval-Augmented Generation (RAG)

1. **Index Documents & Query**  
   ```python
   from llama_index import GPTVectorStoreIndex, SimpleDirectoryReader
   import google.generativeai as genai
   from langchain.llms import VertexAI
   import os

   # Configure Gemini
   genai.configure(api_key=os.getenv("GEMINI_API_KEY"))

   # Build RAG index
   docs = SimpleDirectoryReader("docs/").load_data()
   index = GPTVectorStoreIndex.from_documents(docs)        # citeturn0search4
   query_engine = index.as_query_engine()

   context = query_engine.query("Summarize the Q1 sales report.")
   response = genai.models.generate_content(
       model="gemini-2.5-pro",
       contents=[f"Context:\n{context}\n\nSummarize:"]
   )                                                       # citeturn0search4
   print(response.text)
   ```

---

## Day 7: Mini-Project — LangChain-Powered Chatbot

### Project Description  
Build a **Streamlit** chatbot that:
- Maintains **memory** across messages  
- Uses **LangChain chains** and **VertexAI Gemini**  
- Optionally integrates the **JokeTool** for fun  

### `app.py` Skeleton  
```python
import streamlit as st
from langchain.chains import ConversationChain
from langchain.memory import ConversationBufferMemory
from langchain.llms import VertexAI
import google.generativeai as genai, os

genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
llm = VertexAI(model_name="gemini-2.5-pro")

memory = ConversationBufferMemory()
chat = ConversationChain(llm=llm, memory=memory)

st.title("LangChain Gemini Chatbot")
if 'history' not in st.session_state:
    st.session_state.history = []

user_input = st.text_input("You:")
if user_input:
    reply = chat.predict(input=user_input)
    st.session_state.history.append((user_input, reply))

for user, bot in st.session_state.history:
    st.markdown(f"**You:** {user}")
    st.markdown(f"**Bot:** {bot}")
```

### Deliverables  
- `app.py`, `requirements.txt`  
- **README.md** with setup & usage  
- Optional: integrate **JokeTool** for additional agent functionality  

---

```text
# Install & Run
pip install -r requirements.txt
streamlit run app.py
```
