# Week 1: Rapid Prototyping with Replit Agent & Gemini API

## Day 1: Environment Setup

1. **Accounts & Access**
   - Sign up for a free [Replit](https://replit.com) account.
   - Sign up for Google AI Studio at <https://ai.google.dev> and verify your email.

2. **Install SDKs**
   ```bash
   pip install -U google-genai
   ```

3. **Create a Replit “Python” Repl**
   - In Replit, click **+ Create** → **Python**.
   - Under **Version control**, link your GitHub account.

4. **Configure Environment Variables**
   - In Replit’s **Secrets** (🔒) tab, add:
     ```
     GEMINI_API_KEY=<your_key>
     ```

---

## Day 2: Exploring Replit Agent

1. **Read the Docs**
   - Review the [Replit Agent Quickstart](https://docs.replit.com/replitai/agent).

2. **Generate a Flask App**
   - Open the Agent pane in your Repl.
   - Enter the prompt:
     ```
     Create a Flask app with one endpoint `/hello` that returns "Hello, AI!"
     ```
   - Inspect the generated files (`main.py`, `requirements.txt`).

3. **Run & Verify**
   ```bash
   python main.py
   ```
   - Visit the provided URL to confirm **“Hello, AI!”** is returned.

---

## Day 3: Agent Integrations Deep Dive

1. **Add a React Front-End**
   - Prompt Agent:
     ```
     Add a React front-end served at `/app` that has an input box and button.
     ```
   - Examine the new `frontend/` folder.

2. **Generate a Dockerfile**
   - Prompt:
     ```
     Include a Dockerfile to containerize the Flask + React app.
     ```
   - Check for:
     ```dockerfile
     EXPOSE 8080
     CMD ["bash", "start.sh"]
     ```

3. **Manual Tweaks**
   - Fix any import or path issues introduced by the generated code.

---

## Day 4: Gemini API Basics

1. **Obtain Your API Key**
   - In Google AI Studio → **Credentials** → **Create key** → **API key**.

2. **First Text-Generation Call**
   Create `gemini_text.py`:
   ```python
   from google import genai

   client = genai.Client(api_key="YOUR_KEY")
   chat   = client.chats.create(model="gemini-2.0-flash")
   resp   = chat.send_message("Hello, Gemini!")
   print(resp.text)
   ```

3. **Add Error Handling**
   ```python
   try:
       resp = chat.send_message("Test prompt")
       print(resp.text)
   except genai.exceptions.AuthenticationError as e:
       print("Auth failed:", e)
   except genai.exceptions.RateLimitError as e:
       print("Rate limited:", e)
   ```

---

## Day 5: Multimodal Gemini Calls

1. **Image Generation**
   Extend to `gemini_multi.py`:
   ```python
   from google import genai

   client = genai.Client(api_key="YOUR_KEY")
   img_resp = client.generateContent(
       model="gemini-alpha-1.0",
       prompt="A neon-lit cyberpunk cityscape",
       modality="image"
   )
   with open("city.png", "wb") as f:
       f.write(img_resp.data)
   ```

2. **Display the Image**
   ```python
   from IPython.display import Image, display
   display(Image("city.png"))
   ```

---

## Day 6: Integrating Agent & Gemini

1. **Scaffold a CLI with Click**
   Prompt Agent:
   ```
   Create a `cli.py` using Click that has commands `ask` and `image`.
   ```
   Verify skeleton:
   ```python
   import click

   @click.group()
   def cli():
       pass

   @cli.command()
   @click.argument("prompt")
   def ask(prompt):
       # TODO: call Gemini text API
       pass

   @cli.command()
   @click.argument("prompt")
   def image(prompt):
       # TODO: call Gemini image API
       pass

   if __name__ == "__main__":
       cli()
   ```

2. **Embed Your Gemini Calls**
   - Copy code from `gemini_text.py` and `gemini_multi.py` into `ask` and `image` commands.

3. **Bridge to Flask**
   Create `app.py`:
   ```python
   from flask import Flask, request, jsonify
   from cli import ask, image

   app = Flask(__name__)

   @app.route("/ask")
   def web_ask():
       q = request.args.get("q")
       answer = ask.callback(q)
       return jsonify(answer=answer)

   @app.route("/image")
   def web_image():
       q = request.args.get("q")
       path = image.callback(q)
       return app.send_static_file(path)

   if __name__ == "__main__":
       app.run(host="0.0.0.0", port=8080)
   ```

---

## Day 7: Mini-Project — “Gemini CLI”

### Deliverables
- Public Repl with:
  - `cli.py`
  - `app.py`
  - `requirements.txt`
  - `static/` assets

- **README.md** including:
  - **Setup**:
    ```bash
    pip install -r requirements.txt
    ```
  - **Env var**:
    ```
    GEMINI_API_KEY=<your_key>
    ```
  - **Usage**:
    ```bash
    python cli.py ask "Tell me a joke"
    python cli.py image "Sunset over mountains"
    ```
  - **Run server**:
    ```bash
    python app.py
    ```
