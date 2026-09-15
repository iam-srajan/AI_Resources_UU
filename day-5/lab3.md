````md
# Create a Python Chat Application with Microsoft Foundry

## Overview

In this exercise, you will create a Python chat application that communicates with a model deployed in a **Microsoft Foundry project**.

You will learn how to:

1. Download the starter application from GitHub.
2. Configure the Python environment.
3. Connect to an Azure-hosted model using the OpenAI SDK.
4. Use the **Chat Completions API**.
5. Use the newer **Responses API**.
6. Maintain conversation context.
7. Stream responses as they are generated.
8. Use the asynchronous OpenAI API.

---

# 1. Get the Application Files

The starter application is available in the Microsoft Learning GitHub repository.

## Clone the repository

1. Open **Visual Studio Code**.
2. Press:

   ```text
   Ctrl + Shift + P
   ```
````

3. Search for:

   ```text
   Git: Clone
   ```

4. Clone this repository:

   ```text
   https://github.com/microsoftlearning/mslearn-ai-studio
   ```

5. Select a local folder.

6. Open the cloned repository in Visual Studio Code.

7. If Visual Studio Code asks whether you trust the authors, select **Yes/Trust** if appropriate.

---

# 2. Prepare the Python Environment

## Install the Python Extension

In Visual Studio Code:

1. Open the **Extensions** pane.
2. Search for **Python**.
3. Install the Microsoft Python extension if it is not already installed.

## Select the Python Interpreter

Open the Command Palette:

```text
Ctrl + Shift + P
```

Search for:

```text
Python: Select Interpreter
```

Select your Python 3.13 installation and create/select a virtual environment (`.venv`).

> **Tip:** If Visual Studio Code asks to install dependencies from `requirements.txt`, you can allow it. Otherwise, install them manually in the next step.

---

# 3. Open the Application Folder

Navigate to:

```text
labfiles/foundry-chat/python/chat-app
```

The folder contains the following important files:

| File               | Purpose                           |
| ------------------ | --------------------------------- |
| `.env`             | Stores application configuration  |
| `requirements.txt` | Lists required Python packages    |
| `chat-app.py`      | Main synchronous chat application |
| `chat-async.py`    | Asynchronous chat application     |

Right-click the `chat-app` folder and select:

```text
Open in Integrated Terminal
```

The terminal should show your virtual environment, for example:

```text
(.venv)
```

---

# 4. Install Required Packages

Run:

```powershell
pip install -r requirements.txt
```

This installs the required packages, including the OpenAI SDK and Azure Identity libraries.

---

# 5. Configure the `.env` File

Open:

```text
labfiles/foundry-chat/python/chat-app/.env
```

Add/update the following settings:

```env
AZURE_OPENAI_ENDPOINT="YOUR_AZURE_OPENAI_ENDPOINT"
MODEL_DEPLOYMENT="YOUR_MODEL_DEPLOYMENT_NAME"
```

### Important

- `AZURE_OPENAI_ENDPOINT` should contain the **Azure OpenAI endpoint**, not the project endpoint.
- `MODEL_DEPLOYMENT` must contain the **exact deployment name** of your deployed model.

Example:

```env
AZURE_OPENAI_ENDPOINT="https://your-resource.services.ai.azure.com"
MODEL_DEPLOYMENT="gpt-5.2-1"
```

Use the actual values from your Microsoft Foundry/Azure environment.

Save the `.env` file.

---

# 6. Chat Application with the Chat Completions API

The **Chat Completions API** is a traditional and widely used way to communicate with language models.

## Import the Required Libraries

At the top of `chat-app.py`, add:

```python
from openai import OpenAI
from azure.identity import DefaultAzureCredential, get_bearer_token_provider
```

---

## Create the OpenAI Client

Use:

```python
token_provider = get_bearer_token_provider(
    DefaultAzureCredential(),
    "https://ai.azure.com/.default"
)

openai_client = OpenAI(
    base_url=azure_openai_endpoint,
    api_key=token_provider
)
```

### What this code does

