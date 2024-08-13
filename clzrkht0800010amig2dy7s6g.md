---
title: "Building a Fact-Checking AI Chat Interface"
seoTitle: "Create a Fact-Checked LLM Chat Interface with Python"
seoDescription: "Create an AI chat interface that fact-checks responses in real-time using Anthropic's Claude 3.5 Sonnet and Brave Search API. Try it now!"
datePublished: Fri Jun 21 2024 04:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clzrkht0800010amig2dy7s6g
slug: building-a-fact-checking-ai-chat-interface
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/gcsNOsPEXfs/upload/90f466b48255c9eb426b1591145d5ad8.png
tags: python, chatbot, llm, claudeai, claude-35, factcheck

---

Hey there, fellow developers 👋, coming at you with a super cool project I whipped up inspired by [Andrej Karpathy's tweet about LLM interfaces and proof](https://x.com/karpathy/status/1804187473167421798). Let's dive into this fun little creation that combines the power of AI with fact-checking capabilities! 🚀

## The Inspiration 💡

So, Karpathy tweeted about wanting a built-in feature in LLM interfaces that provides proof for the information they give. You know how it is - we often find ourselves manually verifying things like command switches or API details. Well, that got my gears turning, and I thought, "Why not create something that does this automatically?" 🤔

## The Solution: Fact-Checked LLM Chat Interface 🧠🔍

I threw together a quick and dirty (but oh-so-cool) Fact-Checked LLM Chat interface. Here's how it works:

1. We use Anthropic's Claude 3.5 Sonnet to generate responses.
    
2. The response is streamed back to the user in real-time.
    
3. We then use Brave Search to find relevant sources for fact-checking.
    
4. The content from these sources is fed back to Claude to verify its original claims.
    
5. Finally, we present the user with the fact-check results and the source URL.
    

Let's break down some of the key components of this code, shall we? 🛠️

### Setting Up the Environment

First things first, we import all the necessary libraries and set up our API keys:

```python
import os
import asyncio
import httpx
import gradio as gr
from anthropic import AsyncAnthropic
import json
import re

async_client = AsyncAnthropic(api_key=os.environ["ANTHROPIC_API_KEY"])
BRAVE_API_KEY = os.environ.get("BRAVE_API_KEY")
CLAUDE_MODEL = "claude-3-5-sonnet-20240620"
```

### Fetching Search Results

We use Brave Search API to find relevant sources:

```python
async def fetch_brave_search_results(query: str, count: int = 5) -> dict:
    url = "https://api.search.brave.com/res/v1/web/search"
    headers = {
        "Accept": "application/json",
        "Accept-Encoding": "gzip",
        "X-Subscription-Token": BRAVE_API_KEY
    }
    params = {
        "q": query,
        "count": count,
        "country": "us",
        "search_lang": "en",
    }

    async with httpx.AsyncClient() as client:
        response = await client.get(url, headers=headers, params=params)
        response.raise_for_status()
        return response.json()
```

### Streaming Claude's Responses

We stream Claude's responses for a snappy user experience:

```python
async def ask_claude_streaming(message):
    completion = async_client.messages.stream(
        max_tokens=4096,
        messages=[{
            "role": "user",
            "content": message
        }],
        model=CLAUDE_MODEL,
    )
    async with completion as stream:
        async for text in stream.text_stream:
            yield text
```

### Fact-Checking Magic ✨

The real magic happens in the `fact_check` function. We fetch content from the search results, have Claude analyze it, and return the fact-check results:

```python
async def fact_check(llm_response: str, query: str, search_results: dict,
                     first_page_content: str) -> str:
    # ... (check the full code for details)
    fact_check_prompt = f"""
    Given the following claim and markdown content from a webpage, determine if the claim is supported by the content. 
    Respond with a JSON object containing two fields: "claim_supported" (boolean) and "supporting_facts" (string).
    Ensure the "supporting_facts" field is a single line string with no line breaks.

    Claim: {llm_response}

    Query: {query}

    Markdown Content:
    {markdown_content}

    JSON Response:
    """

    fact_check_result = ''.join(
        [chunk async for chunk in ask_claude_streaming(fact_check_prompt)])
    # ... (process the result)
```

### Putting It All Together

The `chat_with_fact_check` function orchestrates the whole process:

```python
async def chat_with_fact_check(message, history):
    # ... (see full code for details)
    llm_response = ""
    async for chunk in ask_claude_streaming(json.dumps(messages)):
        llm_response += chunk
        yield history + [[message, llm_response]]

    # Fact-checking after the full response
    fact_check_result = await fact_check(llm_response, message, search_results,
                                         first_page_content)

    yield history + [[message, llm_response],
                     ["Fact Check", fact_check_result]]
```

## The Result 🎉

What we end up with is a chat interface that not only provides AI-generated responses but also fact-checks them on the fly! It's like having a super-smart assistant with a built-in fact-checker. How cool is that? 😎

## Try It Yourself!

I've made a Replit template so you can fork, build, and improve upon this project. Feel free to play around with it and see what awesome features you can add!

Remember, this is just a quick demo, but the possibilities are endless. You could extend this to support more sources, improve the fact-checking algorithm, or even add visual elements to make the proof more engaging.

So, what do you think? Ready to take your LLM interactions to the next level with some automatic fact-checking? Let me know what cool modifications you come up with!

Happy coding, and may your AI assistants always be truthful! 🤖✅

%[https://replit.com/@MartinBowling/Karpathy-Fact-Check-LLM-Chat-with-Sonnet-35?v=1#main.py]