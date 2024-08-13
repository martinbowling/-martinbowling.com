---
title: "Building an AI-Enhanced Chatbot with FastAPI and Groq"
seoTitle: "Advanced AI Chatbot: FastAPI, Groq, and Message Prediction"
seoDescription: "Discover how to create an intelligent chatbot using FastAPI and Groq. Learn about message completion prediction, similarity detection,& efficient response"
datePublished: Sun Mar 17 2024 04:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clzrcoi1l000609l848ee6d3j
slug: building-an-ai-enhanced-chatbot-with-fastapi-and-groq
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/4XlvusRVTU0/upload/5d4f520065fdf0339dbf7fd3bffcb205.jpeg
tags: ai, python, chatbot, llm, groq

---

I was tinkering with a fun idea - an AI-enhanced chatbot system that leverages the power of FastAPI and Groq's lightning-fast inference capabilities. This chatbot is designed to provide a more natural and efficient conversation experience by incorporating several advanced features. Let me walk you through the key components and concepts!

1. FastAPI Framework
    

We're using FastAPI as our web framework. It's fast, easy to use, and perfect for building APIs. Here's how we set up our basic app:

```python
from fastapi import FastAPI
import uvicorn

app = FastAPI()

if __name__ == "__main__":
    uvicorn.run(app, port=8080, host="0.0.0.0")
```

2. Message Completion Prediction
    

One of the coolest features of this chatbot is its ability to predict message completions. We use Groq's API to assess the likelihood that a user's message is complete:

```python
response = await client.chat.completions.create(
    model=MODEL_TO_USE,
    messages=[{
        "role": "system",
        "content": "It is extremely important that you are brief and that you only respond with a number between 0-100 to show likelihood that the user message is complete and can be responded to..."
    }, {
        "role": "user",
        "content": f"{text}"
    }],
)
```

If the likelihood is below 90%, we generate a predicted ending for the message.

3. Similarity Detection
    

To avoid redundant processing, we implement a similarity check using embeddings:

```python
async def check_if_same(combined_message):
    global last_message_embedding, last_response
    embedding_response = await embed_client.embeddings.create(
        model=EMBEDDING_MODEL_TO_USE, input=combined_message)
    current_embedding = np.array(embedding_response.data[0].embedding)
    
    if last_message_embedding is not None:
        similarity = 1 - cosine(last_message_embedding, current_embedding)
        if similarity > 0.9:
            return True, similarity, last_response
    
    last_message_embedding = current_embedding
    return False, 0, None
```

This function helps us identify if the current message is very similar to the previous one, allowing us to reuse the last response if appropriate.

4. Response Generation
    

If the message is unique or complete, we generate a new response using Groq's API:

```python
response = await client.chat.completions.create(
    model=MODEL_TO_USE,
    messages=[{
        "role": "system",
        "content": "Generate a response to the user message"
    }, {
        "role": "user",
        "content": combined_message
    }],
)
```

5. User Interface
    

We've created a simple yet effective UI using HTML, Bootstrap, and jQuery. The interface allows users to input their messages and displays the AI's responses in real-time.

```html
<div class="container text-center">
    <h2>Enter Your Message</h2>
    <div class="d-flex justify-content-center">
        <input type="text" class="inputBox" id="userInput">
        <div class="spinner-border text-primary d-none" id="loadingSpinner" role="status">
            <span class="sr-only">Loading...</span>
        </div>
    </div>
    <div class="response-area" id="responseArea"></div>
</div>
```

The JavaScript code handles user input and updates the UI with the AI's responses:

```javascript
$('#userInput').on('keyup', function(event) {
    if (event.key === ' ' || event.key === '.' || event.key === '?' || event.key === '!') {
        // Send request to server
        // ...
    }
});
```

This project demonstrates how we can combine multiple AI models and fast inference engines like Groq to create a more intelligent and responsive chatbot. The system's ability to predict message completions, detect similarities, and generate context-aware responses makes for a much more natural conversation flow.

I'm constantly working on improving this system, and I'd love to hear your thoughts or suggestions. Feel free to try it out and let me know what you think!

%[https://replit.com/@MartinBowling/GroqPredictiveChat?v=1#index.html]