- `DefaultAzureCredential()` gets Azure credentials.
- `get_bearer_token_provider()` creates a token provider.
- `OpenAI()` creates the OpenAI SDK client.
- `base_url` specifies the Azure endpoint.
- `api_key=token_provider` allows the SDK to obtain an Azure access token.

---

# 7. Send a Chat Completions Request

Inside the application loop, use:

```python
completion = openai_client.chat.completions.create(
    model=model_deployment,
    messages=[
        {
            "role": "system",
            "content": "You are a helpful AI assistant that answers questions and provides information."
        },
        {
            "role": "user",
            "content": input_text
        }
    ]
)

print(completion.choices[0].message.content)
```

## Understanding `messages`

The `messages` list contains the conversation information.

### System message

```python
{
    "role": "system",
    "content": "You are a helpful AI assistant..."
}
```

The system message tells the model how it should behave.

### User message

```python
{
    "role": "user",
    "content": input_text
}
```

This contains the user's question.

---

# 8. Sign in to Azure

Open the terminal and run:

```powershell
az login
```

A browser window may open.

Sign in using your Azure account.

If you have subscriptions in multiple tenants, you may need:

```powershell
az login --tenant YOUR_TENANT_ID
```

After signing in, make sure the correct Azure subscription is selected.

---

# 9. Run the Chat Application

Run:

```powershell
python chat-app.py
```

You should see something similar to:

```text
Enter a prompt (or type "quit" to exit):
```

Enter:

```text
Tell me about the ELIZA chatbot.
```

The model should return information about the ELIZA chatbot.

To exit:

```text
quit
```

---

# 10. Use the Responses API

The **Responses API** provides a simpler interface for many modern OpenAI model interactions.

Instead of using:

```python
openai_client.chat.completions.create(...)
```

use:

```python
openai_client.responses.create(...)
```

Replace the response section with:

```python
response = openai_client.responses.create(
    model=model_deployment,
    instructions="You are a helpful AI assistant that answers questions and provides information.",
    input=input_text
)

print(response.output_text)
```

## Difference

With Chat Completions:

```python
messages=[
    {
        "role": "system",
        "content": "..."
    },
    {
        "role": "user",
        "content": input_text
    }
]
```

With Responses API:

```python
instructions="..."
input=input_text
```

The Responses API is therefore simpler for this basic scenario.

---

# 11. Test the Responses API

Run:

```powershell
python chat-app.py
```

Enter:

```text
Tell me about the ELIZA chatbot.
```

Then try:

```text
How does it compare to modern LLMs?
```

You may notice that the model does not understand what **"it"** refers to.

## Why?

Each request is independent.

The application is not yet sending the previous response to the model.

We need to add **conversation tracking**.

---

# 12. Maintain Conversation Context

The Responses API allows you to connect a new response to a previous response using:

```python
previous_response_id
```

## Important Fix

`last_response_id` must be created **before the `while` loop**.

Do **not** put this inside the loop:

```python
while True:
    last_response_id = None
```

If you do that, the previous response ID is reset every time the user enters a new prompt.

Instead, use:

```python
last_response_id = None

while True:
    ...
```

---

# 13. Responses API with Conversation Tracking

Use this code:

```python
last_response_id = None

while True:
    input_text = input('\nEnter a prompt (or type "quit" to exit): ')

    if input_text.lower() == "quit":
        break

    if len(input_text.strip()) == 0:
        print("Please enter a prompt.")
        continue

    response = openai_client.responses.create(
        model=model_deployment,
        instructions="You are a helpful AI assistant that answers questions and provides information.",
        input=input_text,
        previous_response_id=last_response_id
    )

    print(response.output_text)

    last_response_id = response.id
```

---

# 14. How Conversation Tracking Works

Suppose the user first asks:

```text
Tell me about the ELIZA chatbot.
```

The model returns a response with an ID:

```text
response_123
```

The application stores it:

```python
last_response_id = response.id
```

The user then asks:

```text
How does it compare to modern LLMs?
```

The application sends:

```python
previous_response_id=last_response_id
```

