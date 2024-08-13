---
title: "Building Infinite Fourth Trivia"
seoTitle: "Infinite Fourth Trivia: Building an AI-Powered Patriotic Quiz Game"
seoDescription: "Discover how I created Infinite Fourth Trivia, an endless AI-powered quiz game for July 4th using Groq's API and Google's Gemma 2 model."
datePublished: Thu Jul 04 2024 04:00:00 GMT+0000 (Coordinated Universal Time)
cuid: clzr7s5sw000c09l89nhy3ijo
slug: building-infinite-fourth-trivia
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/1i8xRkE8gXo/upload/1c1fb817be8e25f2c7189d491cbfb746.jpeg
tags: ai, python, python-development, fastapi, aidemos, groq, gemma-ai, google-gemma, gemma2

---

Hey there, fellow developers and trivia enthusiasts! Martin Bowling here, and I'm excited to share the story behind my latest project: Infinite Fourth Trivia. As we celebrate Independence Day, I wanted to create something that combines my love for technology, American history, and good old-fashioned fun. Let me take you through the journey of building this never-ending patriotic quiz game!

## The Spark of Inspiration

It all started when I was thinking about how to make this year's Fourth of July celebration a bit more special. As an indie developer always looking to push the boundaries, I thought, "What if we could have an AI-powered trivia game that never runs out of questions?" And thus, Infinite Fourth Trivia was born!

## Leveraging Cutting-Edge AI

The heart of this project lies in its ability to generate an endless stream of USA-themed questions. To make this possible, I turned to two incredible technologies:

1. **Groq's Lightning-Fast Inference**: Groq's API allowed me to generate questions at breakneck speeds, ensuring a smooth user experience.
    
2. **Google's Gemma 2 9b Model**: This powerful language model provides the brains behind our questions, ensuring they're diverse, accurate, and engaging.
    

Here's a snippet of how I set up the Groq client in my FastAPI backend:

```python
from groq import AsyncGroq
import os

client = AsyncGroq(api_key=os.environ.get("GROQ_API_KEY"))
GROQ_MODEL = "gemma2-9b-it"
```

## Crafting the Perfect Question

One of the most crucial parts of the project was designing the prompt for our AI model. I wanted to ensure that questions were not only factual but also engaging and appropriate for a Fourth of July celebration. Here's a simplified version of the prompt I used:

```python
trivia_prompt = f"""Generate a Fourth of July themed trivia multiple-choice question. The question should be {difficulty} difficulty, focusing on US independence, American Revolution, patriotic symbols, or Fourth of July traditions.

Format the response as a JSON object with the following structure:
{{
    "question": "The full text of the question",
    "options": ["A. First option", "B. Second option", "C. Third option", "D. Fourth option"],
    "correct_answer": "The letter of the correct answer (A, B, C, or D)",
    "explanation": "A brief explanation of the correct answer"
}}

Ensure that the question is factual, engaging, and appropriate for a Fourth of July celebration. Aim for a mix of historical facts, cultural traditions, and interesting tidbits about American independence and patriotism.

IMPORTANT: Do not repeat any of the following recent questions:
{recent_questions_str}

Reply only with the JSON object, no additional comments or explanations.
"""
```

## Building a Responsive Frontend

For the frontend, I wanted something that was both visually appealing and easy to use. I opted for a combination of HTML, CSS (with Tailwind for quick styling), and JavaScript. Here's a small taste of the JavaScript that powers the game logic:

```javascript
async function fetchAndDisplayQuestion(difficulty) {
    console.log('Fetching and displaying question with difficulty:', difficulty);
    try {
        const response = await axios.post('/fourth_trivia', { difficulty }, {
            headers: {
                'Content-Type': 'application/json'
            }
        });
        console.log('Response received:', response.data);
        if (response.data.error) {
            console.error('Error from server:', response.data.error);
            displayError("Sorry, there was an error fetching the question. Please try again.");
        } else {
            displayQuestion(response.data);
            prefetchNextQuestion(difficulty);  // Prefetch the next question after displaying current one
        }
    } catch (error) {
        console.error('Error fetching question:', error);
        displayError("Sorry, there was an error connecting to the server. Please check your internet connection and try again.");
    }
}
```

## Challenges and Learnings

Building Infinite Fourth Trivia wasn't without its challenges. Ensuring question uniqueness, handling various edge cases, and optimizing for performance were all hurdles I had to overcome. But with each challenge came a learning opportunity, and I'm excited to apply these lessons to future projects.

## The Road Ahead

This is just the beginning for Infinite Fourth Trivia. I have plans to expand the game with more features, such as multiplayer modes, themed question sets, and perhaps even voice-activated gameplay. The possibilities are as endless as the questions themselves!

## Try It Out!

I'm thrilled to share Infinite Fourth Trivia with all of you. Whether you're a history buff, a casual trivia fan, or just looking for a fun way to celebrate the Fourth, I hope you'll give it a try. You can find the game at [https://infinite4thtrivia.replit.app](https://infinite4thtrivia.replit.app), and the source code is available on Replit.

%[https://replit.com/@MartinBowling/Infinite-Fourth-Trivia-powered-By-Groq?v=1] 

Happy Fourth of July, and happy trivia-ing!