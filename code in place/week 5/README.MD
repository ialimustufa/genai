# Week 5: Multi-Modality with Lovable & Replicate

## Day 1: Lovable.dev Setup & Text-to-Image Generation

1. **Accounts & Access**
   - Sign up for a free account on [Lovable.dev](https://lovable.dev).  
   - Obtain your Lovable **Edit Mode** and **Chat Mode** API keys from the dashboard.

2. **Install SDK**
   ```bash
   pip install lovable-client google-generativeai
   ```

3. **Configure Gemini & Lovable**
   ```python
   import os
   import lovable
   import google.generativeai as genai

   # Configure keys
   lovable.configure(api_key=os.getenv("LOVABLE_API_KEY"))
   genai.configure(api_key=os.getenv("GEMINI_API_KEY"))
   ```

4. **First Text-to-Image Call**
   ```python
   # Using Lovable’s Chat Mode for image generation
   result = lovable.generate(
       prompt="A serene watercolor landscape of mountains at sunrise",
       mode="image"
   )
   with open("landscape.png", "wb") as f:
       f.write(result.data)
   print("Image saved to landscape.png")
   ```

---

## Day 2: Video Generation with Replicate

1. **Sign Up & Access Token**
   - Create a free account at [Replicate](https://replicate.com).  
   - Copy your **Replicate API token**.

2. **Install Replicate SDK**
   ```bash
   pip install replicate
   ```

3. **Basic Video Generation**
   ```python
   import os
   import replicate

   client = replicate.Client(api_token=os.getenv("REPLICATE_API_TOKEN"))
   model = client.models.get("stability-ai/stable-video-diffusion")
   version = model.versions.get("latest")

   output = version.predict(prompt="A short animated clip of a rocket launch")
   video_url = output[0]
   print("Video available at:", video_url)
   ```

4. **Download & Play**
   ```bash
   curl -o launch.mp4 "<VIDEO_URL>"
   ffplay launch.mp4
   ```

---

## Day 3: Audio & Speech Modalities

1. **Text-to-Speech with Geminivox**
   ```python
   from google.generativeai import texttospeech as tts

   tts.configure(api_key=os.getenv("GEMINI_API_KEY"))
   speech = tts.synthesize_speech(
       input_text="Welcome to the future of multimodal AI!",
       voice="en-US-Standard-B",
       audio_format="mp3"
   )
   with open("welcome.mp3", "wb") as f:
       f.write(speech.audio)
   print("Audio saved to welcome.mp3")
   ```

2. **Speech-to-Text via Replicate Whisper**
   ```python
   import replicate

   client = replicate.Client(api_token=os.getenv("REPLICATE_API_TOKEN"))
   model = client.models.get("openai/whisper")
   version = model.versions.get("latest")

   transcript = version.predict(audio=open("welcome.mp3", "rb"))
   print("Transcript:", transcript)
   ```

---

## Day 4: Combined Modalities Workflow

1. **Pipeline Skeleton**
   ```python
   def multimodal_pipeline(text_prompt):
       # 1. Generate image with Lovable
       img = lovable.generate(prompt=text_prompt, mode="image")
       open("output.png", "wb").write(img.data)

       # 2. Generate video with Replicate
       video = replicate.Client(...).predict(prompt=text_prompt)[0]

       # 3. Synthesize speech via Gemini
       speech = tts.synthesize_speech(
           input_text=text_prompt, voice="en-US-Standard-B"
       )
       open("output.mp3", "wb").write(speech.audio)

       return "output.png", video, "output.mp3"
   ```

2. **Test the Pipeline**
   ```python
   image_path, video_url, audio_path = multimodal_pipeline("A futuristic cityscape at dusk")
   print(image_path, video_url, audio_path)
   ```

---

## Day 5: Editing & Fine-Tuning Multimodal Outputs

1. **Lovable Edit Mode**
   ```python
   # Modify the generated image
   edit = lovable.edit(
       image_path="output.png",
       prompt="Add flying cars and neon lights",
       mode="image_edit"
   )
   open("edited.png", "wb").write(edit.data)
   print("Edited image saved to edited.png")
   ```

2. **Replicate Model Parameters**
   ```python
   output = version.predict(
       prompt="A short animated clip of a rocket launch",
       num_inference_steps=50,
       guidance_scale=7.5
   )
   print("Refined video URL:", output[0])
   ```

---

## Day 6: Scalability & Deployment

1. **Containerize the Pipeline**
   ```dockerfile
   # Dockerfile
   FROM python:3.10-slim
   WORKDIR /app
   COPY requirements.txt .
   RUN pip install -r requirements.txt
   COPY . .
   CMD ["python", "app.py"]
   ```

2. **Deploy to Replit or Heroku**
   - For Replit: push repo and set environment secrets.  
   - For Heroku:
     ```bash
     heroku create
     heroku config:set LOVABLE_API_KEY=<key> GEMINI_API_KEY=<key> REPLICATE_API_TOKEN=<token>
     git push heroku main
     ```

---

## Day 7: Mini-Project — Multimodal Story Generator

**Project Description**  
Build an application that:
- Accepts a text prompt (e.g., “A day in the life of a dragon”).  
- Generates an **illustrated storyboard** (sequence of images) via Lovable.  
- Creates a **narrated video clip** combining generated audio (Gemini TTS) and video (Replicate).  
- Presents results in a simple web UI (Flask or Streamlit).

**Deliverables**  
- `storygen.py` (core pipeline)  
- `app.py` and UI templates  
- `requirements.txt`  
- `README.md` with setup, usage, and demo screenshots  

```text
# Run the Story Generator
pip install -r requirements.txt
python storygen.py "A day in the life of a dragon"
streamlit run app.py
```
