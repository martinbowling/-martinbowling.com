---
title: "Building Secret Shopper:"
datePublished: Fri Aug 16 2024 20:36:24 GMT+0000 (Coordinated Universal Time)
cuid: clzx66bkc00020amf66bq3pa5
slug: building-secret-shopper
tags: ai, nextjs, ocr, agent, ai-agents, highlight, multion

---

# FriYAY: Meet Secret Shopper

Happy Friday 🎉 I'm excited to share a project I've been working on that combines two powerful tools: Highlight and MultiOn. 🛠️💡But before we dive into the code, I want to take a moment to highlight some amazing opportunities for developers in the Highlight ecosystem.

## Getting Started with Highlight

First off, if you're not already familiar with Highlight, their comprehensive [developer docs](https://docs.highlight.ing) are an absolute goldmine of information. Whether you're just getting started or looking to level up your Highlight skills, these docs are your go-to resource.

For those who love to connect and collaborate, the [Highlight Discord community](https://highlight.ing/discord) is the place to be. It's a vibrant hub where developers share ideas, troubleshoot issues, and showcase their latest creations.

### Highlight Grant Program

Now, here's something that might really pique your interest: Highlight's incredible grant program for developers. They're offering $10,000 per developer, with $5,000 upfront and another $5,000 upon app publication. The best part? No equity is taken! But wait, there's more:

* Free office space in NYC's Upper West Side whenever you need it (perfect for those intense coding sessions or team meetups)
    
* An additional $20,000 follow-on grant available, depending on your app's post-launch traction
    

If you're ready to take your development journey to the next level with Highlight, you can [sign up for the grant program here](https://docs.google.com/forms/d/e/1FAIpQLSc02_q5xyG_VeIQuc0YXfoR2RiVwuBNGkh8WmUTENasUQ3oVQ/viewform).

## Introducing MultiOn: Your AI Agent API

To get started with this project, you'll also need a MultiOn API key. MultiOn offers an Agent API that allows you to build and embed AI agents that can complete tasks and workflows on the web for your users. You can [get your API key here](https://www.multion.ai/api).

### What MultiOn Offers:

* **Versatile Actions**: From ordering on Amazon to sending calendar invites, booking flights, or controlling the AWS console, MultiOn's general-purpose agents can handle a wide range of tasks.
    
* **Cross-Sector Applications**: Whether you're in e-commerce, travel, financial services, or developing smart devices, MultiOn can automate tasks and assist customers.
    
* **Seamless Integration**: MultiOn allows users to complete purchases or bookings without leaving your website, fill complex forms, and aggregate data from various online sources.
    

With these resources and support at your fingertips, the possibilities are endless. And speaking of possibilities, let me show you what I've been cooking up with Highlight and MultiOn...

## The Power of Context-Aware Computing

Before we dive into the code, let's talk about why this project is so cool. We're living in an age where our computers can understand what's on our screens and act on that information in real-time. That's where Highlight comes in – it's a game-changing platform that allows developers to create apps that can "see" and interpret what's on a user's screen.

Pair that with MultiOn, a tool that can interact with web pages programmatically, and you've got a recipe for some seriously powerful applications.

## Introducing Secret Shopper

Secret Shopper is a background shopping tool that works quietly while you browse. It detects when you're looking at a product, then automatically fetches price comparisons from other retailers. Let's break down how it works:

### 1\. Screen Content Analysis

The heart of Secret Shopper is its ability to understand what's on your screen. We use Highlight's OCR capabilities to read the screen content, then pass that to a language model for classification. Here's how it works in detail:

```typescript
export async function classifyProductPage(ocrContent: string): Promise<ProductClassificationResult> {
  try {
    const messages: LLMMessage[] = [
      {
        role: "system",
        content: "You are an AI trained to analyze OCR content and determine if it's from a product page..."
      },
      {
        role: "user",
        content: `Analyze this OCR content: ${ocrContent}`
      }
    ];

    const textPrediction = await Highlight.inference.getTextPrediction(messages);
    let fullResponse = '';
    for await (const chunk of textPrediction) {
      fullResponse += chunk;
    }

    const jsonMatch = fullResponse.match(/\{[\s\S]*\}/);
    
    if (jsonMatch) {
      const jsonString = jsonMatch[0];
      return JSON.parse(jsonString);
    } else {
      throw new Error('Failed to extract JSON from the response');
    }
  } catch (error) {
    console.error('Error classifying the page:', error);
    throw new Error('Failed to classify the page.');
    }
}
```

This function does several important things:

1. It sets up a conversation with the AI model, giving it instructions on how to analyze the OCR content.
    
2. It uses Highlight's `getTextPrediction` method to get a response from the AI.
    
3. It processes the response, extracting the JSON part that contains the classification results.
    

We also have an alternative implementation using the Groq API, which shows how you can easily swap out different AI providers:

```typescript
export async function classifyProductPageGroq(ocrContent: string): Promise<ProductClassificationResult> {
  try {
    const chatCompletion = await groq.chat.completions.create({
      messages: [
        {
          role: "system",
          content: "You are an AI trained to analyze OCR content..."
        },
        {
          role: "user",
          content: `Analyze this OCR content: ${ocrContent}`
        }
      ],
      model: "llama-3.1-70b-versatile",
      temperature: 0.5,
      max_tokens: 1024,
      top_p: 1,
      stream: false,
      stop: null
    });

    // ... process and return the result
  } catch (error) {
    console.error('Error classifying the page:', error);
    throw new Error('Failed to classify the page.');
  }
}
```

### 2\. Fetching Price Comparisons

Once we know we're looking at a product, it's time to fetch some price comparisons. This is where MultiOn comes in:

```typescript
export async function retrieveProductListings(product: string, multionKey: string): Promise<ProductListing[]> {
  const multion = new MultiOnClient({ apiKey: multionKey });

  try {
    const response = await multion.retrieve({
      cmd: `Get all non-sponsored listings on Google for ${product} with the price, description, and URL...`,
      url: `https://www.google.com/search?q=${encodeURIComponent(product)}`,
      fields: ["price", "description", "url"]
    });

    // ... process and return the results
  } catch (error) {
    console.error("Error retrieving product listings:", error);
    throw new Error("Failed to retrieve product listings.");
  }
}
```

This function uses MultiOn to search for our product and return a list of prices and retailers. It's like having a little web-scraping genie at your command! 🧞‍♂️

### 3\. Managing State with React Context

We use React's Context API to manage the state of our application. Here's a snippet from our `ProductContext`:

```typescript
export const ProductProvider: React.FC<{ children: React.ReactNode }> = ({ children }) => {
  const [productGroups, setProductGroups] = useState<ProductGroup[]>([]);
  const [checkInterval, setCheckInterval] = useState(60);
  const [multionKey, setMultionKey] = useState('');
  // ... other state variables

  const addProductGroup = useCallback((name: string, products: ProductListing[]) => {
    setProductGroups((prevGroups) => {
      const existingGroup = prevGroups.find((group) => group.name === name);
      if (existingGroup) {
        // Update existing group
      } else {
        // Add new group
      }
    });
  }, []);

  // ... other methods

  const fetchProductUpdates = useCallback(async () => {
    try {
      const context = await Highlight.user.getContext();
      const ocrContent = context.environment?.ocrScreenContents || '';

      if (ocrContent) {
        const classification = await classifyProductPage(ocrContent);

        if (classification.isProductPage || classification.isProductDiscussion || classification.isProductMention) {
          const productName = classification.productName;
          
          if (productName) {
            // Fetch and store product listings
            const listings = await retrieveProductListings(productName, multionKey);
            addProductGroup(productName, listings);
          }
        }
      }
    } catch (error) {
      console.error('Error fetching product updates:', error);
    }
  }, [productGroups, addProductGroup, multionKey, backgroundPermissionGranted]);

  // ... context value and provider
};
```

This context manages the state of our application, including the list of product groups, settings, and the main `fetchProductUpdates` function that ties everything together.

### 4\. The User Interface

Our main application component, `SecretShopperApp`, uses this context to render the UI and manage the background processing:

```typescript
const SecretShopperApp: React.FC = () => {
  const { 
    productGroups, 
    removeProductGroup, 
    fetchProductUpdates, 
    checkInterval,
    showOnboarding,
    setShowOnboarding
  } = useContext(ProductContext);

  const [isPaused, setIsPaused] = useState(false);

  useEffect(() => {
    let intervalId: NodeJS.Timeout | null = null;

    if (!isPaused) {
      intervalId = setInterval(() => {
        fetchProductUpdates();
      }, checkInterval * 1000);
    }

    return () => {
      if (intervalId) clearInterval(intervalId);
    };
  }, [fetchProductUpdates, checkInterval, isPaused]);

  // ... render UI
};
```

This component sets up an interval to fetch product updates regularly, but only when the app isn't paused. It's a simple way to give users control while still leveraging the power of background processing.

## The Power of Background Processing

One of the coolest things about Secret Shopper is that it runs in the background, thanks to Highlight's background permission feature. This is managed in our `OnboardingScreen` component:

```typescript
const requestBackgroundPermission = useCallback(async () => {
  try {
    const granted = await Highlight.permissions.requestBackgroundPermission();
    if (granted) {
      await Highlight.app.setBackgroundStatus(true);
      setBackgroundPermissionGranted(true);
      await saveToAppStorage("backgroundPermissionGranted", "true");
      setError(null);
    } else {
      setError("Background permission was not granted. This app requires background access to function properly.");
    }
    return granted;
  } catch (error) {
    console.error('Error requesting background permission:', error);
    setError("An error occurred while requesting background permission. Please try again.");
    return false;
  }
}, []);
```

This function requests background permission from the user and sets up the app to run in the background if granted.

## Wrapping Up

Secret Shopper demonstrates the power of context-aware computing. By leveraging Highlight's screen analysis capabilities and MultiOn's web interaction abilities, we've created an app that understands what we're looking at and acts on that information automatically.

The possibilities for this kind of technology are endless. Imagine an app that automatically takes notes in your meetings, or one that suggests relevant documents based on what you're working on. The future of computing is context-aware, and tools like Highlight and MultiOn are making it possible for developers to build these kinds of intelligent, responsive applications.

That's all for now, folks! Feel free to dive [into the code](https://replit.com/@MartinBowling/Secret-Shopper-Highlight-and-MultiOn-Power-Shopping-Agent?v=1), experiment with it, and see what you can build. Happy coding! 👨‍💻🚀

%[https://replit.com/@MartinBowling/Secret-Shopper-Highlight-and-MultiOn-Power-Shopping-Agent?v=1]