The model can then use the previous response as conversational context.

### Flow

```text
User question
     |
     v
Responses API
     |
     v
Response + response.id
     |
     v
Save response.id
     |
     v
Next user question
     |
     v
Send previous_response_id
     |
     v
Model understands the conversation
```

---

# 15. Test Conversation Tracking

Run:

```powershell
python chat-app.py
```

First enter:

```text
Tell me about the ELIZA chatbot.
```

Then enter:

```text
How does it compare to modern LLMs?
```

This time, the model should understand that **"it"** refers to the ELIZA chatbot.

---

# 16. Implement Streaming Responses

A normal request waits for the complete response before displaying anything.

For long responses, this can make the application appear unresponsive.

**Streaming** allows the application to display the response as it arrives.

---

# 17. Responses API with Streaming

Replace the response section with:

```python
stream = openai_client.responses.create(
    model=model_deployment,
    instructions="You are a helpful AI assistant that answers questions and provides information.",
    input=input_text,
    previous_response_id=last_response_id,
    stream=True
)

for event in stream:
    if event.type == "response.output_text.delta":
        print(event.delta, end="", flush=True)

    elif event.type == "response.completed":
        last_response_id = event.response.id
        print()
```

### Important

The following parameter enables streaming:

```python
stream=True
```

The model then sends events as the response is generated.

---

# 18. Streaming Events

The application checks the event type:

```python
if event.type == "response.output_text.delta":
```

This means that a new piece of text has arrived.

The application prints it immediately:

```python
print(event.delta, end="", flush=True)
```

When the response is complete:

```python
elif event.type == "response.completed":
```

the application saves the new response ID:

```python
last_response_id = event.response.id
```

---

# 19. Test Streaming

Run:

```powershell
python chat-app.py
```

Enter:

```text
Tell me about the ELIZA chatbot.
```

The response should appear gradually instead of waiting for the entire response.

Then try:

```text
How does it compare to modern LLMs?
```

The response should also appear incrementally.

---

# 20. Complete Synchronous `chat-app.py`

Here is a clean version of the application using the **Responses API + conversation tracking + streaming**.

```python
import os

from dotenv import load_dotenv
from openai import OpenAI
from azure.identity import DefaultAzureCredential, get_bearer_token_provider


def main():
    # Clear the console
    os.system("cls" if os.name == "nt" else "clear")

    try:
        # Load configuration settings
        load_dotenv()

        azure_openai_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
        model_deployment = os.getenv("MODEL_DEPLOYMENT")

        if not azure_openai_endpoint:
            raise ValueError("AZURE_OPENAI_ENDPOINT is not set in the .env file.")

        if not model_deployment:
            raise ValueError("MODEL_DEPLOYMENT is not set in the .env file.")

        # Create Azure authentication token provider
        token_provider = get_bearer_token_provider(
            DefaultAzureCredential(),
            "https://ai.azure.com/.default"
        )

        # Initialize the OpenAI client
        openai_client = OpenAI(
            base_url=azure_openai_endpoint,
            api_key=token_provider
        )

        # Track the previous response
        last_response_id = None

        # Continue until the user wants to quit
        while True:
            input_text = input(
                '\nEnter a prompt (or type "quit" to exit): '
            )

            if input_text.lower() == "quit":
                break

            if len(input_text.strip()) == 0:
                print("Please enter a prompt.")
                continue

            # Send a streaming response request
            stream = openai_client.responses.create(
                model=model_deployment,
                instructions=(
                    "You are a helpful AI assistant that answers "
                    "questions and provides information."
                ),
                input=input_text,
                previous_response_id=last_response_id,
                stream=True
            )

            # Display the response as it arrives
            for event in stream:
                if event.type == "response.output_text.delta":
                    print(event.delta, end="", flush=True)

                elif event.type == "response.completed":
                    last_response_id = event.response.id
                    print()

    except Exception as ex:
        print(f"Error: {ex}")


if __name__ == "__main__":
    main()
```

---

# 21. Use the Asynchronous API

