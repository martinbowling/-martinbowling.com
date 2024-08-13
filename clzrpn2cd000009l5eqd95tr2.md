---
title: "Building a Safe and Fun AI Chat Experience with Llama Guard 3 🦙🛡️"
seoTitle: "SafeChat: Moderated AI Conversations | Llama Guard 3 & Groq API"
seoDescription: "Experience safe and intelligent AI chat with SafeChat. Powered by Llama Guard 3 and Groq API, Safe Chat app ensures moderated, real-time conversations. "
datePublished: Tue Aug 13 2024 00:54:41 GMT+0000 (Coordinated Universal Time)
cuid: clzrpn2cd000009l5eqd95tr2
slug: building-a-safe-and-fun-ai-chat-experience-with-llama-guard-3
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/QOL_IwPmj6E/upload/27fa13119b57e7159da694b1dd61980b.jpeg
tags: ai, python, replit, gradio, llm, llama, ai-safety, groq, llama3

---

Hey there, tech enthusiasts and curious minds! 👋 Today, I'm excited to share a project I've been working on that combines the power of AI with the importance of online safety. Let's dive into how we can create a moderated AI chat experience using Llama Guard 3 and the Groq API. 🚀

## Why Content Moderation Matters 🤔

In the world of AI and large language models, it's crucial to ensure that our digital conversations remain respectful and safe. That's where content moderation comes in. It helps us filter out potentially harmful or inappropriate content, making sure our AI assistants behave responsibly. Think of it as a friendly bouncer for your chat app! 🕴️

## Enter Llama Guard 3 🦙

Llama Guard 3 is our superhero in this story. It's a powerful 8B parameter model designed to classify content in both AI inputs and outputs. It's like having a wise old llama watching over our conversations, making sure everything stays friendly and appropriate. 🦸‍♂️

## Let's Build Our Safe Chat App! 🛠️

I've put together a fun little project using Python and the Gradio library to create a web-based chat interface. Here's a sneak peek at how we set things up:

```python
import os
import asyncio
import gradio as gr
from groq import AsyncGroq

client = AsyncGroq(api_key=os.environ.get("GROQ_API_KEY"))

chat_model = "llama3-70b-8192"
moderation_model = "llama-guard-3-8b"

system_prompt = """You are a helpful, respectful, and honest assistant. Always strive to answer the user's questions to the best of your ability, while maintaining a polite and professional tone."""
```

This sets up our connection to the Groq API and defines which models we'll use for chat and moderation. The system prompt gives our AI assistant its personality – think of it as its morning pep talk! ☀️

## The Magic of Moderation ✨

Here's where Llama Guard 3 really shines. We use it to check each message before our main AI responds:

```python
async def moderate_message(message):
    """Moderate the user message using Llama Guard 3."""
    response = await client.chat.completions.create(
        model=moderation_model,
        messages=[{"role": "user", "content": message}],
        temperature=0.1,
        max_tokens=10,
    )
    return response.choices[0].message.content.strip().lower() == "safe"
```

If Llama Guard 3 gives us the thumbs up, we proceed with generating a response. If not, we politely decline to engage with the unsafe content. It's like having a wise friend who knows when to change the subject! 😉

## Streaming Responses for a Smooth Experience 🌊

To make our chat feel more natural and responsive, we stream the AI's responses:

```python
async def chat_response(message, history):
    """Generate a chat response, including moderation."""
    is_safe = await moderate_message(message)
    
    if not is_safe:
        yield "I apologize, but I can't respond to that type of message. Let's keep our conversation respectful and appropriate."
        return

    # ... (code for generating and streaming the response)
```

This way, the response appears gradually, just like a real person typing. It's much more engaging than waiting for the whole message to pop up at once!

## A Pretty Interface with Gradio 🎨

Gradio makes it super easy to create a good-looking web interface for our chat app. Here's a snippet of how we set it up:

```python
with gr.Blocks(theme=gr.themes.Soft(), fill_height=True, head=js) as demo:
    gr.Markdown("# SafeChat: Moderated AI Conversation")
    gr.Markdown("This chat interface uses Llama Guard 3 to moderate messages and ensure safe interactions.")
    
    chatbot = gr.Chatbot(height=450, show_label=False)
    message = gr.Textbox(
        placeholder="Type your message here...",
        label="User Input",
        show_label=False,
        container=False
    )
    submit = gr.Button("Send", variant="primary")
    clear = gr.Button("Clear")

    # ... (code for handling user input and bot responses)
```

This creates a clean, user-friendly interface with a chat window, input box, and buttons for sending messages and clearing the chat.

## Try It Out! 🚀

I've made this project a template on Replit, so you can give it a spin right now! Just head over to [Replit](https://replit.com/@MartinBowling/SafeChat-with-Llama-Guard-3?v=1) and start chatting. Feel free to ask about quantum physics, plan a trip to Japan, or discuss the finer points of machine learning algorithms. Our AI assistant is ready for a fun and safe conversation!

## Wrapping Up 🎁

Building this safe chat experience has been a blast, and I hope you enjoy using it as much as I enjoyed creating it. It's amazing to see how we can combine cutting-edge AI technology with responsible safeguards to create something both powerful and trustworthy.

Remember, in the world of AI, with great power comes great responsibility. By using tools like Llama Guard 3, we can ensure that our AI interactions remain positive, informative, and safe for everyone.

Happy chatting, and may your conversations be ever insightful and respectful! 🌟💬

%[https://replit.com/@MartinBowling/SafeChat-with-Llama-Guard-3?v=1]