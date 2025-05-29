# Week 6: Rapid UI Prototyping with Streamlit, Gradio & Bolt.new

## Day 1: Streamlit Fundamentals

1. **Install Streamlit**  
   ```bash
   pip install streamlit
   ```
2. **Your First App**  
   ```python
   import streamlit as st

   st.title("Hello, Streamlit!")
   st.write("This is your first Streamlit app.")
   ```
3. **Widgets & Layout**  
   ```python
   name = st.text_input("Enter your name:")
   if name:
       st.write(f"Welcome, {name}!")
   st.slider("Select a value", 0, 100, 25)
   ```
4. **Run the App**  
   ```bash
   streamlit run app.py
   ```

---

## Day 2: Gradio Basics

1. **Install Gradio**  
   ```bash
   pip install gradio
   ```
2. **Simple Interface**  
   ```python
   import gradio as gr

   def greet(name):
       return f"Hello, {name}!"

   demo = gr.Interface(fn=greet, inputs="text", outputs="text")
   demo.launch()
   ```
3. **Advanced Components**  
   ```python
   def classify(image):
       # Dummy classifier
       return {"cat": 0.7, "dog": 0.3}

   demo = gr.Interface(fn=classify, inputs="image", outputs="label")
   demo.launch()
   ```

---

## Day 3: Bolt.new Cloud IDE

1. **Getting Started**  
   - Visit [Bolt.new](https://bolt.new) and login.  
   - Create a new Python project.

2. **AI-Powered Code Generation**  
   - Use natural-language prompts in Bolt’s sidebar to generate UI code:
     ```
     "Create a Streamlit app with a chatbox and send button."
     ```
3. **Live Preview & Deployment**  
   - Edit code in-browser and click **Run** for instant preview.  
   - Publish your project with a public URL.

---

## Day 4: Combining Frameworks

1. **Hybrid App**  
   - Scaffold a Streamlit app in Bolt:
     ```python
     import streamlit as st
     import google.generativeai as genai
     import os

     genai.configure(api_key=os.getenv("GEMINI_API_KEY"))

     st.title("Multimodal Chat")
     user_input = st.text_input("You:")
     if user_input:
         response = genai.models.generate_content(
             model="gemini-2.5-pro",
             contents=[user_input]
         )
         st.write(response.text)
     ```
2. **Embed Gradio Component**  
   ```python
   import gradio as gr

   def image_prompt(prompt):
       return genai.models.generate_content(
           model="gemini-2.5-pro",
           contents=[prompt],
           modality="image"
       ).data

   gr_interface = gr.Interface(fn=image_prompt, inputs="text", outputs="image")
   gr_interface.launch(inline=True)
   ```

---

## Day 5: Theming & Customization

1. **Streamlit Themes**  
   - In `.streamlit/config.toml`:
     ```toml
     [theme]
     primaryColor = "#0F4C75"
     backgroundColor = "#BBE1FA"
     font = "sans serif"
     ```
2. **Gradio CSS Overrides**  
   ```python
   demo = gr.Interface(fn=greet, inputs="text", outputs="text",
                       css=".output_class {background-color: #f0f0f0;}")
   demo.launch()
   ```

---

## Day 6: Deployment Strategies

1. **Streamlit Cloud**  
   - Push to GitHub and connect to [Streamlit Cloud](https://streamlit.io/cloud).  
   - Set secrets for `GEMINI_API_KEY`.

2. **Bolt.new Publish**  
   - Click **Publish** in Bolt UI.  
   - Share the generated URL.

3. **Containerization**  
   ```dockerfile
   FROM python:3.10-slim
   WORKDIR /app
   COPY requirements.txt .
   RUN pip install -r requirements.txt
   COPY . .
   CMD ["streamlit", "run", "app.py", "--server.port", "8080"]
   ```

---

## Day 7: Mini-Project — Multimodal UI Demo

**Project**  
Build a **multimodal chat interface** that:
- Takes text input for LLM responses via Gemini.
- Displays images generated from prompts.
- Allows switching between Streamlit and Gradio views.

**Deliverables**  
- `app.py` (Streamlit + inline Gradio)  
- `requirements.txt`  
- **README.md** with setup, usage, and deployment instructions

```bash
pip install -r requirements.txt
streamlit run app.py
```  