The OpenAI SDK also provides an asynchronous client.

Async operations can help applications remain responsive while waiting for long-running model operations.

For this example, use:

```text
chat-async.py
```

instead of:

```text
chat-app.py
```

---

# 22. Import Async Libraries

At the top of `chat-async.py`, add:

```python
import asyncio

from openai import AsyncOpenAI
from azure.identity.aio import (
    DefaultAzureCredential,
    get_bearer_token_provider
)
```

---

# 23. Create the Async OpenAI Client

Use:

```python
credential = DefaultAzureCredential()

token_provider = get_bearer_token_provider(
    credential,
    "https://ai.azure.com/.default"
)

async_client = AsyncOpenAI(
    base_url=azure_openai_endpoint,
    api_key=token_provider
)
```

---

# 24. Send an Async Request

Use:

```python
response = await async_client.responses.create(
    model=model_deployment,
    instructions=(
        "You are a helpful AI assistant that answers "
        "questions and provides information."
    ),
    input=input_text,
    previous_response_id=last_response_id
)

assistant_text = response.output_text

print("Assistant:", assistant_text)

last_response_id = response.id
```

The important part is:

```python
await
```

`await` waits for the asynchronous operation to finish without blocking the async event loop in the same way as a normal synchronous call.

---

# 25. Close the Async Credential

Because the Azure credential is asynchronous, close it when the application finishes:

```python
finally:
    await credential.close()
```

---

# 26. Complete Async `chat-async.py`

Use the following cleaned-up version:

```python
import asyncio
import os

from dotenv import load_dotenv
from openai import AsyncOpenAI
from azure.identity.aio import (
    DefaultAzureCredential,
    get_bearer_token_provider
)


async def main():
    # Clear the console
    os.system("cls" if os.name == "nt" else "clear")

    credential = None

    try:
        # Load configuration settings
        load_dotenv()

        azure_openai_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
        model_deployment = os.getenv("MODEL_DEPLOYMENT")

        if not azure_openai_endpoint:
            raise ValueError(
                "AZURE_OPENAI_ENDPOINT is not set in the .env file."
            )

        if not model_deployment:
            raise ValueError(
                "MODEL_DEPLOYMENT is not set in the .env file."
            )

        # Create Azure credential
        credential = DefaultAzureCredential()

        # Create token provider
        token_provider = get_bearer_token_provider(
            credential,
            "https://ai.azure.com/.default"
        )

        # Initialize async OpenAI client
        async_client = AsyncOpenAI(
            base_url=azure_openai_endpoint,
            api_key=token_provider
        )

        # Track conversation context
        last_response_id = None

        # Continue until the user wants to quit
        while True:
            input_text = input(
                '\nEnter a prompt (or type "quit" to exit): '
            )

            if input_text.lower() == "quit":
                break

            if len(input_text.strip()) == 0:
                print("Please enter a prompt.")
                continue

            # Send asynchronous request
            response = await async_client.responses.create(
                model=model_deployment,
                instructions=(
                    "You are a helpful AI assistant that answers "
                    "questions and provides information."
                ),
                input=input_text,
                previous_response_id=last_response_id
            )

            print("Assistant:", response.output_text)

            # Save response ID for the next request
            last_response_id = response.id

    except Exception as ex:
        print(f"Error: {ex}")

    finally:
        # Close the Azure credential
        if credential is not None:
            await credential.close()


if __name__ == "__main__":
    asyncio.run(main())
```

---

# 27. Run the Async Application

Run:

```powershell
python chat-async.py
```

Enter:

```text
Tell me about the Turing test.
```

The application should return information about the Turing test.

To exit:

```text
quit
```

---

# 28. Important API Concepts

## Chat Completions API

Traditional API style:

```python
completion = openai_client.chat.completions.create(
    model=model_deployment,
    messages=[
        {
            "role": "system",
            "content": "You are a helpful AI assistant."
        },
        {
            "role": "user",
            "content": input_text
        }
    ]
)
```

Response text:

```python
completion.choices[0].message.content
```

---

