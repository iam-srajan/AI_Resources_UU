# Microsoft Foundry — Text Analysis Lab

## 1. What is this lab teaching?

The main goal is to learn **two ways of analyzing text** in Microsoft Foundry.

### Approach 1 — General-purpose AI model

You use an LLM such as:

```text
GPT-5-mini
```

and give it natural-language prompts.

For example:

```text
Summarize this article.
```

The LLM understands the request and generates the answer.

### Approach 2 — Specialized language tools

You use **Azure Language** tools for specific tasks.

For example:

```text
Detect the language
Identify PII
Redact PII
```

These tools return more **structured and deterministic results**.

The lab specifically contrasts general-purpose AI models with purpose-built language tools.

---

# Overall Flow of the Lab

You can remember the whole lab like this:

```text
Create Foundry Project
        ↓
Deploy GPT-5-mini
        ↓
Use LLM for Text Summarization
        ↓
Open Azure Language Tools
        ↓
Detect Language
        ↓
Detect PII
        ↓
Look at Python Code
        ↓
Clean Up Azure Resources
```

The exercise is expected to take about **20 minutes**.

---

# PART 1 — Create a Microsoft Foundry Project

## Step 1: Open Microsoft Foundry

Open:

[Microsoft Foundry](https://ai.azure.com?utm_source=chatgpt.com)

Sign in using your Azure credentials.

---

# Step 2: Enable New Foundry

At the top of the Foundry portal, look for:

**New Foundry**

If it isn't already enabled, enable it.

---

# Step 3: Create the Project

If you don't already have a project, create one.

Use this project name:

```text
project64911452
```

Open:

**Advanced options**

and configure:

| Setting          | Value                             |
| ---------------- | --------------------------------- |
| Project name     | `project64911452`                 |
| Foundry resource | `project64911452-resource`        |
| Subscription     | Your Azure subscription           |
| Resource group   | `ResourceGroup1`                  |
| Region           | Any AI Foundry recommended region |

These are the exact settings specified by the lab.

---

# Step 4: Create the Project

Click:

**Create**

Wait for the project to finish creating.

It may take a few minutes.

After it opens, close any **Quick Start** pages if they appear.

You should now be inside your Foundry project.

---

# PART 2 — Use a General-Purpose AI Model

Now you're going to use an LLM to perform a text-analysis task.

The task will be:

> **Summarize text.**

The lab explains that LLMs can perform common text-analysis tasks such as summarization, entity extraction, and classification.

---

# Step 5: Open the Model Catalog

Inside your project:

1. Go to **Discover**.
2. Select **Models**.

This opens the Microsoft Foundry model catalog.

---

# Step 6: Find GPT-5-mini

Search for:

```text
gpt-5-mini
```

Select the model.

You'll see information about its capabilities.

---

# Step 7: Deploy GPT-5-mini

Click:

**Deploy**

Use the **default settings**.

Wait for the deployment to finish.

After deployment, Foundry takes you to a **chat playground** where you can test the model.

### Important

Make sure the **Available Region** is the same region you selected when creating the project.

---

## What does deployment mean?

You are taking the model from the model catalog and making it available for your project.

Conceptually:

```text
Model Catalog
      ↓
GPT-5-mini
      ↓
Deploy
      ↓
Your Foundry Project
      ↓
Chat Playground
```

---

# If GPT-5-mini Cannot Be Deployed

The lab says model deployments are subject to regional quotas.

If you don't have enough quota for `gpt-5-mini`, you can use another chat-capable GPT model, such as:

```text
gpt-5-nano
```

or:

```text
gpt-5.4-mini
```

You can also create another project in a different region.

---

# PART 3 — Summarize Text

Now we are going to use GPT-5-mini for a real text-analysis task.

The task is:

```text
Summarization
```

---

# Step 8: Hide the Left Navigation

In the chat playground, use the button at the bottom of the left navigation pane to hide it.

This simply gives you more room to work.

---

# Step 9: Set the Instructions

On the left, find:

**Instructions**

Replace the default instructions with:

```text
You are an AI assistant that analyzes and summarizes text.
```

This tells the model what role it should perform.

---

# What is the purpose of this instruction?

You're telling GPT:

```text
Your role = Analyze and summarize text
```

So instead of simply saying:

```text
You are an AI.
```

you're giving it a specific purpose.

---

# Step 10: Give the Model a Text-Summarization Prompt

Enter:

```text
Summarize this review as a single short paragraph:
```

Then paste the review provided in the lab.

The review is about the:

```text
Commodore 64
```

The article describes its:

- 64K RAM
- colorful graphics
- sound capabilities
- SID sound generator
- software interest
- keyboard limitations
- documentation
- expensive peripherals
- overall position in the home-computer market

The complete review text is provided in the lab.

---

# Step 11: Read the Summary

Submit the prompt.

GPT-5-mini should generate a **single short paragraph** summarizing the review.

The important thing you're learning is that you can use a **general-purpose LLM** for text analysis simply by describing the task in natural language.

For example:

```text
Summarize this text.
```

You don't need to write a special summarization algorithm yourself.

---

# Why Are LLMs Good at Text Analysis?

The lab explains that LLMs are based on machine-learning techniques with roots in natural language processing and text analysis.

They can perform tasks such as:

```text
Summarization
       ↓
Named entity extraction
       ↓
Sentiment classification
       ↓
Topic classification
       ↓
Style analysis
```

---

# PART 4 — Use a Specialized Language Analysis Tool

Now the lab changes approach.

Instead of using a general-purpose LLM, we're going to use a **specialized language-analysis tool**.

The lab uses:

**Azure Language in Foundry Tools**

These are purpose-built analyzers that use statistical techniques to produce **structured and deterministic results**.

---

# General AI vs Specialized Tool

This distinction is extremely important.

### General-purpose LLM

You might ask:

```text
Summarize this document.
```

The LLM generates a natural-language answer.

```text
Input
 ↓
GPT-5-mini
 ↓
Generated text
```

### Specialized tool

For example:

```text
Detect the language of this text.
```

The specialized tool performs that specific operation and returns structured information.

```text
Input
 ↓
Azure Language
 ↓
Language detection result
```

---

# Step 12: Go to Build

In Microsoft Foundry:

1. Go to the menu at the top.
2. Select:

**Build**

---

# Step 13: Open Services

On the left side of the Build page:

1. Expand the menu if necessary.
2. Select:

**Services**

You'll see various AI services.

Microsoft Foundry Tools includes services supporting areas such as:

- Speech
- Translation
- Language
- Content understanding

---

# Step 14: Look at the Available Services

Look through the services.

The lab specifically points out Azure Language services for:

```text
Language detection
PII redaction
```

Now we're going to try both.

---

# PART 5 — Detect Language

Language detection answers:

> **What language is this text written in?**

This can be useful when an application receives text in an unknown language.

For example:

```text
User enters text
       ↓
Detect language
       ↓
English?
German?
French?
Spanish?
...
       ↓
Send to appropriate processing
```

The lab explains that language detection can be the first step in an analysis workflow so text can be routed to an appropriate model or agent.

---

# Step 15: Select Language Detection

In the AI services list, select:

**Azure Language - Language detection**

---

# Step 16: Select Sample Text

You'll see:

**Input text**

Select one of the provided sample documents.

Then click:

**Detect**

The service will determine the language of the sample.

---

# Step 17: Edit the Input

After looking at the result, click the **Edit** button.

Now you can:

- choose another sample
- type your own text
- upload a text file

The lab explicitly lists these options.

---

# Step 18: Try the Vintage Computer Example

The lab provides this text:

```text
CPC 464
Art.-Nr.: 31020
Serien-Nr.: 464-87-041256
220–240 V ~ 50 Hz
40 W
Hergestellt in Korea
SCHNEIDER RUNDFUNKWERKE AG
Türkheim/Unterallgäu
Bundesrepublik Deutschland
```

Enter it into the input area.

Then click:

**Detect**

The language detection service should identify the language.

Notice that most of the text consists of product information, numbers, and German words.

---

# Optional: Translation

The lab gives you a tip:

Foundry Tools also includes a **Text Translator** service.

So if you detect that text is in another language, you could use translation to convert it to another language.

Conceptually:

```text
Unknown Text
     ↓
Language Detection
     ↓
German
     ↓
Text Translator
     ↓
English
```

---

# PART 6 — Identify PII

Now we're going to perform another specialized text-analysis task:

**PII detection**

PII means:

> **Personally Identifiable Information**

This is information that can identify or provide personal details about an individual.

The lab gives examples such as:

- names
- addresses
- phone numbers
- email addresses
- other personal details

---

# Why Is PII Detection Important?

Organizations may need to protect personal information because of privacy policies and laws.

For example, imagine an invoice contains:

```text
Customer:
Margaret Ellis

Address:
128 High Street

Telephone:
021 685 4215
```

Before storing or sharing that document, you might want to identify and redact the personal information.

---

# Step 19: Open Text PII Redaction

On the language detection playground:

Find the:

**Type**

dropdown.

Select:

**Text PII Redaction**

Alternatively, return to the AI services list and select:

**Azure Language - Text PII Redaction**.

---

# Step 20: Select Sample Text

Under:

**Input text**

select one of the sample documents.

Then click:

**Detect**

The service will detect PII in the text.

---

# Step 21: Edit the Input

Click:

**Edit**

Now you can:

- choose another sample
- type your own text
- upload a text file

---

# Step 22: Enter the Invoice Example

The lab provides this example:

```text
Tailspin Toys Ltd
Invoice
14 September 1984

Customer:
Margaret Ellis
128 High Street, Reading, Berkshire RG1 2AB
Telephone: 021 685 4215

Item: ZX Spectrum 48K home computer (includes power supply, RF lead, and user manual)
Price: £79.00
Payment received: £79.00
```

Enter it into the input area.

Then click:

**Detect**

---

# What Should the Tool Find?

The lab says Azure Language can recognize an extensive list of PII.

Some examples specifically mentioned are:

```text
People names
Email addresses
Phone numbers
Street addresses
```

So in the invoice example, you should expect information such as:

```text
Margaret Ellis
128 High Street...
021 685 4215
```

to be recognized as personal information.

---

# Step 23: Experiment With Your Own Text

You can enter your own text and see what PII Azure Language recognizes.

The lab points you toward Microsoft's complete list of PII entity categories.

---

# PART 7 — Review the Python Code

Now the lab moves from the graphical interface to programming.

Foundry provides sample code that you can use as a starting point for creating your own application using Azure Language.

---

# Step 24: Open the Code Tab

On the right side, select:

**Code**

You'll see sample Python code for PII identification.

The sample looks like this:

```python
key = "<your-api-key>"
endpoint = "https://ai-resrce.cognitiveservices.azure.com/"

from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential
```

Let's understand it.

---

# Step 25: API Key

```python
key = "<your-api-key>"
```

This represents the authentication key for your Azure Language resource.

You would replace:

```text
<your-api-key>
```

with your actual key.

### What is an API key?

It is a credential that allows your application to authenticate with the service.

Conceptually:

```text
Python Application
       ↓
API Key
       ↓
Azure Language Service
```

---

# Step 26: Endpoint

```python
endpoint = "https://ai-resrce.cognitiveservices.azure.com/"
```

The endpoint tells your program **where the Azure Language service is located**.

Think:

```text
endpoint = service address
```

So:

```text
API Key
   +
Endpoint
   ↓
Azure Language Service
```

---

# Step 27: Import `TextAnalyticsClient`

```python
from azure.ai.textanalytics import TextAnalyticsClient
```

This imports the Azure Text Analytics client.

It gives your Python application methods for working with language-analysis functionality.

---

# Step 28: Import `AzureKeyCredential`

```python
from azure.core.credentials import AzureKeyCredential
```

This is used to create a credential object from your API key.

---

# Step 29: Create the Authentication Function

The sample defines:

```python
def authenticate_client():
    ta_credential = AzureKeyCredential(key)

    text_analytics_client = TextAnalyticsClient(
        endpoint=endpoint,
        credential=ta_credential
    )

    return text_analytics_client
```

Let's understand this step by step.

---

## `def authenticate_client():`

This creates a Python function called:

```text
authenticate_client
```

Its purpose is to create an authenticated Azure Language client.

---

## Create the credential

```python
ta_credential = AzureKeyCredential(key)
```

This takes your API key and turns it into a credential object.

Conceptually:

```text
API Key
   ↓
AzureKeyCredential
```

---

## Create Text Analytics Client

```python
text_analytics_client = TextAnalyticsClient(
    endpoint=endpoint,
    credential=ta_credential
)
```

This creates the client that will communicate with Azure Language.

You're providing:

```text
Endpoint
+
Credential
```

---

## Return the client

```python
return text_analytics_client
```

The function returns the authenticated client.

---

# Step 30: Authenticate

Then the code says:

```python
client = authenticate_client()
```

This calls the function.

So now:

```text
client
   ↓
Authenticated TextAnalyticsClient
   ↓
Can communicate with Azure Language
```

---

# PART 8 — PII Recognition Function

Now look at:

```python
def pii_recognition_example(client):
```

This defines a function that will perform PII recognition.

It receives:

```text
client
```

which is the authenticated Azure Language client.

---

# Step 31: Provide Documents

Inside the function:

```python
documents = [
    "$documents"
]
```

`$documents` represents the text/document that you're analyzing.

In a real application, this would be replaced by the actual text you want to analyze.

---

# Step 32: Recognize PII

The key line is:

```python
response = client.recognize_pii_entities(
    documents,
    language="en"
)
```

This tells Azure Language:

> Analyze these documents and identify personally identifiable information.

The language is specified as:

```text
en
```

which means English.

The lab's sample uses this exact PII recognition method.

---

# Step 33: Remove Errors From Results

Next:

```python
result = [doc for doc in response if not doc.is_error]
```

This keeps only the documents where the operation was successful.

In simple terms:

```text
Response
   ↓
Check each document
   ↓
Is there an error?
   ↓
No → keep it
Yes → ignore it
```

---

# Step 34: Print Redacted Text

Then:

```python
print("Redacted Text: {}".format(doc.redacted_text))
```

This prints a version of the text where detected PII has been redacted.

For example, conceptually:

```text
Original:

Margaret Ellis
128 High Street
021 685 4215


Redacted:

[PERSON]
[ADDRESS]
[PHONE NUMBER]
```

The exact output depends on what Azure Language detects.

---

# Step 35: Print Each Detected Entity

Next:

```python
for entity in doc.entities:
```

This loops through every PII entity that Azure detected.

For example:

```text
Margaret Ellis
128 High Street
021 685 4215
```

---

# Step 36: Print Entity Text

```python
print("Entity: {}".format(entity.text))
```

This prints the actual detected text.

For example:

```text
Entity: Margaret Ellis
```

---

# Step 37: Print Entity Category

```python
print(" Category: {}".format(entity.category))
```

This tells you what type of PII was detected.

For example:

```text
Category: Person
```

or another appropriate category.

---

# Step 38: Print Confidence Score

```python
print(" Confidence Score: {}".format(entity.confidence_score))
```

The confidence score indicates how confident the service is that the detected text belongs to that entity category.

Conceptually:

```text
0 → low confidence
1 → high confidence
```

The exact score is generated by the service.

---

# Step 39: Print Offset

```python
print(" Offset: {}".format(entity.offset))
```

The offset tells you where the detected entity occurs in the original text.

Think of the text as characters numbered:

```text
0 1 2 3 4 5 6 ...
```

The offset tells you where the entity starts.

---

# Step 40: Print Length

```python
print(" Length: {}".format(entity.length))
```

This tells you how many characters are included in the detected entity.

So together:

```text
Entity
Category
Confidence Score
Offset
Length
```

provide detailed information about each detected PII entity.

---

# Step 41: Run the Function

Finally:

```python
pii_recognition_example(client)
```

This calls your PII recognition function.

So the complete flow is:

```text
API Key
   ↓
AzureKeyCredential
   ↓
TextAnalyticsClient
   ↓
Authenticate
   ↓
Provide Document
   ↓
recognize_pii_entities()
   ↓
Azure Language
   ↓
Detect PII
   ↓
Redacted Text + Entity Details
```

---

# Complete Python Code With Beginner Comments

Here is the same sample from the lab, with comments added to explain what each part does:

```python
# Your Azure Language API key
key = "<your-api-key>"

# Your Azure Language service endpoint
endpoint = "https://ai-resrce.cognitiveservices.azure.com/"


# Import the client used to communicate with Azure Language
from azure.ai.textanalytics import TextAnalyticsClient

# Import the credential class used to authenticate with the API key
from azure.core.credentials import AzureKeyCredential


# Create an authenticated Azure Language client
def authenticate_client():

    # Convert the API key into an Azure credential
    ta_credential = AzureKeyCredential(key)

    # Create the Text Analytics client
    text_analytics_client = TextAnalyticsClient(
        endpoint=endpoint,
        credential=ta_credential
    )

    # Return the authenticated client
    return text_analytics_client


# Create the client
client = authenticate_client()


# Function for detecting PII
def pii_recognition_example(client):

    # Text/document that we want to analyze
    documents = [
        "$documents"
    ]

    # Ask Azure Language to detect PII
    response = client.recognize_pii_entities(
        documents,
        language="en"
    )

    # Keep only successful results
    result = [
        doc for doc in response
        if not doc.is_error
    ]

    # Process each successful document
    for doc in result:

        # Print the text with PII redacted
        print(
            "Redacted Text: {}".format(
                doc.redacted_text
            )
        )

        # Go through each detected PII entity
        for entity in doc.entities:

            # Print the detected text
            print(
                "Entity: {}".format(entity.text)
            )

            # Print what type of PII it is
            print(
                " Category: {}".format(entity.category)
            )

            # Print confidence score
            print(
                " Confidence Score: {}".format(
                    entity.confidence_score
                )
            )

            # Print where the entity starts in the text
            print(
                " Offset: {}".format(entity.offset)
            )

            # Print how long the entity is
            print(
                " Length: {}".format(entity.length)
            )


# Run the PII recognition function
pii_recognition_example(client)
```

The lab notes that you can copy this sample code and run it in a Python development environment such as Visual Studio Code, but you'll need environment variables for the Azure Language endpoint and key.

---

# PART 9 — The Most Important Concept of This Lab

This lab is mainly teaching you:

## General-purpose AI vs Specialized AI Service

### General-purpose LLM

Example:

```text
GPT-5-mini
```

You can tell it what you want using natural language.

For example:

```text
Summarize this document.
```

The model figures out how to perform the task.

```text
               GPT-5-mini
                   │
       ┌───────────┼───────────┐
       ↓           ↓           ↓
  Summarize    Classify    Extract
```

---

## Specialized Language Service

Azure Language has dedicated capabilities.

For example:

```text
Azure Language
      │
      ├── Language Detection
      │
      ├── PII Detection
      │
      └── PII Redaction
```

These are designed specifically for those tasks.

The lab describes these specialized analyzers as producing structured, deterministic results, which can be useful for consistent automated pipelines.

---

# When Should You Use Which?

A simple way to remember it:

| Requirement                          | Better approach                     |
| ------------------------------------ | ----------------------------------- |
| "Summarize this article"             | General-purpose LLM                 |
| "Explain this document"              | General-purpose LLM                 |
| "Rewrite this text"                  | General-purpose LLM                 |
| "Detect sentiment"                   | LLM or specialized language service |
| "Detect language"                    | Azure Language                      |
| "Find PII"                           | Azure Language                      |
| "Redact PII consistently"            | Azure Language                      |
| Need structured/deterministic output | Specialized tool                    |

The lab's main conclusion is that a generative AI model may provide all the NLP functionality needed in many scenarios, while specialized Azure Language tools are useful for more specific NLP requirements.

---

# Complete Architecture

Now connect everything you've learned:

```text
                  MICROSOFT FOUNDRY
                         │
                         ▼
                  Foundry Project
                         │
              ┌──────────┴──────────┐
              │                     │
              ▼                     ▼
        General AI Model       Azure Language
              │                     │
              ▼                     ├── Language Detection
          GPT-5-mini                │
              │                     └── PII Redaction
              │
              ▼
        Chat Playground
              │
              ▼
       Natural Language Prompt
              │
              ▼
       "Summarize this text"
              │
              ▼
        Generated Summary
```

And for the specialized tool:

```text
User Text
    │
    ▼
Azure Language
    │
    ├───────────────┐
    ▼               ▼
Language          PII
Detection        Detection
    │               │
    ▼               ▼
Language        Entities
Result          + Categories
                    │
                    ▼
              Redacted Text
```

---

# Final Summary of the Lab

You performed these steps:

### 1. Created a Foundry project

```text
project64911452
```

### 2. Deployed

```text
gpt-5-mini
```

### 3. Used GPT-5-mini to summarize text

```text
Commodore 64 review
        ↓
GPT-5-mini
        ↓
Short summary
```

### 4. Opened Foundry AI Services

```text
Build
 ↓
Services
```

### 5. Used Azure Language — Language Detection

You entered text and detected its language.

### 6. Used Azure Language — PII Redaction

You entered an invoice containing personal information and detected the PII.

### 7. Examined the Python code

You learned how a Python application can call Azure Language using:

```text
API Key
+
Endpoint
+
TextAnalyticsClient
```

### 8. Learned the key difference

```text
General-purpose LLM
        ↓
Flexible natural-language tasks

Specialized Azure Language
        ↓
Specific, structured, predictable NLP tasks
```

---

# One Sentence to Remember

> **Use a general-purpose LLM when you want flexible text understanding through natural-language prompts; use specialized Azure Language tools when you need a specific NLP capability with structured and predictable results.**
