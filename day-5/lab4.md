# Microsoft Foundry Lab — Build a Generative AI App with Tools

## 🎯 What you will build

You will create a travel chatbot for **Margie's Travel**.

The chatbot will be able to:

1. Answer normal travel questions using the AI model.
2. Search the **web** for current information.
3. Search **travel brochure PDFs** for information about Margie's Travel.
4. Remember the previous question/answer in the conversation.

The lab uses the Microsoft Foundry portal and the OpenAI Responses API.

---

# Part 1 — Prerequisites

Before starting, make sure you have:

- Azure subscription
- Visual Studio Code
- Python **3.13.x**
- Git
- Azure CLI

The lab was tested with Python **3.13.12**.

Check your versions:

```powershell
python --version
git --version
az --version
```

You should have something like:

```text
Python 3.13.12
```

---

# Part 2 — Create Microsoft Foundry Project

## Step 1: Open Microsoft Foundry

Open:

[Microsoft Foundry](https://ai.azure.com?utm_source=chatgpt.com)

Sign in with your Azure account.

---

## Step 2: Create the project

Create a new project.

Use:

| Setting          | Value                             |
| ---------------- | --------------------------------- |
| Project name     | `Project65023168`                 |
| Foundry resource | `Project65023168-resource`        |
| Subscription     | Your Azure subscription           |
| Resource group   | `ResourceGroup1`                  |
| Region           | Any recommended AI Foundry region |

The project is where your models, resources, data, and other AI assets are organized.

### Important concept

Think of it like this:

```text
Azure
│
├── Resource Group
│      │
│      └── Foundry Resource
│              │
│              └── Project
│                    │
│                    ├── Models
│                    ├── Deployments
│                    ├── Tools
│                    └── Data
```

---

# Part 3 — Deploy GPT-5.2

Now we need an AI model for our application.

## Step 1

In Microsoft Foundry:

```text
Discover
   ↓
Models
```

Search for:

```text
gpt-5.2
```

---

## Step 2

Open the GPT-5.2 model.

Select:

```text
Deploy
```

Use the default settings.

After deployment, the model should open in the **model playground**.

---

# Part 4 — Test the Model in Playground

Before writing Python code, let's understand how tools improve the AI.

## Step 1 — Add instructions

In the playground, put this in the **Instructions** field:

```text
You are a travel assistant that provides information on
travel services available from Margie's Travel.
```

This tells the AI what role it should perform.

---

## Step 2 — Ask a question

Ask:

```text
What are some recommended tourist activities in New York next month?
```

### What happens?

The model can provide general travel information.

But it doesn't necessarily know what is **currently happening next month**.

That's because the model's normal knowledge isn't the same as live web information.

---

# Part 5 — Add Web Search

Now we give the AI a tool.

Go to:

```text
Tools
   ↓
Add
   ↓
web_search
```

Then ask the same question:

```text
What are some recommended tourist activities in New York next month?
```

Now the model can use the web to find current information.

### Simple idea

Without web search:

```text
User
 ↓
AI Model
 ↓
Answer from model knowledge
```

With web search:

```text
User
 ↓
AI Model
 ↓
web_search
 ↓
Current web information
 ↓
AI Model
 ↓
Answer
```

---

# Part 6 — Create the Python Application

Now we move from the playground to a real Python application.

The application will use:

```text
Python
   ↓
OpenAI SDK
   ↓
Azure OpenAI
   ↓
GPT-5.2
   ↓
Tools
   ├── file_search
   └── web_search
```

The lab specifically uses the **Azure OpenAI endpoint**, not the project endpoint.

---

# Part 7 — Get the Azure OpenAI Endpoint

In Microsoft Foundry:

```text
Home
   ↓
Azure OpenAI Endpoint
```

Copy the endpoint.

### ⚠️ Important

There can be two different endpoints:

```text
Project Endpoint
```

and

```text
Azure OpenAI Endpoint
```

For this Python lab, use the:

```text
Azure OpenAI Endpoint
```

not the project endpoint.

---

# Part 8 — Download the Lab Code

Open Visual Studio Code.

Press:

```text
Ctrl + Shift + P
```

Search:

```text
Git: Clone
```

Clone:

[Microsoft Learning AI Studio GitHub repository](https://github.com/microsoftlearning/mslearn-ai-studio?utm_source=chatgpt.com)

Then open the repository in VS Code.

---

# Part 9 — Select Python Environment

In VS Code:

```text
Extensions
   ↓
Python
```

Install the Python extension if necessary.

Then:

```text
Ctrl + Shift + P
```

Search:

```text
Python: Select Interpreter
```

Select/create a virtual environment using Python 3.13.

The lab's application is located in:

```text
/labfiles/tools/python/tools-app
```

The important files are:

```text
tools-app
│
├── brochures/
│     └── PDF travel brochures
│
├── .env
├── requirements.txt
└── tools-app.py
```

---

# Part 10 — Open Terminal

Right-click the `tools-app` folder and select:

```text
Open in Integrated Terminal
```

Your terminal should show something similar to:

```text
(.venv)
```

This means your virtual environment is active.

---

# Part 11 — Install Required Libraries

Run:

```powershell
pip install -r requirements.txt
```

This installs the required Python packages.

---

# Part 12 — Configure `.env`

Open:

```text
.env
```

You need two important values:

```env
AZURE_OPENAI_ENDPOINT="YOUR_AZURE_OPENAI_ENDPOINT"
MODEL_DEPLOYMENT="YOUR_DEPLOYMENT_NAME"
```

For example:

```env
AZURE_OPENAI_ENDPOINT="https://xxxxxxxx.openai.azure.com/"
MODEL_DEPLOYMENT="gpt-5.2-1"
```

### Important difference

`MODEL_DEPLOYMENT` is **not necessarily**:

```text
gpt-5.2
```

It must be the **exact deployment name you gave your deployed model**.

For example, if you deployed GPT-5.2 and named the deployment:

```text
gpt-5.2-1
```

then:

```env
MODEL_DEPLOYMENT="gpt-5.2-1"
```

The lab specifically says to enter the exact deployment name.

---

# Part 13 — Python Code

Open:

```text
tools-app.py
```

The lab already provides some code. We add the following pieces.

---

## Step 1 — Import libraries

At the top:

```python
import os
from dotenv import load_dotenv
import glob

from openai import OpenAI
from azure.identity import DefaultAzureCredential, get_bearer_token_provider
```

These imports allow us to:

- read environment variables
- find PDF files
- communicate with OpenAI
- authenticate with Azure

The lab uses `DefaultAzureCredential` and an Entra ID token provider for authentication.

---

# Part 14 — Create OpenAI Client

Inside `main()`:

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

### What is happening?

```text
Your Python application
        ↓
DefaultAzureCredential
        ↓
Azure authentication
        ↓
Token
        ↓
OpenAI client
        ↓
Azure OpenAI
```

This allows the application to authenticate using Azure/Entra ID rather than putting an API key directly in the code.

---

# Part 15 — Create Vector Store

Now we need to make the travel brochures searchable.

Add:

```python
print("Creating vector store and uploading files...")

vector_store = openai_client.vector_stores.create(
    name="travel-brochures"
)
```

A **vector store** is used to store/search the content of the travel documents.

---

# Part 16 — Find the PDF Files

The brochures are inside:

```text
brochures/
```

Use:

```python
file_streams = [
    open(f, "rb")
    for f in glob.glob("brochures/*.pdf")
]
```

This means:

> Find every PDF file inside the `brochures` folder and open it for uploading.

Then check if there are no files:

```python
if not file_streams:
    print("No PDF files found in the brochures folder!")
    return
```

---

# Part 17 — Upload PDFs to Vector Store

Use:

```python
file_batch = openai_client.vector_stores.file_batches.upload_and_poll(
    vector_store_id=vector_store.id,
    files=file_streams
)
```

Then close the files:

```python
for f in file_streams:
    f.close()
```

Finally:

```python
print(
    f"Vector store created with "
    f"{file_batch.file_counts.completed} files."
)
```

The complete flow is:

```text
Travel PDF
   ↓
Open PDF
   ↓
Upload
   ↓
Vector Store
   ↓
file_search
```

---

# Part 18 — Maintain Conversation

We create a variable:

```python
last_response_id = None
```

This stores the ID of the previous AI response.

Later we use:

```python
previous_response_id=last_response_id
```

This allows follow-up questions to continue the conversation.

---

# Part 19 — Ask User for Questions

The application keeps asking questions:

```python
while True:

    input_text = input(
        '\nEnter a question (or type "quit" to exit): '
    )

    if input_text.lower() == "quit":
        break

    if len(input_text) == 0:
        print("Please enter a question.")
        continue
```

So the user can interact like:

```text
Enter a question:
What's happening in San Francisco next month?
```

Then:

```text
Enter a question:
What hotels does Margie's Travel offer there?
```

---

# Part 20 — Call the AI with Tools

This is the **most important part of the lab**.

```python
response = openai_client.responses.create(
    model=model_deployment,

    instructions="""
    You are a travel assistant that provides information
    on travel services available from Margie's Travel.

    Answer questions about services offered by
    Margie's Travel using the provided travel brochures.

    Search the web for general information about
    destinations or current travel advice.
    """,

    input=input_text,

    previous_response_id=last_response_id,

    tools=[
        {
            "type": "file_search",
            "vector_store_ids": [vector_store.id]
        },
        {
            "type": "web_search"
        }
    ]
)
```

---

# Part 21 — Understand the Tools

There are **two tools**:

### Tool 1 — `file_search`

```python
{
    "type": "file_search",
    "vector_store_ids": [vector_store.id]
}
```

Used for:

> Information inside Margie's Travel brochures.

Example:

```text
What hotels does Margie's Travel offer?
```

The AI can search the uploaded brochures.

---

### Tool 2 — `web_search`

```python
{
    "type": "web_search"
}
```

Used for:

> General/current information from the web.

Example:

```text
What's happening in San Francisco next month?
```

The AI can search current web information.

The lab explicitly describes `file_search` as searching the vector store and `web_search` as performing general web searches.

---

# Part 22 — Display the Answer

After getting the response:

```python
print(response.output_text)
```

Then save the response ID:

```python
last_response_id = response.id
```

This allows the next question to use the previous response as context.

---

# Part 23 — Complete Python Code

Here is the complete code from the lab, formatted cleanly:

```python
import os
from dotenv import load_dotenv
import glob

from openai import OpenAI
from azure.identity import (
    DefaultAzureCredential,
    get_bearer_token_provider
)


def main():

    # Clear the console
    os.system('cls' if os.name == 'nt' else 'clear')

    try:

        # -----------------------------------------
        # 1. Load configuration
        # -----------------------------------------

        load_dotenv()

        azure_openai_endpoint = os.getenv(
            "AZURE_OPENAI_ENDPOINT"
        )

        model_deployment = os.getenv(
            "MODEL_DEPLOYMENT"
        )


        # -----------------------------------------
        # 2. Initialize OpenAI client
        # -----------------------------------------

        token_provider = get_bearer_token_provider(
            DefaultAzureCredential(),
            "https://ai.azure.com/.default"
        )

        openai_client = OpenAI(
            base_url=azure_openai_endpoint,
            api_key=token_provider
        )


        # -----------------------------------------
        # 3. Create vector store
        # -----------------------------------------

        print(
            "Creating vector store and uploading files..."
        )

        vector_store = openai_client.vector_stores.create(
            name="travel-brochures"
        )


        # -----------------------------------------
        # 4. Find PDF brochures
        # -----------------------------------------

        file_streams = [
            open(f, "rb")
            for f in glob.glob("brochures/*.pdf")
        ]

        if not file_streams:

            print(
                "No PDF files found in the brochures folder!"
            )

            return


        # -----------------------------------------
        # 5. Upload brochures
        # -----------------------------------------

        file_batch = (
            openai_client
            .vector_stores
            .file_batches
            .upload_and_poll(
                vector_store_id=vector_store.id,
                files=file_streams
            )
        )


        # Close files
        for f in file_streams:
            f.close()


        print(
            f"Vector store created with "
            f"{file_batch.file_counts.completed} files."
        )


        # -----------------------------------------
        # 6. Track conversation
        # -----------------------------------------

        last_response_id = None


        # -----------------------------------------
        # 7. Chat loop
        # -----------------------------------------

        while True:

            input_text = input(
                '\nEnter a question '
                '(or type "quit" to exit): '
            )


            # Exit
            if input_text.lower() == "quit":
                break


            # Empty input
            if len(input_text) == 0:

                print("Please enter a question.")

                continue


            # -----------------------------------------
            # 8. Get AI response using tools
            # -----------------------------------------

            response = openai_client.responses.create(

                model=model_deployment,

                instructions="""
                You are a travel assistant that provides
                information on travel services available
                from Margie's Travel.

                Answer questions about services offered by
                Margie's Travel using the provided travel
                brochures.

                Search the web for general information about
                destinations or current travel advice.
                """,

                input=input_text,

                previous_response_id=last_response_id,

                tools=[

                    {
                        "type": "file_search",
                        "vector_store_ids": [
                            vector_store.id
                        ]
                    },

                    {
                        "type": "web_search"
                    }

                ]
            )


            # -----------------------------------------
            # 9. Display response
            # -----------------------------------------

            print(response.output_text)


            # Save response ID
            last_response_id = response.id


    except Exception as ex:

        print(ex)


if __name__ == '__main__':
    main()
```

---

# Part 24 — Login to Azure

Before running the application, login using Azure CLI:

```powershell
az login
```

A browser will open.

Complete the Azure login.

The lab notes that `az login` is normally enough; if you have multiple tenants, you may need `--tenant`.

---

# Part 25 — Run the Application

Make sure you're inside:

```text
tools-app
```

Then run:

```powershell
python tools-app.py
```

You should see:

```text
Creating vector store and uploading files...
Vector store created with X files.

Enter a question (or type "quit" to exit):
```

---

# Part 26 — Test Web Search

Ask:

```text
What's happening in San Francisco next month?
```

The application should use:

```text
web_search
```

to retrieve current information.

---

# Part 27 — Test File Search

Now ask:

```text
What hotels does Margie's Travel offer there?
```

The application should use:

```text
file_search
```

to search the travel brochures.

---

# 🧠 The Most Important Concept

You are building this:

```text
                     ┌──────────────┐
                     │     User     │
                     └──────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │  GPT-5.2      │
                    │     Model     │
                    └───────┬───────┘
                            │
                ┌───────────┴───────────┐
                │                       │
                ▼                       ▼
        ┌───────────────┐       ┌───────────────┐
        │  web_search   │       │  file_search  │
        │               │       │               │
        │ Current web   │       │ Travel PDFs   │
        │ information   │       │ /brochures    │
        └───────┬───────┘       └───────┬───────┘
                │                       │
                └───────────┬───────────┘
                            ▼
                    ┌───────────────┐
                    │    GPT-5.2    │
                    │  Final Answer │
                    └───────────────┘
```

### In simple language:

**GPT-5.2 is the brain.**

**web_search is its connection to current web information.**

**file_search is its connection to your private travel documents.**

**Vector Store is where the searchable brochure information is stored.**

---

# Part 28 — What Each Important File Does

| File               | Purpose                                      |
| ------------------ | -------------------------------------------- |
| `.env`             | Stores endpoint and deployment configuration |
| `requirements.txt` | Lists Python packages                        |
| `tools-app.py`     | Main Python application                      |
| `brochures/`       | Contains travel PDF documents                |

---

# Part 29 — Important Values to Check

Before running the application, check these three things:

### 1. Endpoint

```env
AZURE_OPENAI_ENDPOINT="..."
```

Must be the **Azure OpenAI endpoint**.

### 2. Deployment

```env
MODEL_DEPLOYMENT="..."
```

Must exactly match your **GPT-5.2 deployment name**.

### 3. Brochures

Your folder should contain:

```text
brochures/
    brochure1.pdf
    brochure2.pdf
    ...
```

Otherwise you'll get:

```text
No PDF files found in the brochures folder!
```

---

# Part 30 — Clean Up Azure Resources

When you're finished, delete the resources to avoid unnecessary Azure costs.

Go to:

```text
Azure Portal
   ↓
Resource Groups
   ↓
ResourceGroup1
   ↓
Delete resource group
```

Enter the resource group name and confirm deletion.

---

## ⭐ Lab in One-Line Flow

Remember the entire lab like this:

```text
Create Foundry Project
        ↓
Deploy GPT-5.2
        ↓
Test model in Playground
        ↓
Add web_search
        ↓
Get Azure OpenAI Endpoint
        ↓
Clone Python application
        ↓
Install requirements
        ↓
Configure .env
        ↓
Create OpenAI client
        ↓
Create Vector Store
        ↓
Upload travel PDFs
        ↓
Add file_search + web_search
        ↓
az login
        ↓
python tools-app.py
        ↓
Test questions
        ↓
Delete Azure resources
```

This is the core idea of the entire lab.
