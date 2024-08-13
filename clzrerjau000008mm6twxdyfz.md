---
title: "Crafting a Blazing-Fast Audio Transcription App with Groq's Whisper v3"
seoDescription: "Learn how to create a lightning-fast real-time audio transcription application using Groq's Whisper v3 model, FastAPI, and JavaScript. "
datePublished: Thu Jun 20 2024 04:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clzrerjau000008mm6twxdyfz
slug: crafting-a-blazing-fast-audio-transcription-app-with-groqs-whisper-v3
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/Y20JJ_ddy9M/upload/322bba8de230c8c4befbf88ec0ac57d0.jpeg
tags: javascript, python, html5, speech-to-text, fastapi, whisper, audio-transcription, groq, whisperv3

---

Hey there, fellow developers! 👋 Today, I'm excited to share with you a project I've been working on that's all about pushing the boundaries of real-time audio transcription. Let's dive into the code and see how we can harness the power of Groq's Whisper v3 model to create a blazing-fast transcription service!

## The Backend Magic 🧙‍♂️

Let's start by looking at the heart of our application - the FastAPI backend. Here's where the real magic happens:

```python
import os
import io
import logging
from fastapi import FastAPI, File, UploadFile, Request, WebSocket
from fastapi.responses import HTMLResponse
from fastapi.templating import Jinja2Templates
from groq import AsyncGroq

app = FastAPI()
templates = Jinja2Templates(directory="templates")

GROQ_API_KEY = os.environ.get("GROQ_API_KEY")
groq_client = AsyncGroq(api_key=GROQ_API_KEY)
```

We're using FastAPI for its speed and ease of use, and we're integrating with Groq's AsyncGroq client to tap into their lightning-fast Whisper v3 model.

The star of the show is our `/transcribe` endpoint:

```python
@app.post("/transcribe")
async def transcribe_audio(file: UploadFile = File(...)):
    contents = await file.read()
    audio_file = io.BytesIO(contents)
    audio_file.name = "audio.wav"

    try:
        transcription = await groq_client.audio.transcriptions.create(
            file=audio_file,
            model="whisper-large-v3",
            response_format="verbose_json"
        )
        return {"transcription": transcription.text}
    except Exception as e:
        return {"error": f"Error during transcription: {str(e)}"}
```

This endpoint receives audio chunks, processes them using Groq's Whisper v3 model, and returns the transcription. It's asynchronous, which means it can handle multiple requests efficiently without blocking.

## The Frontend Experience 🎨

On the frontend, we're keeping things simple yet effective with a straightforward HTML/JS setup:

```html
<button id="startButton">Start Recording</button>
<button id="stopButton" disabled>Stop Recording</button>
<div id="transcription"></div>
<div id="status"></div>
```

The real action happens in our JavaScript:

```javascript
startButton.onclick = async () => {
    try {
        const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
        mediaRecorder = new MediaRecorder(stream);
        
        mediaRecorder.ondataavailable = (event) => {
            audioChunks.push(event.data);
            if (audioChunks.length === 5) {  // Send every 5 chunks
                sendAudioChunks();
            }
        };

        mediaRecorder.start(1000);  // Capture in 1-second intervals
        // ... (update UI)
    } catch (error) {
        updateStatus(`Error starting recording: ${error.message}`);
    }
};
```

We're using the MediaRecorder API to capture audio in 1-second chunks. Every 5 chunks, we send the data to our backend for transcription.

## The Secret Sauce: Groq's Whisper v3 🚀

The real power behind this application is Groq's Whisper v3 model. It's incredibly fast and accurate, allowing us to transcribe audio in near real-time. By leveraging Groq's API, we're able to offload the heavy lifting of transcription to their specialized hardware, resulting in lightning-fast results.

## Putting It All Together 🏗️

When a user starts recording, we capture audio chunks and send them to our backend. The backend then uses Groq's Whisper v3 model to transcribe the audio and sends the results back to the frontend, where it's displayed in real-time.

This architecture allows for a smooth, responsive user experience while taking advantage of the incredible speed of Groq's technology.

## Wrapping Up 🎁

And there you have it! A real-time audio transcription service that leverages the power of Groq's Whisper v3 model. This project demonstrates how we can combine modern web technologies with cutting-edge AI to create powerful, user-friendly applications.

Feel free to clone the repo, experiment with it, and let me know what cool features you add! Happy coding, everyone! 🚀👨‍💻👩‍💻

%[https://replit.com/@MartinBowling/YT-Transcriber-with-Groq-Whisper-v3?v=1#templates/index.html]