## Responses API

Modern API style:

```python
response = openai_client.responses.create(
    model=model_deployment,
    instructions="You are a helpful AI assistant.",
    input=input_text
)
```

Response text:

```python
response.output_text
```

---

## Conversation Tracking

Store the response ID:

```python
last_response_id = response.id
```

Send it with the next request:

```python
previous_response_id=last_response_id
```

---

## Streaming

Enable streaming:

```python
stream=True
```

Process text chunks:

```python
if event.type == "response.output_text.delta":
    print(event.delta, end="", flush=True)
```

---

## Async

Create an async client:

```python
async_client = AsyncOpenAI(...)
```

Send an async request:

```python
response = await async_client.responses.create(...)
```

---

# 29. Common Code Problems

## Problem 1: `last_response_id` resets

### Incorrect

```python
while True:
    last_response_id = None
```

This removes the conversation history on every loop.

### Correct

```python
last_response_id = None

while True:
    ...
```

---

## Problem 2: Running multiple API examples together

Do not put all of these in the same execution path:

```python
chat.completions.create(...)
```

```python
responses.create(...)
```

```python
responses.create(..., stream=True)
```

Choose **one approach** at a time.

For the final synchronous application, the recommended example in this lab is:

```python
responses.create(..., stream=True)
```

---

## Problem 3: Broken Python formatting

Code copied from formatted lab material may appear like this:

```text
import osfrom dotenv import load_dotenv
```

This is invalid Python.

It must be:

```python
import os
from dotenv import load_dotenv
```

---

## Problem 4: Incorrect `__name__` block

The correct Python syntax is:

```python
if __name__ == "__main__":
    main()
```

For an async application:

```python
if __name__ == "__main__":
    asyncio.run(main())
```

---

# 30. Final Project Structure

Your folder should look similar to:

```text
chat-app/
│
├── .env
├── requirements.txt
├── chat-app.py
└── chat-async.py
```

---

# 31. Commands You Need

## Install dependencies

```powershell
pip install -r requirements.txt
```

## Sign in to Azure

```powershell
az login
```

## Run synchronous application

```powershell
python chat-app.py
```

## Run asynchronous application

```powershell
python chat-async.py
```

---

# 32. Example Conversation

Run:

```powershell
python chat-app.py
```

Then:

```text
Enter a prompt (or type "quit" to exit): Tell me about the ELIZA chatbot.
```

The model responds.

Then:

```text
Enter a prompt (or type "quit" to exit): How does it compare to modern LLMs?
```

Because `previous_response_id` is being used, the model can understand the reference to ELIZA.

---

# 33. Summary

In this exercise, you created a Python chat application for a model deployed in Microsoft Foundry.

You learned:

- How to configure a Python application.
- How to install the OpenAI SDK.
- How to authenticate with Azure.
- How to create an `OpenAI` client.
- How to use the **Chat Completions API**.
- How to use the **Responses API**.
- How to maintain conversation context using `previous_response_id`.
- How to stream responses using `stream=True`.
- How to use `AsyncOpenAI`.
- How to make asynchronous model requests with `await`.

The overall flow is:

```text
Python Application
       |
       v
Azure Authentication
       |
       v
OpenAI SDK
       |
       v
Microsoft Foundry / Azure Model
       |
       v
Model Response
       |
       +----> Normal response
       |
       +----> Conversation context
       |
       +----> Streaming response
       |
       +----> Async response
```

---

# 34. Clean Up Azure Resources

If you have finished exploring Microsoft Foundry, delete the resources created for the exercise to avoid unnecessary Azure costs.

1. Open the **Azure portal**.

2. Find the resource group used for this exercise.

3. Open the resource group.

4. Select:

   ```text
   Delete resource group
   ```

5. Enter the resource group name.

6. Confirm the deletion.

> **Warning:** Deleting a resource group deletes the resources contained inside it. Make sure it contains only resources you no longer need.

---

# 35. End the Lab

If you are completing this as part of an Azure lab, make sure you also select **End Lab** when you are finished.

```

```
