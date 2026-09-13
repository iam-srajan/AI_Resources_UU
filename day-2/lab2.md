# Microsoft Foundry Lab — Complete Step-by-Step Guide

## 0. What are we going to build?

Before starting, understand the overall flow:

```text
Azure Subscription
       ↓
Resource Group
       ↓
Microsoft Foundry Resource
       ↓
Foundry Project
       ↓
Deploy GPT-5-mini
       ↓
Chat with Model
       ↓
Give Model Instructions
       ↓
Add Web Search Tool
       ↓
Add File Knowledge
       ↓
Save as AI Agent
       ↓
Preview Agent
       ↓
Connect Agent to Application using Python
```

So this lab gradually takes you from:

**"I have an LLM"**

to

**"I have an AI agent that has instructions, tools, knowledge, and can be called from an application."**

---

# Part 1 — Create a Microsoft Foundry Project

## Step 1: Open Microsoft Foundry

Open your browser and go to:

[Microsoft Foundry](https://ai.azure.com?utm_source=chatgpt.com)

Sign in using your **Azure credentials**.

If you see introductory pop-ups, tips, or quick-start windows, close them.

If necessary, click the **Foundry logo** in the top-left corner to return to the home page.

---

## Step 2: Enable New Foundry

At the top of the page, look for:

**New Foundry**

If it isn't enabled, turn it on.

This makes sure you're working with the newer Microsoft Foundry experience described by this lab.

---

# Step 3: Create the Project

If you don't already have a project, Foundry will ask you to create one.

Create a project with:

**Project name:**

```text
Project64909453
```

Then expand:

**Advanced options**

and configure the following:

| Setting          | Value                             |
| ---------------- | --------------------------------- |
| Project name     | `Project64909453`                 |
| Foundry resource | `Project64909453-resource`        |
| Subscription     | Your Azure subscription           |
| Resource group   | `ResourceGroup1`                  |
| Region           | Any AI Foundry recommended region |

These settings are specified by the lab.

### What does each thing mean?

This is important because you were asking about resources and resource groups previously.

Think of it like this:

```text
Azure Subscription
       │
       └── Resource Group
              │
              └── Foundry Resource
                     │
                     └── Foundry Project
                            │
                            ├── Models
                            ├── Agents
                            ├── Tools
                            └── Data
```

### Subscription

Your **Azure subscription** is the billing and resource-management boundary.

It is essentially where Azure resources are charged and managed.

### Resource Group

`ResourceGroup1` is a **container for Azure resources**.

For example:

```text
ResourceGroup1
   ├── Foundry Resource
   ├── Storage
   ├── Other Azure resources
   └── ...
```

### Foundry Resource

The lab calls this:

```text
Project64909453-resource
```

This is the Azure resource associated with Microsoft Foundry.

### Foundry Project

Your actual Foundry project is:

```text
Project64909453
```

The project is where you organize the AI-development assets used by your solution.

Microsoft describes Foundry projects as organizing models, resources, data, and other assets used to develop an AI solution.

---

# Step 4: Select a Region

For **Region**, choose an **AI Foundry recommended region**.

The lab specifically says to use a region from Microsoft's supported-region list.

### Important

You don't necessarily have to select your nearest geographical region.

You need a region that supports the model and services you want to use.

---

# Step 5: Create the Project

After entering the settings, create the project.

Wait for the project to finish creating.

It may take a few minutes.

Once finished, close any welcome dialogs.

You should now be inside your Foundry project.

---

# Part 2 — Deploy an LLM

Now we have a project.

Next, we need an **AI model**.

The lab uses:

```text
gpt-5-mini
```

The lab explains that an LLM is at the heart of an AI agent.

---

# Step 6: Open the Model Catalog

Inside your Foundry project:

1. Go to **Discover**.
2. Select **Models**.

You'll see the **Microsoft Foundry model catalog**.

This catalog contains models from providers such as Microsoft and OpenAI.

---

# Step 7: Find GPT-5-mini

Search for:

```text
gpt-5-mini
```

Select the model.

You'll see information about the model, including its capabilities.

---

# Step 8: Deploy the Model

Click:

**Deploy**

Use the default settings.

Wait for the deployment to complete.

The lab says deployment may take approximately a minute.

### What does "deploy a model" mean?

This is an important concept.

You are not creating the GPT model yourself.

Instead, you are making a particular model available for use inside your Foundry project.

Think:

```text
Model Catalog
     ↓
gpt-5-mini
     ↓
Deploy
     ↓
Your Foundry Project
     ↓
Your model deployment
```

---

## Important: Model Quota

The lab warns that model deployments are subject to **regional quotas**.

If you can't deploy `gpt-5-mini`, the lab suggests alternatives such as:

```text
gpt-5-nano
```

or

```text
gpt-5.4-mini
```

Alternatively, you can create a project in another region.

---

# Part 3 — Chat with the Model

Once the model is deployed, Foundry opens the **model playground**.

This is where you can interact with the model.

Think of Playground as:

> A testing environment where you can experiment with your AI model before building an actual application.

The lab describes the playground as a place where you can chat with the deployed model.

---

# Step 9: Hide the Navigation Panel

At the bottom of the left navigation panel, there is a button to hide it.

Click it.

This gives you more space for the chat interface.

---

# Step 10: Ask Your First Question

In the **Chat** pane, enter:

```text
Who was Ada Lovelace?
```

Then review the response.

You have now successfully interacted with your deployed LLM.

---

# Step 11: Ask a Follow-up Question

Now enter:

```text
Tell me more about her work with Charles Babbage.
```

Notice something interesting.

You didn't explicitly say:

```text
Tell me more about Ada Lovelace's work...
```

You said:

```text
her
```

The model understands that **"her" refers to Ada Lovelace** because the previous conversation is part of the context.

The lab specifically points out that generative AI chat applications often include conversation history in the prompt.

---

# Important Concept — Conversation History

Suppose you have:

```text
User:
Who was Ada Lovelace?

Model:
Ada Lovelace was...

User:
Tell me more about her work.
```

The model can understand **her** because the conversation contains:

```text
Ada Lovelace
```

Conceptually:

```text
Previous conversation
       ↓
   Context
       ↓
Current question
       ↓
      LLM
       ↓
   Response
```

---

# Step 12: Start a New Chat

At the top-right of the Chat pane, click:

**New chat**

This starts a new conversation and removes the previous conversation history.

---

# Step 13: Give a More Specific Prompt

Enter:

```text
List three facts about Ada Lovelace.
```

The response should now follow your explicit instruction.

The important lesson is:

> The way you write your prompt can influence the response.

The lab uses this example to demonstrate that LLM responses are generated dynamically rather than simply retrieved from a static database.

---

# Part 4 — Add Instructions

Now we're going to make the model behave differently.

Currently, you can ask the model almost anything.

But suppose you want an AI assistant that **only talks about computing history**.

That's where **instructions/system prompts** come in.

---

# Step 14: Start a New Chat

Click:

**New chat**

Again, this removes the previous conversation history.

---

# Step 15: Add a System Instruction

On the left side, find:

**Instructions**

Enter this:

```text
You are an expert in the history of computing and AI. You only answer questions about significant people and events in the development of computing, and about notable vintage computers. Do not engage in conversations on any topic that is unrelated to computing history.
```

This is the exact instruction given by the lab.

---

# What is a System Prompt?

A system prompt is a set of instructions that tells the model:

- who it should act as
- what it should focus on
- what it should answer
- what it shouldn't answer
- how it should behave

For example:

```text
SYSTEM INSTRUCTION
        ↓
"You are a computing historian."
        ↓
USER
"Tell me about ELIZA."
        ↓
MODEL
        ↓
Computing-history answer
```

---

# Step 16: Test the Instructions

Ask:

```text
Tell me about ELIZA.
```

The model should answer because ELIZA is related to computing history.

---

# Step 17: Ask a Follow-up

Ask:

```text
How does it compare with modern LLMs?
```

This should also be related to computing/AI.

The lab asks you to try this.

---

# Step 18: Ask an Off-topic Question

Now ask:

```text
What's the capital of Spain?
```

The model should refuse or avoid answering because your system instruction says:

> Only answer questions related to computing history.

This demonstrates how **instructions control the scope and behavior of an AI application**.

---

# Part 5 — Add Web Search

So far, the model mainly relies on the knowledge it learned during training.

But what if we need:

- current information
- recent information
- information from the web

For that, we can give the model a **tool**.

The lab uses the **Web search** tool.

---

# Step 19: Open Tools

On the left side, below the Instructions section, find:

**Tools**

Expand it if necessary.

---

# Step 20: Enable Web Search

Click:

**Add**

Then enable:

**Web search**

Read the information about the tool.

Now your model has access to a web-search capability.

Conceptually:

```text
User Question
      ↓
     LLM
      ↓
Need current information?
      ↓
 Web Search Tool
      ↓
    Web
      ↓
Search Results
      ↓
     LLM
      ↓
    Answer
```

---

# Step 21: Start a New Chat

Click:

**New chat**

This gives you a fresh conversation.

You should now see the `web_search` tool listed in the left panel.

---

# Step 22: Test Web Search

Enter:

```text
Find a vintage computer store near Seattle
```

The lab says you can use your local city instead.

The model should use web search to find relevant stores.

### Why is this different?

Without web search:

```text
Question
   ↓
LLM's existing knowledge
   ↓
Answer
```

With web search:

```text
Question
   ↓
LLM
   ↓
Web Search
   ↓
Current web information
   ↓
LLM
   ↓
Answer
```

---

# Part 6 — Add Knowledge From a File

Now we move to another very important AI concept:

**Grounding / RAG**

Suppose you have your company's private documents.

The model wasn't trained on those documents.

How can the model answer questions using them?

We can give it a **file search** capability.

The lab uses a file containing information about **vintage computer manufacturer serial numbers**.

---

# Step 23: Download the Knowledge File

The lab provides:

```text
vintage_computer_identifiers.docx
```

Download it to your computer.

The source file is provided by Microsoft Learning.

---

# Step 24: Upload the File

Return to your Foundry agent/model playground.

Go to:

**Tools**

Upload:

```text
vintage_computer_identifiers.docx
```

When prompted, create a **new index** using the default index name.

After the index is created, attach it to the agent/model.

---

# What is an Index?

This is a key concept.

Imagine your document contains:

```text
ASSY 250425 → Computer information
820-001A → Computer information
i386 → Processor/computer information
...
```

Instead of sending the entire document every time, the system creates an index that allows relevant information to be found efficiently.

Conceptually:

```text
DOCX FILE
   ↓
Create Index
   ↓
Searchable Knowledge
   ↓
User asks question
   ↓
Relevant information retrieved
   ↓
LLM uses it to answer
```

This is related to **Retrieval-Augmented Generation (RAG)**.

---

# Step 25: Start a New Chat

Click:

**New chat**

Then go to the Chat tab.

---

# Step 26: Ask About a PCB

Enter:

```text
I have a printed circuit board with the "ASSY 250425" on it. What can you tell me about it?
```

The model should use the information from the uploaded knowledge source.

---

# Step 27: Try More Questions

Try:

```text
What kind of computer does a PCB with "820-001A" come from?
```

Then:

```text
What about "i386"?
```

The lab explains that when relevant information exists in the file, the model can use it. If the file doesn't contain relevant information, the model may use its training knowledge or the web-search tool.

---

# Very Important — Understand the Difference

At this point your model has three major sources/capabilities:

### 1. Model knowledge

```text
LLM
 ↓
Training knowledge
```

### 2. Web search

```text
LLM
 ↓
Web Search
 ↓
Current web information
```

### 3. File search

```text
LLM
 ↓
File Search
 ↓
Your uploaded knowledge
```

So:

```text
                   ┌── Training Knowledge
                   │
User → LLM ────────┼── Web Search
                   │
                   └── File Search
```

That's a major idea demonstrated by this lab.

---

# Part 7 — Save the Configuration as an Agent

So far we've been configuring and testing a model.

Now we want to turn this configuration into an **AI agent**.

The lab explains that an agent encapsulates things such as:

- model
- instructions
- tools
- configuration

so applications can interact with the agent without having to recreate all those settings themselves.

---

# Step 28: Save as Agent

In the model playground, top-right:

Click:

**Save as agent**

When asked for the name, enter:

```text
computing-historian
```

Then create it.

---

# What Have We Created?

Previously:

```text
Model
```

Now:

```text
Agent
```

Your agent contains something conceptually like:

```text
computing-historian
       │
       ├── Model: gpt-5-mini
       │
       ├── Instructions
       │
       ├── Web Search
       │
       └── File Search
```

That's why an agent is more than just an LLM.

---

# Step 29: Look at YAML

After creating the agent, it opens in an agent playground.

On the right side, select:

**YAML**

You'll see the agent definition.

The lab shows that the YAML contains information such as:

```yaml
name: computing-historian
version: "1"

definition:
  kind: prompt
  model: gpt-5-mini

  instructions: ...

  tools:
    - type: web_search
    - type: file_search
```

The actual lab output also includes the file-search vector-store ID and other metadata.

---

# What Does This YAML Mean?

Don't worry about every line.

The important part is understanding the structure.

### Agent name

```yaml
name: computing-historian
```

Your agent is called:

**computing-historian**

### Model

```yaml
model: gpt-5-mini
```

The agent uses GPT-5-mini.

### Instructions

```yaml
instructions: ...
```

This contains your system instruction.

### Tools

```yaml
tools:
  - type: web_search
  - type: file_search
```

This tells you that your agent has access to:

```text
Web Search
File Search
```

---

# Step 30: Test the Agent

Switch back to:

**Chat**

Ask:

```text
Who are you?
```

The response should indicate that the agent understands its role as a **computing historian**.

---

# Part 8 — Preview the Agent

Now we're going to see how an actual user could interact with the agent.

---

# Step 31: Preview Web App

At the top of the Chat pane, find:

**Publish**

Open the dropdown.

Select:

**Preview web app**

A new browser tab opens with a basic chat interface for your agent.

---

# Step 32: Test the Web App

Enter:

```text
What can you tell me about the Altair 8800?
```

You should receive a response from your agent.

Now you're seeing something closer to a real AI application:

```text
User
 ↓
Web Chat UI
 ↓
AI Agent
 ↓
GPT-5-mini
 ↓
Tools / Knowledge
 ↓
Response
```

---

# Part 9 — Connect the Agent to Python

This is the final technical part of the lab.

You have created an agent.

Now imagine you want to build your own application:

```text
Python application
       ↓
Microsoft Foundry Agent
       ↓
GPT-5-mini
       ↓
Response
```

Foundry provides sample code to help you do this.

---

# Step 33: Open Call Agent

In the agent playground:

Switch from:

**Chat**

to:

**Call agent**

You'll see sample Python code.

---

# Step 34: Install the Required Library

The sample starts with:

```python
# pip install azure-ai-projects>=2.1.0
```

This tells you that the application needs the Azure AI Projects Python package.

---

# Step 35: Import Authentication

The code contains:

```python
from azure.identity import DefaultAzureCredential
```

This provides Azure authentication using an Entra ID identity.

---

# Step 36: Import AIProjectClient

The code contains:

```python
from azure.ai.projects import AIProjectClient
```

This gives your Python application a client for communicating with the Foundry project.

---

# Step 37: Specify the Endpoint

The sample contains an endpoint similar to:

```python
endpoint = "https://ai-resrce.services.ai.azure.com/api/projects/ai-project"
```

This identifies the Foundry project that your application is connecting to.

---

# Step 38: Create the Project Client

The code:

```python
project_client = AIProjectClient(
    endpoint=endpoint,
    credential=DefaultAzureCredential()
)
```

means:

> Create a client that connects my Python application to my Microsoft Foundry project using Azure/Entra ID authentication.

---

# Step 39: Identify Your Agent

The sample defines:

```python
my_agent = "computing-historian"
my_version = "1"
```

This means:

```text
Agent name = computing-historian
Agent version = 1
```

Your project can contain multiple agents, so the application needs to tell Foundry which agent it wants to use.

---

# Step 40: Get the OpenAI Client

The code:

```python
openai_client = project_client.get_openai_client()
```

gets an OpenAI-compatible client from your Foundry project.

Now your application can use the Responses API to interact with the agent.

---

# Step 41: Send a Prompt to the Agent

The code:

```python
response = openai_client.responses.create(
    input=[
        {
            "role": "user",
            "content": "Tell me what you can help with."
        }
    ],
    extra_body={
        "agent_reference": {
            "name": my_agent,
            "version": my_version,
            "type": "agent_reference"
        }
    }
)
```

The important idea is:

```text
Python Application
       ↓
Responses API
       ↓
computing-historian agent
       ↓
GPT-5-mini + instructions + tools
       ↓
Response
```

The agent is identified through `extra_body` using its name and version.

---

# Step 42: Print the Response

Finally:

```python
print(f"Response output: {response.output_text}")
```

prints the agent's response in your Python application.

---

# Why Does It Use Entra ID?

The lab makes an important security point here.

Because the application is connecting to a Foundry project that can contain privileged resources, the lab says **key-based authentication isn't supported for this connection**.

Instead, the application uses an **Entra ID identity** for authentication.

In simple terms:

```text
Your Python App
      ↓
Entra ID Authentication
      ↓
Microsoft Foundry
      ↓
Your Project
      ↓
Your Agent
```

---

# Complete Architecture of This Lab

Now let's put everything together.

```text
                         AZURE
                           │
                    Azure Subscription
                           │
                           ▼
                    Resource Group
                     ResourceGroup1
                           │
                           ▼
                  Foundry Resource
             Project64909453-resource
                           │
                           ▼
                 Foundry Project
                   Project64909453
                           │
             ┌─────────────┴─────────────┐
             │                           │
             ▼                           ▼
        Model Catalog              Project Assets
             │
             ▼
        GPT-5-mini
             │
             ▼
        Model Deployment
             │
             ▼
        Model Playground
             │
     ┌───────┼────────┐
     │       │        │
     ▼       ▼        ▼
Instructions Web     File
            Search   Search
                      │
                      ▼
              Knowledge File
                      │
                      ▼
                    Index
                      │
                      ▼
              Save as Agent
                      │
                      ▼
             computing-historian
                      │
          ┌───────────┴───────────┐
          │                       │
          ▼                       ▼
     Preview Web App         Python App
                                  │
                                  ▼
                         AIProjectClient
                                  │
                                  ▼
                           Agent Reference
                                  │
                                  ▼
                              Response
```

---

# What You Learned in This Lab

The lab is actually teaching several important AI concepts.

## 1. Foundry Project

A project organizes the assets needed for an AI solution.

```text
Project
 ├── Models
 ├── Agents
 ├── Data
 ├── Tools
 └── Other assets
```

This is explicitly the purpose of Foundry projects described in the lab.

---

## 2. Model

The LLM is the core reasoning/generation component.

In this lab:

```text
GPT-5-mini
```

---

## 3. Model Deployment

Deployment makes the selected model available for your project to use.

```text
Model Catalog
      ↓
GPT-5-mini
      ↓
Deploy
      ↓
Project
```

---

## 4. Prompt

A prompt is what the user asks.

Example:

```text
Who was Ada Lovelace?
```

---

## 5. Conversation Context

Previous messages can be included as context.

That's why:

```text
User: Who was Ada Lovelace?

User: Tell me about her work.
```

can work even though the second question only says **her**.

---

## 6. System Instructions

Instructions control the model's behavior.

For example:

```text
You are an expert in computing history.
```

They can establish:

- role
- scope
- style
- restrictions
- behavior

The lab demonstrates this by preventing the model from answering unrelated questions.

---

## 7. Tool

A tool gives the model an additional capability.

In this lab:

```text
Web Search
```

allows the agent to access web information.

---

## 8. Knowledge / File Search

A file can provide specialized knowledge.

```text
vintage_computer_identifiers.docx
```

is indexed so relevant information can be retrieved when the user asks about computer identifiers.

---

## 9. RAG

The file-search portion demonstrates the basic idea of **Retrieval-Augmented Generation**:

```text
User Question
      ↓
Search Knowledge
      ↓
Retrieve Relevant Information
      ↓
Give Information to LLM
      ↓
Generate Answer
```

---

## 10. Agent

An agent packages together:

```text
Model
+
Instructions
+
Tools
+
Knowledge
```

The lab's `computing-historian` agent demonstrates this concept.

---

# The Most Important Difference: Model vs Agent

This lab is especially useful for understanding this.

### Model

```text
GPT-5-mini
```

is the underlying AI model.

It generates responses.

### Agent

```text
computing-historian
```

is a configured AI experience built around the model.

It can have:

```text
Agent
 │
 ├── Model
 ├── Instructions
 ├── Web Search
 └── File Search
```

So you can think:

> **Model = brain**

> **Instructions = behavior/rules**

> **Tools = capabilities**

> **Knowledge = additional information**

> **Agent = the complete configured AI worker**

---

# Final Clean-Up

When you finish the lab, delete the Azure resources so you don't continue using resources unnecessarily.

The lab specifically recommends deleting the resource group.

## Step 43: Open Azure Portal

Open:

[Azure Portal](https://portal.azure.com?utm_source=chatgpt.com)

Sign in.

---

## Step 44: Find Resource Group

Go to:

**Resource groups**

Find:

```text
ResourceGroup1
```

Open it.

---

## Step 45: Delete Resource Group

On the toolbar, select:

**Delete resource group**

Enter the resource group name to confirm.

Then confirm deletion.

### Why delete the Resource Group?

Because the resources created for this lab are inside it.

Deleting the resource group removes the resources contained in it, helping avoid unnecessary Azure charges.

---

# One-Line Summary of the Entire Lab

The whole lab can be remembered as:

> **Create Foundry Project → Deploy GPT-5-mini → Chat → Add Instructions → Add Web Search → Add File Knowledge → Save as Agent → Preview Agent → Connect Agent from Python → Clean Up.**

And the conceptual progression is:

```text
LLM
 ↓
LLM + Prompt
 ↓
LLM + Instructions
 ↓
LLM + Instructions + Web Search
 ↓
LLM + Instructions + Web Search + Knowledge
 ↓
        AGENT
 ↓
Agent + Web Application
 ↓
Agent + Python Application
```

That is the core story of this lab.
