# Microsoft Foundry – Work with Images and Video

In this exercise, you will learn how to use Microsoft Foundry to work with **three types of AI capabilities**:

1. 🖼️ **Understand images** – AI looks at an image and explains what it sees.
2. 🎨 **Generate images** – AI creates a new image from a text prompt.
3. 🎬 **Generate videos** – AI creates a video from a text prompt.

The exercise uses a Microsoft Foundry project and models such as **GPT-5-mini**, **gpt-image-1-mini**, and **Sora-2**.

---

# Part 1: Create a Microsoft Foundry Project

## Step 1: Open Microsoft Foundry

Open:

[Microsoft Foundry](https://ai.azure.com?utm_source=chatgpt.com)

Sign in with your **Azure credentials**.

Microsoft Foundry uses projects to organize:

- Models
- Resources
- Data
- AI assets

---

## Step 2: Enable New Foundry

At the top of the Microsoft Foundry page, find:

**New Foundry**

If it is not enabled, turn it on.

---

## Step 3: Create a Project

Create a new project with a **unique name**.

In the Advanced options, use:

| Setting              | Value                             |
| -------------------- | --------------------------------- |
| **Foundry resource** | `Project64997393-resource`        |
| **Subscription**     | Your Azure subscription           |
| **Resource group**   | `ResourceGroup1`                  |
| **Region**           | Any recommended AI Foundry region |

Wait for the project to finish creating. It may take a few minutes.

---

# Part 2: Use AI to Understand Images

Now we will make the AI **look at an image and understand it**.

This is called **computer vision** or **vision capability**.

For example:

```text
Image
  ↓
AI Vision Model
  ↓
AI understands the image
  ↓
Text explanation
```

The exercise uses this capability to identify **vintage computer hardware**.

---

# Part 3: Download the Images

## Step 1: Download `images.zip`

Download the image file from:

[Download images.zip](https://microsoftlearning.github.io/mslearn-ai-fundamentals/data/images.zip?utm_source=chatgpt.com)

Save it to your computer.

## Step 2: Extract the ZIP

Right-click the downloaded file and extract it.

For example:

```text
images.zip
    ↓
Extract
    ↓
images folder
    ├── image1
    ├── image2
    ├── image3
    └── ...
```

These are the images that you will give to the AI for analysis.

---

# Part 4: Deploy GPT-5-mini

Go back to your Microsoft Foundry project.

## Step 1: Open Models

Go to:

**Discover → Models**

This opens the Microsoft Foundry model catalog.

## Step 2: Search for GPT-5-mini

Search for:

```text
gpt-5-mini
```

Deploy it using the default settings.

Deployment may take a little time.

### Important

Model deployment depends on:

- Region
- Azure quota
- Subscription
- Model availability

If `gpt-5-mini` cannot be deployed, the exercise says you can use another vision-capable/chat model such as:

- `gpt-5-nano`
- `gpt-5.4-mini`

Alternatively, create the project in another region.

---

# Part 5: Open the Model Playground

After deployment, Microsoft Foundry opens the **model playground**.

The playground allows you to communicate with the model without writing code.

Think of it like:

```text
You
 ↓
Model Playground
 ↓
AI Model
 ↓
Answer
```

---

# Part 6: Give Instructions to the Model

On the left side, find **Instructions**.

Enter:

```text
You are an AI assistant that helps people identify vintage computer hardware.
```

Now the AI knows what its main job is.

---

# Part 7: Upload an Image

In the Chat pane, select:

**Upload image**

Choose one of the images you extracted earlier.

The image will appear in the prompt area.

Then enter:

```text
What can you tell me about this?
```

Submit the prompt.

Now you are sending **both text and an image** to the AI.

```text
             Your Prompt
                  │
        ┌─────────┴─────────┐
        │                   │
      Image                Text
        │                   │
        └─────────┬─────────┘
                  ↓
              AI Model
                  ↓
          Image Explanation
```

---

# Part 8: Analyze More Images

You can upload the other images and ask questions such as:

```text
What is this?
```

or:

```text
Tell me about this.
```

The AI will analyze the images and provide information about them.

---

# Part 9: Use Python to Analyze Images

You can also use code instead of the playground.

Microsoft Foundry supports using the **OpenAI Responses API** to send image input to the model.

The basic idea is:

```text
Python Application
       ↓
OpenAI API
       ↓
GPT Vision Model
       ↓
Image Analysis
       ↓
Response
```

---

# Part 10: Python Code for Image Analysis

The sample uses:

- **Python**
- **OpenAI SDK**
- **Key authentication**

The important part is that the input contains both **text and an image**.

Example:

```python
from openai import OpenAI

endpoint = "https://your-project-resource.openai.azure.com/openai/v1/"
deployment_name = "gpt-5-mini"
api_key = "<your-api-key>"

client = OpenAI(
    base_url=endpoint,
    api_key=api_key
)

response = client.responses.create(
    model=deployment_name,
    input=[{
        "role": "user",
        "content": [
            {
                "type": "input_text",
                "text": "what's in this image?"
            },
            {
                "type": "input_image",
                "image_url": "https://an-online-image.jpg"
            }
        ],
    }],
)

print(f"answer: {response.output_text}")
```

### Important parts

#### `deployment_name`

```python
deployment_name = "gpt-5-mini"
```

This is the model deployment you want to use.

#### `input_text`

```python
"text": "what's in this image?"
```

This is your question.

#### `input_image`

```python
"image_url": "https://an-online-image.jpg"
```

This tells the model which image it needs to analyze.

---

# Part 11: Generate New Images

So far, we gave an image **to AI** and asked AI to understand it.

Now we will do the opposite:

> Give AI a text description and ask it to create an image.

For example:

```text
Text Prompt
     ↓
Image Generation Model
     ↓
New Image
```

The exercise gives this example:

```text
A vintage PC with a CRT monitor.
```

---

# Part 12: Deploy an Image Generation Model

Go back to the Models page.

Select:

**Deploy a base model**

Then select:

**Collections → Direct from Azure**

For **Inference tasks**, select:

**Text to image**

You will now see models that can generate images.

---

## Step 2: Select an Image Model

You may see models such as:

```text
gpt-image-1-mini
FLUX.2-pro
```

Choose an available model and deploy it.

Model availability depends on:

- Your subscription
- Region
- Quota

---

# Part 13: Generate an Image

After deployment, the **Image Playground** will open.

Enter a prompt such as:

```text
A vintage PC with a CRT monitor.
```

The AI will generate an image based on your description.

For example:

```text
"A vintage PC with a CRT monitor"
                  ↓
          Image Generation AI
                  ↓
             🖥️ New Image
```

---

# Part 14: Python Code for Image Generation

If your deployed model provides code samples, you can use Python with the **OpenAI SDK**.

The exercise uses:

- Python
- OpenAI SDK
- Key authentication

Example:

```python
import base64
from openai import OpenAI

endpoint = "https://your-project-resource.openai.azure.com/openai/v1/"
deployment_name = "your-text-to-image-model-deployment"
api_key = "<your-api-key>"

client = OpenAI(
    base_url=endpoint,
    api_key=api_key
)

img = client.images.generate(
    model=deployment_name,
    prompt="A cute baby polar bear",
    n=1,
    size="1024x1024",
)

image_bytes = base64.b64decode(img.data[0].b64_json)

with open("output.png", "wb") as f:
    f.write(image_bytes)
```

---

# Part 15: Understand the Image Generation Code

### 1. Import libraries

```python
import base64
from openai import OpenAI
```

`OpenAI` is used to communicate with the model.

`base64` is used to decode the generated image data.

---

### 2. Set the endpoint

```python
endpoint = "https://your-project-resource.openai.azure.com/openai/v1/"
```

The endpoint tells Python where your Azure AI service is located.

---

### 3. Set the model

```python
deployment_name = "your-text-to-image-model-deployment"
```

This is the name of your deployed image-generation model.

---

### 4. Create the client

```python
client = OpenAI(
    base_url=endpoint,
    api_key=api_key
)
```

This creates the connection between your Python application and the AI service.

---

### 5. Generate the image

```python
img = client.images.generate(
    model=deployment_name,
    prompt="A cute baby polar bear",
    n=1,
    size="1024x1024",
)
```

Here:

| Parameter | Meaning                  |
| --------- | ------------------------ |
| `model`   | Image-generation model   |
| `prompt`  | Description of the image |
| `n=1`     | Generate one image       |
| `size`    | Image size               |

So:

```text
prompt = "A cute baby polar bear"
```

means:

> Create an image of a cute baby polar bear.

---

### 6. Save the Image

```python
image_bytes = base64.b64decode(img.data[0].b64_json)

with open("output.png", "wb") as f:
    f.write(image_bytes)
```

This converts the generated image data into bytes and saves it as:

```text
output.png
```

---

# Part 16: Generate Video with Sora-2

Now we move from **image generation** to **video generation**.

The exercise uses **Sora-2** if it is available in your subscription and region.

The flow is:

```text
Text Prompt
     ↓
Sora-2
     ↓
Video
```

For example:

```text
A retro computer game.
```

The model generates a video based on the prompt.

---

# Part 17: Deploy Sora-2

Go back to the Models page.

Select:

**Deploy a base model**

Then:

**Collections → Direct from Azure**

For **Inference tasks**, select:

**Video generation**

Find:

```text
Sora-2
```

and deploy it.

### Important

Sora-2 may not be available for every:

- Subscription
- Region
- Quota

You may also need to request access to the latest available model.

---

# Part 18: Generate a Video

After deployment, the **Video Playground** opens.

Enter a prompt such as:

```text
A retro computer game.
```

The model will generate a video.

---

# Part 19: Sora-2 REST API Code

For video generation, the exercise shows using the **REST API**.

Example:

```bash
curl -X POST "https://your-project-resource.openai.azure.com/openai/v1/video/generations/jobs" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $AZURE_API_KEY" \
-d '{
  "prompt" : "A video of a cat",
  "height" : "1080",
  "width" : "1080",
  "n_seconds" : "5",
  "n_variants" : "1",
  "model": "sora"
}'
```

### Important parameters

| Parameter    | Meaning                  |
| ------------ | ------------------------ |
| `prompt`     | Description of the video |
| `height`     | Video height             |
| `width`      | Video width              |
| `n_seconds`  | Video duration           |
| `n_variants` | Number of variations     |
| `model`      | Video model              |

For example:

```text
"n_seconds": "5"
```

means the requested video duration is **5 seconds**.

---

# Part 20: Your Actual Azure Python Code

The file also provides Python examples using **Azure authentication** instead of directly putting an API key into the code.

## GPT-5-mini

```python
from openai import OpenAI
from azure.identity import DefaultAzureCredential, get_bearer_token_provider

endpoint = "https://project64997393-resource.services.ai.azure.com/openai/v1"
deployment_name = "gpt-5-mini-1"

token_provider = get_bearer_token_provider(
    DefaultAzureCredential(),
    "https://ai.azure.com/.default"
)

client = OpenAI(
    base_url=endpoint,
    api_key=token_provider
)

response = client.responses.create(
    model=deployment_name,
    input="What is the capital of France?",
)

print(f"answer: {response.output[0]}")
```

The important difference here is that the program uses:

```python
DefaultAzureCredential()
```

to authenticate with Azure rather than manually putting an API key into the source code.

---

# Part 21: Your Image Generation Python Code

Your file also gives this Azure-authenticated example:

```python
import base64
from openai import OpenAI
from azure.identity import DefaultAzureCredential, get_bearer_token_provider

endpoint = "https://project64997393-resource.services.ai.azure.com/openai/v1"
deployment_name = "gpt-image-1-mini"

token_provider = get_bearer_token_provider(
    DefaultAzureCredential(),
    "https://ai.azure.com/.default"
)

client = OpenAI(
    base_url=endpoint,
    api_key=token_provider
)

img = client.images.generate(
    model=deployment_name,
    prompt="A cute baby polar bear",
    n=1,
    size="1024x1024",
)

image_bytes = base64.b64decode(img.data[0].b64_json)

with open("output.png", "wb") as f:
    f.write(image_bytes)
```

---

# 22. Very Important: Model vs Deployment

This exercise uses several terms that can be confusing.

### Model

The **model** is the AI itself.

Examples:

```text
gpt-5-mini
gpt-image-1-mini
Sora-2
```

### Deployment

A **deployment** is your deployed instance/configuration of a model that your application can call.

For example, the model could be:

```text
gpt-5-mini
```

and your deployment could be:

```text
gpt-5-mini-1
```

So:

```text
Model
  ↓
gpt-5-mini
  ↓
Deploy it
  ↓
Deployment
  ↓
gpt-5-mini-1
  ↓
Python Application
```

This is why the code has:

```python
deployment_name = "gpt-5-mini-1"
```

rather than simply using the model's catalog name.

---

# 23. Complete Concept in One Diagram

This entire exercise can be understood like this:

```text
                 MICROSOFT FOUNDRY
                        │
                        ▼
                   PROJECT
                        │
          ┌─────────────┼─────────────┐
          │             │             │
          ▼             ▼             ▼
     GPT-5-mini    Image Model     Sora-2
          │             │             │
          ▼             ▼             ▼
    Understand       Generate       Generate
      Images          Images         Videos
          │             │             │
          ▼             ▼             ▼
     🖼️ Image       🎨 Image       🎬 Video
      Analysis       Creation       Creation
```

---

# 24. The Three Main Things You Learned

## 🖼️ 1. Image Understanding

You give the AI an image.

```text
Image + Question
       ↓
AI Model
       ↓
Description / Answer
```

Example:

> "What's in this image?"

---

## 🎨 2. Image Generation

You give the AI a description.

```text
Text Prompt
     ↓
Image Model
     ↓
New Image
```

Example:

> "A vintage PC with a CRT monitor."

---

## 🎬 3. Video Generation

You give the AI a description.

```text
Text Prompt
     ↓
Sora-2
     ↓
Video
```

Example:

> "A retro computer game."

---

# 25. Final Summary

In this Microsoft Foundry exercise, you learned how **vision-enabled generative AI** can work with visual information. You explored:

- **Image understanding** — AI analyzes an existing image.
- **Image generation** — AI creates a new image from text.
- **Video generation** — AI creates a video from text.
- **Playgrounds** — test models without writing code.
- **OpenAI SDK** — use models from Python.
- **REST API** — call video generation programmatically.
- **Azure authentication** — use `DefaultAzureCredential` and a bearer token provider.

### The easiest way to remember it

> **Give image → AI understands it.**
> **Give text → AI creates an image.**
> **Give text → Sora creates a video.**

---

# 26. Clean Up

When you finish the exercise, delete the Azure resources if you don't need them anymore to avoid unnecessary costs.

Open:

[Azure Portal](https://portal.azure.com?utm_source=chatgpt.com)

Then:

```text
Azure Portal
     ↓
Resource Groups
     ↓
ResourceGroup1
     ↓
Delete resource group
     ↓
Enter resource group name
     ↓
Confirm deletion
```

The source specifically instructs you to delete the resource group containing the resources you created.
