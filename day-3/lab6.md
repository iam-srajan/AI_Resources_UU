# Microsoft Foundry – Azure Content Understanding

In this exercise, you will learn how Microsoft Foundry can take **unstructured content** such as documents and images and convert it into **structured information**, such as text, tables, fields, and JSON.

You will learn three important analyzers:

1. **Read / OCR** → Extract text
2. **Layout** → Understand document structure
3. **Receipt** → Extract useful fields from receipts

The exercise also shows how to do the same work using the **Python SDK**.

---

# Part 1: Create a Microsoft Foundry Project

## Step 1: Open Microsoft Foundry

Open:

[Microsoft Foundry](https://ai.azure.com?utm_source=chatgpt.com)

Sign in using your Azure credentials.

---

## Step 2: Enable New Foundry

At the top of the Foundry page, find:

**New Foundry**

If it is not enabled, turn it on.

---

## Step 3: Create the Project

Create a new project with a unique name.

The exercise uses:

```text
Project64997983-resource
```

Use:

| Setting              | Value                                                                |
| -------------------- | -------------------------------------------------------------------- |
| **Foundry resource** | `Project64997983-resource`                                           |
| **Subscription**     | Your Azure subscription                                              |
| **Resource group**   | `ResourceGroup1`                                                     |
| **Region**           | West US / Sweden Central / Australia East / another supported region |

> **Important:** The available regions can depend on your subscription, model availability, and Azure quota.

Wait for the project to finish creating. It can take a few minutes.

---

# Part 2: What is Azure Content Understanding?

**Azure Content Understanding** is a Foundry service that uses AI to understand different types of content.

It can work with:

- 📄 Documents
- 🖼️ Images
- 🎥 Video
- 🎵 Audio

It converts this unstructured content into **structured information**, such as:

- Text
- Fields
- Tables
- JSON
- Confidence scores
- Information about where the data came from

### Simple example

Suppose you have a receipt:

```text
        RECEIPT
-----------------------
ABC Store
Date: 13/09/2026
Phone: 9876543210

Milk       $5
Bread      $3
-----------------------
Total      $8
```

Content Understanding can turn this into something like:

```json
{
  "company": "ABC Store",
  "date": "13/09/2026",
  "phone": "9876543210",
  "total": "$8"
}
```

So the main idea is:

```text
Unstructured Document
        ↓
Azure Content Understanding
        ↓
Structured Data
        ↓
JSON / Fields / Tables
```

---

# Part 3: Open Content Understanding

In Microsoft Foundry:

### Step 1

Go to:

**Build**

### Step 2

On the left side, open:

**Services**

### Step 3

Find:

**Content Understanding**

Select it.

This opens the **Content Understanding Playground**.

---

# Part 4: OCR – Read Text From an Image

## What is OCR?

**OCR** means:

> **Optical Character Recognition**

OCR allows a computer to read text that appears inside an image.

For example:

```text
Image
┌───────────────────┐
│  ABC COMPUTER     │
│  MODEL: ZX-200    │
│  SERIAL: 12345    │
└───────────────────┘
          ↓
         OCR
          ↓
ABC COMPUTER
MODEL: ZX-200
SERIAL: 12345
```

Instead of manually typing the text, AI extracts it automatically.

The exercise uses OCR to read text printed on computer hardware and other objects.

---

# Part 5: Run OCR Analysis

In Content Understanding:

### Step 1

Select:

**OCR/Read**

### Step 2

Make sure:

**Modality → Document**

is selected.

### Step 3

Make sure the analyzer is:

**OCR/Read**

### Step 4

Select one of the sample images.

### Step 5

Click:

**Run analysis**

The service will extract the text from the image.

---

# Part 6: Understand the OCR Results

After analysis, you can view different result tabs:

- **Markdown**
- **Paragraphs**
- **Result**

These show the information extracted from the document in different formats.

### Think of it like this:

```text
Image
  ↓
OCR
  ↓
Text
  ↓
Markdown / Paragraphs / JSON Result
```

---

# Part 7: Analyze PCB Images

The exercise also provides images of **printed circuit boards (PCBs)** containing text.

Download:

[PCB images](https://aka.ms/pcb-images?utm_source=chatgpt.com)

The download contains `pcbs.zip`.

Extract it to your computer.

Then:

1. Upload a PCB image.
2. View the image.
3. Click **Run analysis**.
4. Review the extracted text.
5. Repeat with the other PCB images.

### Simple idea

```text
PCB Image
    ↓
OCR
    ↓
Text printed on PCB
    ↓
AI-readable text
```

---

# Part 8: Layout Analyzer

OCR only focuses mainly on **reading text**.

But sometimes you also need to understand **where the text appears and how the document is organized**.

That's where **Layout** comes in.

The Layout analyzer can identify things such as:

- Text
- Paragraphs
- Tables
- Structure
- Hierarchy

---

## Run Layout Analysis

In the analyzer list:

1. Select **Layout**.
2. Select a sample image.
3. Click **Run analysis**.

Then examine:

- **Markdown**
- **Paragraphs**
- **Tables**
- **Result**

---

# Part 9: OCR vs Layout

This is an important concept.

| Analyzer       | What it does                                    |
| -------------- | ----------------------------------------------- |
| **OCR / Read** | Reads the text                                  |
| **Layout**     | Reads text + understands document structure     |
| **Receipt**    | Reads text + identifies specific receipt fields |

Think of it as increasing intelligence:

```text
                 READ
                  ↓
             Text extraction
                  ↓
                LAYOUT
                  ↓
       Text + document structure
                  ↓
               RECEIPT
                  ↓
       Text + structure + fields
```

The exercise describes these three analyzers as building on each other in capability.

---

# Part 10: Extract Fields From a Receipt

Now we will do something more useful.

Suppose a company receives thousands of receipts.

Manually entering information would be slow.

For example:

```text
Receipt
   ↓
Read text
   ↓
Find company name
   ↓
Find date
   ↓
Find amount
   ↓
Find phone number
   ↓
Store information
```

Content Understanding can automate this process.

The exercise uses a **Receipt analyzer** to extract fields from scanned receipts.

---

# Part 11: Select Receipt Analyzer

In the analyzer types:

### Step 1

Select:

**Procurement**

### Step 2

Select:

**Receipt**

### Important

The exercise says that field extraction requires a **custom model**, so Foundry may ask you to deploy models.

For this exercise:

> Click **Cancel** if that deployment prompt appears.

Do **not** run the analysis because the exercise provides pre-prepared analysis results.

---

# Part 12: Understand Receipt Results

In the results area, you can see:

- **Fields**
- **Markdown**
- **Paragraphs**
- **Result**

The most important one for an application is **Fields**.

### Why?

The **Result** contains raw JSON.

The **Fields** tab shows the information in a more user-friendly way.

The exercise explains that the Fields tab represents the kind of information a client application would receive from the analysis.

---

# Part 13: Example of Structured Receipt Data

Imagine the original receipt contains:

```text
ABC Store

Date: 13/09/2026

Milk       $5
Bread      $3

Total      $8
```

Instead of receiving just text, the AI can identify individual fields:

```text
Company → ABC Store
Date    → 13/09/2026
Milk    → $5
Bread   → $3
Total   → $8
```

This is much more useful for an application.

For example:

```text
Receipt
   ↓
Content Understanding
   ↓
Fields
   ↓
Expense Application
   ↓
Database
```

---

# Part 14: Python SDK

You don't have to use only the Foundry Playground.

As a developer, you can use the **Python SDK** to perform Content Understanding analysis from your own application.

The Python SDK allows your application to:

1. Connect to Content Understanding.
2. Send a document.
3. Select an analyzer.
4. Analyze the document.
5. Receive the result.
6. Work with the JSON output.

---

# Part 15: Install the Required Libraries

The exercise requires:

**Python 3.9 or later.**

Install the required packages:

```bash
python -m pip install --pre azure-ai-contentunderstanding azure-identity
```

You can also create a virtual environment.

### Windows

```bash
python -m venv .venv
```

Then:

```bash
.venv\Scripts\activate
```

### Linux/macOS

```bash
python -m venv .venv
```

Then:

```bash
source .venv/bin/activate
```

---

# Part 16: Important Python Libraries

The code imports:

```python
import json
```

Used to work with JSON data.

Then:

```python
from azure.ai.contentunderstanding import ContentUnderstandingClient
```

This is the main client used to communicate with Azure Content Understanding.

Then:

```python
from azure.ai.contentunderstanding.models import (
    AnalysisInput,
    AnalysisResult
)
```

These represent the input and analysis result.

Then:

```python
from azure.core.credentials import AzureKeyCredential
```

Used when authenticating with an API key.

And:

```python
from azure.identity import DefaultAzureCredential
```

Used for Azure identity-based authentication.

---

# Part 17: Important Configuration Values

There are three main values you need to configure:

### 1. Endpoint

```python
endpoint = "https://project64997983-resource.services.ai.azure.com/"
```

The endpoint tells your Python program **where your Content Understanding resource is located**.

### 2. API Key

```python
key = "{{CONTENT_UNDERSTANDING_KEY}}"
```

This is your Content Understanding API key.

However, the exercise says the key is optional when using:

```python
DefaultAzureCredential()
```

### 3. File URL

```python
file_url = "{{FILE_URL}}"
```

This is the URL of the document you want to analyze.

---

# Part 18: Analyzer ID

You also specify which analyzer you want to use.

For example:

```python
analyzer_id = "prebuilt-read"
```

This means:

> Use the prebuilt Read analyzer.

For the earlier receipt example, the code uses:

```python
analyzer_id = "prebuilt-receipt"
```

So:

```text
prebuilt-read
      ↓
Extract text

prebuilt-receipt
      ↓
Extract receipt information
```

---

# Part 19: API Version

The code also specifies an API version:

```python
api_version = "2026-06-01-preview"
```

This tells Azure which version of the Content Understanding API the application should use.

> **Note:** The exact API version can change as the service evolves. Use the version provided by your current Foundry sample/documentation.

---

# Part 20: Create the Azure Credential

The code supports two authentication methods.

### API Key

```python
AzureKeyCredential(key)
```

### Azure Identity

```python
DefaultAzureCredential()
```

The code chooses between them:

```python
credential = (
    AzureKeyCredential(key)
    if key and "{{CONTENT_UNDERSTANDING_KEY}}" not in key
    else DefaultAzureCredential()
)
```

In simple language:

```text
Is a real API key provided?
       │
   ┌───┴───┐
  YES      NO
   ↓        ↓
API Key   Azure Login
          DefaultAzureCredential
```

---

# Part 21: Create the Content Understanding Client

```python
client = ContentUnderstandingClient(
    endpoint=endpoint,
    credential=credential,
    api_version=api_version
)
```

This creates the connection between your Python application and Azure Content Understanding.

Think:

```text
Python Program
      ↓
ContentUnderstandingClient
      ↓
Azure Content Understanding
```

---

# Part 22: Start Document Analysis

The important code is:

```python
poller = client.begin_analyze(
    analyzer_id=analyzer_id,
    inputs=[AnalysisInput(url=file_url)],
)
```

This tells Azure:

> Analyze this file using this analyzer.

For example:

```text
File
 ↓
prebuilt-read
 ↓
Content Understanding
 ↓
Analysis
```

---

# Part 23: Why Is There a Poller?

The analysis runs **asynchronously**.

That means the analysis may take some time.

Instead of making the application wait without control, Azure returns a `poller`.

Then:

```python
result = poller.result()
```

waits for the analysis to finish and gets the final result.

The source specifically explains that the analyzer runs asynchronously and returns the analysis results in JSON format.

Simple flow:

```text
Start Analysis
      ↓
   Poller
      ↓
Wait for completion
      ↓
Final Result
```

---

# Part 24: Handle Errors

The code handles Azure errors:

```python
except AzureError as err:
    print(f"[Azure Error]: {err.message}")
```

It also handles unexpected errors:

```python
except Exception as ex:
    print(f"[Unexpected Error]: {ex}")
```

This is useful because your application won't simply crash without explaining what happened.

---

# Part 25: Convert Result to JSON

The code uses:

```python
result_str = json.dumps(
    result.as_dict(),
    indent=2
)
```

This converts the result into a readable JSON string.

For example:

```json
{
  "document": {
    "text": "ABC Store",
    "date": "13/09/2026"
  }
}
```

JSON is useful because applications can easily work with structured data.

---

# Part 26: Display the Result

The code prints the analysis:

```python
print("Analysis result:")
print("=" * 50)

print(result_str)
```

The sample limits the display to 50 lines when the result is very large.

---

# Part 27: Complete Beginner Python Example

Here is the important structure of the code in a cleaner format:

```python
import json

from azure.ai.contentunderstanding import ContentUnderstandingClient
from azure.ai.contentunderstanding.models import (
    AnalysisInput,
    AnalysisResult
)
from azure.core.credentials import AzureKeyCredential
from azure.identity import DefaultAzureCredential


endpoint = "https://project64997983-resource.services.ai.azure.com/"

key = "{{CONTENT_UNDERSTANDING_KEY}}"

file_url = "{{FILE_URL}}"

analyzer_id = "prebuilt-read"

api_version = "2026-06-01-preview"


# Choose authentication method
credential = (
    AzureKeyCredential(key)
    if key and "{{CONTENT_UNDERSTANDING_KEY}}" not in key
    else DefaultAzureCredential()
)


# Create client
client = ContentUnderstandingClient(
    endpoint=endpoint,
    credential=credential,
    api_version=api_version
)


# Start analysis
poller = client.begin_analyze(
    analyzer_id=analyzer_id,
    inputs=[
        AnalysisInput(url=file_url)
    ]
)


# Wait for analysis to finish
result: AnalysisResult = poller.result()


# Convert result to JSON
result_str = json.dumps(
    result.as_dict(),
    indent=2
)


# Print result
print("Analysis result:")
print("=" * 50)
print(result_str)
```

The structure above follows the Python SDK sample provided in the exercise.

---

# Part 28: Understand the Complete Python Flow

The entire program can be remembered like this:

```text
             Python Program
                    │
                    ▼
             Azure Credentials
                    │
                    ▼
        ContentUnderstandingClient
                    │
                    ▼
               File URL
                    │
                    ▼
             Analyzer ID
                    │
                    ▼
           begin_analyze()
                    │
                    ▼
                 Poller
                    │
                    ▼
            poller.result()
                    │
                    ▼
             Analysis Result
                    │
                    ▼
                 JSON
```

---

# Part 29: Read vs Layout vs Receipt

This is probably the **most important concept from this exercise**.

### 1. Read / OCR

Purpose:

> **Extract text**

```text
Image/Document
      ↓
     OCR
      ↓
Raw Text
```

Example:

```text
"ABC STORE
TOTAL $50"
```

---

### 2. Layout

Purpose:

> **Extract text + understand structure**

```text
Document
   ↓
Layout
   ↓
Text + Paragraphs + Tables + Structure
```

---

### 3. Receipt

Purpose:

> **Extract specific information from receipts**

```text
Receipt
   ↓
Receipt Analyzer
   ↓
Company
Date
Amount
Phone
Other fields
```

So remember:

```text
READ
 ↓
Text

LAYOUT
 ↓
Text + Structure

RECEIPT
 ↓
Text + Structure + Specific Fields
```

This progression is exactly how the exercise describes the increasing capabilities of the three analyzers.

---

# Part 30: What Did You Learn?

You learned how **Azure Content Understanding** can transform unstructured content into structured data.

You practiced:

### 📖 Read / OCR

Extract text from images and documents.

### 📐 Layout

Understand the structure of a document, including paragraphs and tables.

### 🧾 Receipt

Extract meaningful fields from receipts.

### 🐍 Python SDK

Use Python to perform document analysis programmatically.

---

# Part 31: Real-World Example

Imagine a company receives **10,000 invoices every month**.

Without AI:

```text
Invoice
   ↓
Employee reads invoice
   ↓
Employee types company name
   ↓
Employee types date
   ↓
Employee types amount
   ↓
Employee enters data into database
```

This takes a lot of time.

With Content Understanding:

```text
10,000 Invoices
       ↓
Azure Content Understanding
       ↓
Extract text
       ↓
Identify fields
       ↓
Structured JSON
       ↓
Company Database
```

This is one of the main reasons document understanding is useful in real applications.

---

# Part 32: Clean Up Azure Resources

When you finish the exercise, delete resources you no longer need to avoid unnecessary Azure costs.

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
Confirm
```

The exercise specifically instructs you to delete the resource group containing the resources you created.

---

# ⭐ One-Line Memory Trick

Remember the whole exercise with these three sentences:

> **OCR reads the text.**
> **Layout understands the structure.**
> **Receipt understands the fields.**

And the developer side is:

> **Python SDK → send document → choose analyzer → wait for analysis → receive JSON.**
