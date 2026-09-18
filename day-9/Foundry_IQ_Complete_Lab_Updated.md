# Integrate an AI Agent with Microsoft Foundry IQ

## Overview

> **Lab update (September 2026):** This version adds the missing Azure
> AI Search IAM configuration for the Microsoft Foundry **project
> managed identity**. The project identity must have **Search Index Data
> Reader** on the Azure AI Search service for the current Foundry IQ/MCP
> access path. This update addresses the common `HTTP 403 Forbidden`
> error seen when the role is assigned only to the parent Foundry
> resource.

In this exercise, you will create an AI agent in **Microsoft Foundry**
that can search information stored in a knowledge base using **Foundry
IQ**.

You will then connect to the agent from **Visual Studio Code** using
Python.

### What you will learn

By the end of this lab, you will know how to:

1.  Create a Microsoft Foundry project.
2.  Create an AI agent.
3.  Create an Azure AI Search resource.
4.  Upload product documents.
5.  Create a Foundry IQ knowledge base.
6.  Connect the knowledge base to an agent.
7.  Test the agent in the Foundry playground.
8.  Configure tool approval.
9.  Connect to the agent from Python.
10. Handle MCP approval requests.
11. Maintain conversation history.
12. Test RAG-style knowledge retrieval.

> **Note:** Some technologies used in this exercise may be in preview or
> active development, so warnings or unexpected behavior are possible.

------------------------------------------------------------------------

# 1. Prerequisites

Before starting, make sure you have:

-   An Azure subscription with permission to create AI resources.
-   Visual Studio Code.
-   Python 3.13.
-   Git.
-   Basic knowledge of Python.
-   Basic familiarity with Microsoft Foundry.

## Python Version

Use **Python 3.13** for this lab.

``` text
Python 3.13.x
```

> **Important:** Python 3.14 is not supported by some dependencies used
> in this lab. The original lab was tested with Python 3.13.12.

------------------------------------------------------------------------

# 2. Create a Microsoft Foundry Project

Open the Microsoft Foundry portal:

``` text
https://ai.azure.com
```

Sign in with your Azure/lab account.

> **Security note:** Do not store usernames or passwords directly in
> your source code or Markdown files. Use the credentials provided by
> your lab environment only through the appropriate sign-in process.

------------------------------------------------------------------------

## Enable the New Foundry Experience

Make sure:

``` text
New Foundry = ON
```

When asked to select a project:

``` text
Create a new project
```

------------------------------------------------------------------------

## Project Settings

Use values similar to:

  SettingValue       
  ------------------ --------------------------------
  Project name       `agent-iq-lab`
  Foundry resource   `agent-iq-lab-resource`
  Subscription       Your Azure subscription
  Resource group     Your lab resource group
  Location           Any available supported region

> **Important:** Some Azure AI resources have regional quotas. If you
> encounter a quota problem, try another supported region.

Select:

``` text
Create
```

Wait until the project has been created.

------------------------------------------------------------------------

# 3. Create an AI Agent

After the project is created:

1.  Open the project.
2.  Select **Build**.
3.  Select **Agents**.
4.  Select **Create agent**.

Give the agent a descriptive name.

Example:

``` text
product-expert-agent
```

When the agent is created, the default model will be selected
automatically.

------------------------------------------------------------------------

# 4. Configure the Agent

Open the agent configuration.

Give the agent the following instructions:

``` text
You are a helpful AI assistant for Contoso, specializing in outdoor camping and hiking products.

You must ALWAYS search the knowledge base to answer questions about our products or product catalog.

Provide detailed, accurate information and always cite your sources.

If you don't find relevant information in the knowledge base, say so clearly.
```

Save the agent configuration.

------------------------------------------------------------------------

# 5. Connect Foundry IQ

In the agent's **Knowledge** section:

``` text
Add
   ↓
Connect to Foundry IQ
```

In the Foundry IQ setup window:

``` text
Connect to an AI Search resource
```

Then select:

``` text
Create new resource
```

------------------------------------------------------------------------

# 6. Create an Azure AI Search Resource

Use settings similar to:

  SettingValue     
  ---------------- ---------------------------------------
  Resource name    `ai-search-lab`
  Subscription     Your Azure subscription
  Resource group   Same as the Foundry project
  Region           Same as the Foundry project
  Pricing tier     Free if available, otherwise Standard

Create the resource.

> If resource creation fails inside Foundry, you can create the Azure AI
> Search resource directly from the Azure portal.

------------------------------------------------------------------------

# 7. Download the Product Documents

The lab provides sample Contoso product information.

Download:

``` text
https://github.com/MicrosoftLearning/mslearn-ai-agents/raw/main/Labfiles/04-integrate-agent-with-foundry-iq/data/contoso-products.zip
```

Extract the ZIP file.

You should have approximately three PDF documents containing Contoso
product information.

------------------------------------------------------------------------

# 8. Create an Azure Storage Account

Open:

``` text
https://portal.azure.com
```

Search for:

``` text
Storage accounts
```

Select **Storage accounts**.

Create a new storage account.

Use settings similar to:

  SettingValue           
  ---------------------- ---------------------------------
  Subscription           Your Azure subscription
  Resource group         Same resource group
  Storage account name   A unique lowercase name
  Region                 Same as project
  Primary service        Azure Blob Storage
  Performance            Standard
  Redundancy             Locally-redundant storage (LRS)

> Storage account names must be globally unique and follow Azure naming
> rules. If the example name is already taken, use another unique name.

------------------------------------------------------------------------

# 9. Create a Blob Container

Open your storage account.

Select:

``` text
Upload
```

Create a container:

``` text
contosoproducts
```

Upload all three PDF documents.

The structure will look approximately like:

``` text
Storage Account
│
└── contosoproducts
      │
      ├── product1.pdf
      ├── product2.pdf
      └── product3.pdf
```

------------------------------------------------------------------------

# 10. Configure Azure AI Search Access

Open the Azure AI Search resource.

Navigate to:

``` text
Security + networking
    ↓
Keys
```

## 10.1 Configure API access control

Under **API access control**, use:

``` text
Both
```

or, if your lab is using only Microsoft Entra ID/RBAC:

``` text
Role-based access control
```

For this lab, **Both** is convenient because the Foundry IQ connection
may also use key authentication.

> **Important:** The exact portal labels can change. The important
> requirement is that the Search service allows the authentication
> method used by Foundry IQ.

------------------------------------------------------------------------

## 10.2 Enable the Foundry Project managed identity

This is a **required step for the current Foundry IQ/MCP setup**.

Open the **Microsoft Foundry project** in the Azure portal.

Go to:

``` text
Foundry Project
    ↓
Identity
```

Under **System assigned**, set:

``` text
Status: On
```

Select:

``` text
Save
```

After saving, copy or note the project's managed identity name/object ID
if needed.

> **Important:** Assign the Search permission to the **Foundry Project
> managed identity**, not only to the parent Foundry resource identity.

------------------------------------------------------------------------

## 10.3 Grant the Foundry Project permission on Azure AI Search

Return to:

``` text
Azure AI Search
    ↓
Access control (IAM)
```

Select:

``` text
Add
    ↓
Add role assignment
```

For the role, select:

``` text
Search Index Data Reader
```

Select:

``` text
Next
```

For **Assign access to**, choose:

``` text
Managed identity
```

Select:

``` text
Select members
```

Choose the Microsoft Foundry project and select the **project managed
identity** you enabled in the previous step.

Then:

``` text
Review + assign
```

The final relationship should be:

``` text
Microsoft Foundry Project
        |
        | System-assigned managed identity
        |
        v
Azure AI Search
        |
        | Search Index Data Reader
        v
Knowledge Base / Search Index
```

### Why this step is required

The Foundry IQ MCP endpoint can return:

``` text
HTTP 403 Forbidden
```

if the project's managed identity does not have permission to read the
Search index.

Microsoft's current Foundry IQ documentation specifies that the
**Foundry project's managed identity** should have the **Search Index
Data Reader** role on the Azure AI Search service.

------------------------------------------------------------------------

## 10.4 Optional: Search service managed identity

If your knowledge base is configured to use an LLM for query planning or
answer synthesis, the Search service may also need its own
**system-assigned managed identity** and appropriate permissions on the
Foundry parent resource.

For the common lab scenario, first make sure the **Foundry Project →
Search Index Data Reader** permission is configured correctly.

If the knowledge base fails during answer synthesis after retrieval is
working, check the Search service managed identity and the **Cognitive
Services User** role as described in the Microsoft Foundry IQ
documentation.

------------------------------------------------------------------------

## 10.5 Configure the Foundry IQ Search connection

Keep the Azure portal open because you may need the Search key.

Return to Microsoft Foundry and refresh the page.

Go to:

``` text
Knowledge
    ↓
Manage
    ↓
Connected resources
```

Select your Azure AI Search service.

Open the authentication settings.

If your lab UI provides:

``` text
Key authentication
```

select it and choose:

``` text
Edit authentication
```

From the Azure portal, copy the appropriate Azure AI Search key.

Paste it into the Foundry authentication dialog.

Select:

``` text
Save
```

> **Important:** Key authentication in the connection does not replace
> the project IAM configuration required by the current MCP/agent access
> path. Keep the **Foundry Project managed identity → Search Index Data
> Reader** assignment in place.

------------------------------------------------------------------------

## 10.6 Wait for RBAC propagation

After assigning the role, wait a few minutes and refresh Microsoft
Foundry.

If you still receive:

``` text
403 Forbidden
```

check:

1.  The role was assigned to the **Foundry Project managed identity**.
2.  The role is **Search Index Data Reader**.
3.  The role was assigned on the correct Azure AI Search resource.
4.  The Search service API access control allows the selected
    authentication mode.
5.  The Foundry project and Search service are in the expected
    subscription/tenant.
6.  The knowledge base is active.

Do **not** recreate the PDFs or index just because the MCP endpoint
returns 403.

------------------------------------------------------------------------

# 11. Create the Foundry IQ Knowledge Base

In Microsoft Foundry:

``` text
Knowledge
    ↓
Create a knowledge base
```

Choose:

``` text
Azure Blob Storage
```

Then select:

``` text
Connect
```

------------------------------------------------------------------------

## Knowledge Source Configuration

Use settings similar to:

  SettingValue             
  ------------------------ ---------------------------------
  Name                     `ks-contosoproducts`
  Description              `Contoso product catalog items`
  Storage account          Your storage account
  Container                `contosoproducts`
  Authentication           API Key
  Content extraction       Minimal
  Embedding model          Available embedding model
  Chat completions model   Available deployed model

For example, the lab may show models similar to:

``` text
text-embedding-3-small
```

and:

``` text
gpt-5
```

> The exact available models can vary depending on your Azure
> environment and region. Select the models actually available in your
> project.

Select:

``` text
Create
```

Then:

``` text
Save knowledge base
```

------------------------------------------------------------------------

# 12. Verify the Knowledge Base

Refresh the page.

The knowledge source should eventually show an active/ready status.

If it is still processing:

``` text
Wait
   ↓
Refresh
   ↓
Check status again
```

------------------------------------------------------------------------

# 13. Configure the Search Connection

Return to the **Knowledge** page.

Select:

``` text
Manage
```

Find:

``` text
Connected resources
```

Select your search service.

Open the authentication settings.

Choose:

``` text
Key authentication
```

Then:

``` text
Edit authentication
```

From the Azure portal, copy the appropriate Azure AI Search key.

Paste it into the Foundry authentication dialog.

Select:

``` text
Save
```

Your Foundry IQ connection should now be configured.

------------------------------------------------------------------------

# 14. Connect Foundry IQ to the Agent

Return to:

``` text
Build
   ↓
Agents
   ↓
product-expert-agent
```

Open the agent playground.

In the **Knowledge** section, add:

``` text
Foundry IQ
```

Select:

-   The Foundry IQ connection.
-   The knowledge base you created.

------------------------------------------------------------------------

# 15. Configure Agent Tools

Open:

``` text
Tools
```

## Remove Web Search

The Web Search tool may be automatically added to the agent.

For this lab, remove:

``` text
Web Search
```

This is important because the model used in the lab may not support that
tool.

------------------------------------------------------------------------

## Add the Foundry IQ Knowledge Tool

Select:

``` text
Add tool
```

Choose the Foundry IQ knowledge-base tool.

It will usually have a name similar to:

``` text
kb-knowledgebase...
```

For example:

``` text
kb-knowledgebase123-abc
```

This is the tool the agent uses to search the knowledge base.

------------------------------------------------------------------------

# 16. Test the Agent in the Playground

Try these questions.

### Query 1

``` text
What types of tents does Contoso offer?
```

### Query 2

``` text
Tell me about which backpacks are available in XL.
```

### Query 3

``` text
What camping accessories are available?
```

The agent should retrieve information from the product documents.

You should observe:

-   Product-specific answers.
-   Information grounded in the knowledge base.
-   Possible citations or source references.
-   Responses focused on Contoso products.

------------------------------------------------------------------------

# 17. Save the Agent Information

You will need these values later:

``` text
Agent name
Project endpoint
```

Example:

``` text
Agent name:
product-expert-agent

Project endpoint:
YOUR_PROJECT_ENDPOINT
```

Store these safely.

------------------------------------------------------------------------

# 18. Configure Tool Approval

By default, the Foundry IQ knowledge tool may execute without asking for
approval.

In this lab, we will configure the agent to request approval before
using the knowledge tool.

This gives the client application control over tool usage.

------------------------------------------------------------------------

# 19. Install Foundry Toolkit in VS Code

Open Visual Studio Code.

Open Extensions:

``` text
Ctrl + Shift + X
```

Search for:

``` text
Foundry Toolkit
```

Install the Microsoft extension if it is not already installed.

> Some older documentation or screenshots may refer to **AI Toolkit**.
> The lab uses the newer **Foundry Toolkit** naming.

------------------------------------------------------------------------

# 20. Sign in to Azure

Open the Foundry Toolkit from the VS Code sidebar.

Sign in to your Azure account if prompted.

If sign-in doesn't work through Foundry Toolkit, sign in through the
Azure extension first and then return to Foundry Toolkit.

------------------------------------------------------------------------

# 21. Select the Default Project

In Foundry Toolkit:

``` text
Microsoft Foundry Resources
        ↓
Set Default Project
```

Select the project you created earlier.

Expand the project.

Under:

``` text
Prompt Agents
```

select:

``` text
product-expert-agent
```

This opens the Agent Builder.

------------------------------------------------------------------------

# 22. Enable Approval for Foundry IQ

In the **Tools** section, find the tool with a name similar to:

``` text
kb-knowledgebase...
```

This is the Foundry IQ knowledge tool.

Select:

``` text
...
```

Then:

``` text
Ask for approval for all tools
```

Save the changes.

Now the application will receive an approval request whenever the agent
wants to use the knowledge tool.

------------------------------------------------------------------------

# 23. Clone the Agent Client Repository

Open Visual Studio Code.

Open the Command Palette:

``` text
Ctrl + Shift + P
```

Select:

``` text
Git: Clone
```

Clone:

``` text
https://github.com/MicrosoftLearning/mslearn-ai-agents
```

Open the cloned repository.

If prompted:

``` text
Do you trust the authors?
```

Choose the appropriate trust option for the repository.

------------------------------------------------------------------------

# 24. Locate the Python Files

Navigate to:

``` text
Labfiles/
└── 04-integrate-agent-with-foundry-iq/
    └── Python/
```

The folder contains the application files.

The important files include:

``` text
.env
agent.py
requirements.txt
```

------------------------------------------------------------------------

# 25. Configure the `.env` File

Open:

``` text
.env
```

Set your project endpoint:

``` env
PROJECT_ENDPOINT="YOUR_PROJECT_ENDPOINT"
```

Set your agent name:

``` env
AGENT_NAME="product-expert-agent"
```

Example:

``` env
PROJECT_ENDPOINT="https://YOUR-PROJECT-ENDPOINT"
AGENT_NAME="product-expert-agent"
```

> Do not commit your `.env` file containing secrets or credentials to
> Git.

------------------------------------------------------------------------

# 26. Understand the Python Client

The Python client performs these major tasks:

``` text
Python Application
       |
       v
Azure Authentication
       |
       v
AIProjectClient
       |
       v
Get OpenAI Client
       |
       v
Get Agent
       |
       v
Create Conversation
       |
       v
Send User Message
       |
       v
Agent Response
       |
       v
MCP Approval Request
       |
       v
User Approval
       |
       v
Foundry IQ Search
       |
       v
Final Answer
```

------------------------------------------------------------------------

# 26.5 Recommended Python Packages

For this lab, use Python 3.13.

A minimal `requirements.txt` can contain:

``` text
azure-ai-projects>=2.0.0
azure-identity
openai
python-dotenv
```

Install them with:

``` powershell
pip install -r requirements.txt
```

> If the Microsoft-provided lab repository contains a tested
> `requirements.txt`, prefer the repository's tested versions for the
> lab environment.

------------------------------------------------------------------------

# 27. Import Required Libraries

At the top of `agent.py`, make sure the required imports are available.

A clean version is:

``` python
import json
import os

from dotenv import load_dotenv
from azure.ai.projects import AIProjectClient
from azure.identity import DefaultAzureCredential
```

The starter project may already contain some of these imports.

Do not duplicate imports unnecessarily.

------------------------------------------------------------------------

# 28. Connect to the Foundry Project

Use:

``` python
credential = DefaultAzureCredential(
    exclude_environment_credential=True,
    exclude_managed_identity_credential=True
)

project_client = AIProjectClient(
    credential=credential,
    endpoint=project_endpoint
)
```

### Explanation

`DefaultAzureCredential()`:

``` text
Gets Azure authentication credentials
```

`AIProjectClient`:

``` text
Connects your Python application to the Foundry project
```

------------------------------------------------------------------------

# 29. Get the OpenAI Client

Use:

``` python
openai_client = project_client.get_openai_client()
```

This gives your application an OpenAI-compatible client that can
communicate with the Foundry project.

------------------------------------------------------------------------

# 30. Get the Agent

Use:

``` python
agent = project_client.agents.get(
    agent_name=agent_name
)

print(
    f"Connected to agent: {agent.name} "
    f"(id: {agent.id})\n"
)
```

This retrieves the agent you created in Microsoft Foundry.

------------------------------------------------------------------------

# 31. Create a Conversation

Use:

``` python
conversation = openai_client.conversations.create(
    items=[]
)

print(
    f"Created conversation (id: {conversation.id})\n"
)
```

The conversation ID allows the application to maintain conversation
context.

------------------------------------------------------------------------

# 32. Send a User Message

Inside `send_message_to_agent()`, add the user message to the
conversation:

``` python
openai_client.conversations.items.create(
    conversation_id=conversation.id,
    items=[
        {
            "type": "message",
            "role": "user",
            "content": user_message
        }
    ]
)
```

------------------------------------------------------------------------

# 33. Maintain Client-Side Conversation History

If the application maintains a local history list, add:

``` python
conversation_history.append(
    {
        "role": "user",
        "content": user_message
    }
)
```

This is useful for displaying the conversation history to the user.

------------------------------------------------------------------------

# 34. Create a Response Using the Agent

Use:

``` python
response = openai_client.responses.create(
    conversation=conversation.id,
    extra_body={
        "agent_reference": {
            "name": agent.name,
            "type": "agent_reference"
        }
    },
    input=""
)
```

### Important

The agent is specified through:

``` python
extra_body={
    "agent_reference": {
        "name": agent.name,
        "type": "agent_reference"
    }
}
```

The conversation is specified using:

``` python
conversation=conversation.id
```

------------------------------------------------------------------------

# 35. Handle MCP Approval Requests

The agent may request permission to use the Foundry IQ knowledge tool.

We need to inspect the response output.

Use:

``` python
while True:
    approval_requests = [
        item
        for item in (getattr(response, "output", None) or [])
        if getattr(item, "type", None) == "mcp_approval_request"
    ]

    if not approval_requests:
        break
```

### What is happening?

The code searches for:

``` text
mcp_approval_request
```

If no approval request exists:

``` python
if not approval_requests:
    break
```

the loop ends.

If an approval request exists, the application asks the user for
permission.

------------------------------------------------------------------------

# 36. Display the Approval Request

For each approval request:

``` python
approval_items = []

for approval_request in approval_requests:

    print(
        f"[Approval required for: "
        f"{approval_request.name}]\n"
    )

    print(
        f"Server: "
        f"{approval_request.server_label}"
    )
```

------------------------------------------------------------------------

# 37. Display Tool Arguments

The application can display the arguments being sent to the tool.

``` python
try:
    args = json.loads(
        approval_request.arguments
    )

    print(
        f"Arguments: "
        f"{json.dumps(args, indent=2)}\n"
    )

except Exception:
    print(
        f"Arguments: "
        f"{approval_request.arguments}\n"
    )
```

### Why display the arguments?

It allows the user to see what the tool is being asked to do before
approving it.

------------------------------------------------------------------------

# 38. Ask the User for Approval

Use:

``` python
approval_input = input(
    "Approve this action? (yes/no): "
).strip().lower()

approved = approval_input in ["yes", "y"]
```

This converts:

``` text
yes
```

or:

``` text
y
```

into:

``` python
True
```

Anything else becomes:

``` python
False
```

------------------------------------------------------------------------

# 39. Create the Approval Response

Create an approval response:

``` python
approval_items.append(
    {
        "type": "mcp_approval_response",
        "approval_request_id": approval_request.id,
        "approve": approved
    }
)
```

The important fields are:

  FieldPurpose            
  ----------------------- ---------------------------------------------
  `type`                  Identifies the item as an approval response
  `approval_request_id`   Identifies the request
  `approve`               `True` or `False`

------------------------------------------------------------------------

# 40. Send the Approval Decision

Add the approval response to the conversation:

``` python
openai_client.conversations.items.create(
    conversation_id=conversation.id,
    items=approval_items
)
```

Then request the next response:

``` python
response = openai_client.responses.create(
    conversation=conversation.id,
    extra_body={
        "agent_reference": {
            "name": agent.name,
            "type": "agent_reference"
        }
    },
    input=""
)
```

------------------------------------------------------------------------

# 41. Why Use a `while` Loop?

The agent may:

-   Require no approval.
-   Require one approval.
-   Require multiple approvals.

Therefore:

``` python
while True:
```

continues until:

``` text
No pending approval requests
```

The flow becomes:

``` text
Create Response
      |
      v
Approval Request?
   /        \
 No          Yes
 |            |
 v            v
Finish     Ask User
              |
              v
        Approve / Deny
              |
              v
       Send Decision
              |
              v
        Create Response
              |
              v
      Check Again
```

------------------------------------------------------------------------

# 42. Complete `send_message_to_agent()` Example

A cleaned-up version is:

``` python
def send_message_to_agent(
    openai_client,
    agent,
    conversation,
    user_message,
    conversation_history
):
    # Add user message to the conversation
    openai_client.conversations.items.create(
        conversation_id=conversation.id,
        items=[
            {
                "type": "message",
                "role": "user",
                "content": user_message
            }
        ]
    )

    # Store message locally
    conversation_history.append(
        {
            "role": "user",
            "content": user_message
        }
    )

    # Create the initial response
    response = openai_client.responses.create(
        conversation=conversation.id,
        extra_body={
            "agent_reference": {
                "name": agent.name,
                "type": "agent_reference"
            }
        },
        input=""
    )

    # Handle zero, one, or multiple approval requests
    while True:

        approval_requests = [
            item
            for item in (getattr(response, "output", None) or [])
            if getattr(item, "type", None)
            == "mcp_approval_request"
        ]

        # No approvals required
        if not approval_requests:
            break

        approval_items = []

        # Handle each approval request
        for approval_request in approval_requests:

            print(
                f"\n[Approval required for: "
                f"{approval_request.name}]"
            )

            print(
                f"Server: "
                f"{approval_request.server_label}"
            )

            # Display tool arguments
            try:
                args = json.loads(
                    approval_request.arguments
                )

                print(
                    "Arguments:"
                )

                print(
                    json.dumps(
                        args,
                        indent=2
                    )
                )

            except Exception:
                print(
                    f"Arguments: "
                    f"{approval_request.arguments}"
                )

            # Ask user for permission
            approval_input = input(
                "\nApprove this action? (yes/no): "
            ).strip().lower()

            approved = approval_input in [
                "yes",
                "y"
            ]

            if approved:
                print("Approving action...\n")
            else:
                print("Action denied.\n")

            # Create approval response
            approval_items.append(
                {
                    "type": "mcp_approval_response",
                    "approval_request_id":
                        approval_request.id,
                    "approve": approved
                }
            )

        # Send approval decisions
        openai_client.conversations.items.create(
            conversation_id=conversation.id,
            items=approval_items
        )

        # Get the next response
        response = openai_client.responses.create(
            conversation=conversation.id,
            extra_body={
                "agent_reference": {
                    "name": agent.name,
                    "type": "agent_reference"
                }
            },
            input=""
        )

    return response
```

> **Note:** The exact SDK object types and fields can change because
> some Foundry/agent capabilities are evolving. If your installed SDK
> reports a different field name, follow the version-specific API
> exposed by your lab environment.

------------------------------------------------------------------------

# 43. Display the Agent Response

After the approval loop finishes, your application should process the
final response.

For example:

``` python
assistant_text = response.output_text

print(
    "Assistant:",
    assistant_text
)

conversation_history.append(
    {
        "role": "assistant",
        "content": assistant_text
    }
)
```

This keeps both sides of the conversation in the local history.

------------------------------------------------------------------------

# 44. Create a Virtual Environment

Open a terminal in:

``` text
Labfiles/04-integrate-agent-with-foundry-iq/Python
```

Create a virtual environment:

``` powershell
python -m venv labenv
```

Activate it on Windows:

``` powershell
.\labenv\Scripts\Activate.ps1
```

Then install dependencies:

``` powershell
pip install -r requirements.txt
```

If activation is blocked by PowerShell execution policy, use the
appropriate Python/VS Code environment activation method allowed by your
system.

------------------------------------------------------------------------

# 45. Sign in to Azure

Run:

``` powershell
az login
```

Complete the Azure sign-in process.

If you have multiple Azure tenants, you may need:

``` powershell
az login --tenant YOUR_TENANT_ID
```

Make sure the correct subscription containing your Foundry project is
selected.

------------------------------------------------------------------------

# 46. Run the Agent Client

Run:

``` powershell
python agent.py
```

The application should connect to your Foundry agent.

You should see something similar to:

``` text
Connected to agent: product-expert-agent
Created conversation
```

------------------------------------------------------------------------

# 47. Test the Agent

## Query 1 --- Product Categories

Ask:

``` text
What types of outdoor products does Contoso offer?
```

When prompted:

``` text
Approve this action? (yes/no):
```

Enter:

``` text
yes
```

The agent should search the knowledge base.

------------------------------------------------------------------------

# 48. Query 2 --- Product Details

Ask:

``` text
Tell me about the weatherproof features of your tents.
```

Approve the knowledge-base search.

The agent should retrieve information from the tent product documents.

------------------------------------------------------------------------

# 49. Query 3 --- Product Comparison

Ask:

``` text
What's the difference between your daypacks and expedition backpacks?
```

Approve the knowledge-base request.

The agent should retrieve and synthesize information from the backpack
documentation.

------------------------------------------------------------------------

# 50. Query 4 --- Accessories

Ask:

``` text
What camping accessories would you recommend for a weekend hiking trip?
```

Approve the request.

The agent should use the product information stored in the knowledge
base.

------------------------------------------------------------------------

# 51. Query 5 --- Follow-up Question

Ask:

``` text
How much do those items typically cost?
```

The important thing here is **conversation context**.

The application maintains the same conversation ID, so the agent can
understand:

``` text
"those items"
```

as a reference to the products discussed previously.

------------------------------------------------------------------------

# 52. View Conversation History

If the application provides a history command, type:

``` text
history
```

This should display the conversation stored by the client application.

To exit:

``` text
quit
```

------------------------------------------------------------------------

# 53. What Is Happening Behind the Scenes?

The complete architecture looks like this:

``` text
                    Microsoft Foundry
                           |
                           v
                  +------------------+
                  |   AI Agent       |
                  | product-expert   |
                  +--------+---------+
                           |
                           v
                  +------------------+
                  |   Foundry IQ     |
                  +--------+---------+
                           |
                           v
                  +------------------+
                  | Knowledge Base   |
                  +--------+---------+
                           |
                           v
                  +------------------+
                  | Azure AI Search  |
                  +--------+---------+
                           |
                           v
                  +------------------+
                  | Product PDFs     |
                  +------------------+


Python Application
       |
       v
AIProjectClient
       |
       v
OpenAI Client
       |
       v
Conversation
       |
       v
Agent
       |
       v
MCP Approval Request
       |
       v
User Approves
       |
       v
Foundry IQ
       |
       v
Knowledge Retrieval
       |
       v
Agent Answer
```

------------------------------------------------------------------------

# 54. Important Concepts

## 54.1 Foundry IQ

Foundry IQ allows an agent to search and retrieve information from
connected knowledge sources.

In this lab:

``` text
Product PDFs
      ↓
Azure Blob Storage
      ↓
Azure AI Search
      ↓
Foundry IQ
      ↓
AI Agent
```

------------------------------------------------------------------------

## 54.2 Knowledge Base

A knowledge base contains information that the agent can search.

In this project:

``` text
Contoso Product Catalog
```

is the knowledge source.

------------------------------------------------------------------------

## 54.3 Agent

The agent is the AI application that:

-   Understands the user's question.
-   Decides when it needs external knowledge.
-   Calls the knowledge tool.
-   Uses retrieved information.
-   Generates the final response.

------------------------------------------------------------------------

## 54.4 MCP Approval

MCP approval gives the user control over tool usage.

Instead of automatically allowing:

``` text
Agent → Knowledge Tool
```

the application uses:

``` text
Agent
  ↓
Approval Request
  ↓
User
  ↓
Yes / No
  ↓
Knowledge Tool
```

------------------------------------------------------------------------

## 54.5 Conversation ID

The conversation ID keeps requests connected.

Example:

``` text
Conversation ID
      |
      +--- Question 1
      |
      +--- Answer 1
      |
      +--- Question 2
      |
      +--- Answer 2
      |
      +--- Question 3
```

This allows follow-up questions to retain context.

------------------------------------------------------------------------

# 55. RAG Architecture

This project is essentially an enterprise knowledge retrieval workflow.

The basic RAG flow is:

``` text
User Question
      |
      v
AI Agent
      |
      v
Search Knowledge Base
      |
      v
Retrieve Relevant Documents
      |
      v
Provide Retrieved Context
      |
      v
LLM
      |
      v
Grounded Answer
```

The important difference is that the agent can decide when it needs to
use the knowledge tool.

------------------------------------------------------------------------

# 56. Error Handling

The application should handle errors gracefully.

A simple pattern is:

``` python
try:
    # Application code

except Exception as ex:
    print(f"Error: {ex}")
```

For example:

``` python
try:
    response = openai_client.responses.create(
        conversation=conversation.id,
        extra_body={
            "agent_reference": {
                "name": agent.name,
                "type": "agent_reference"
            }
        },
        input=""
    )

except Exception as ex:
    print(f"Error communicating with agent: {ex}")
```

------------------------------------------------------------------------

# 57. Common Problems

## Problem 1 --- Python 3.14 Dependency Error

### Symptom

A package fails to install or has no compatible build.

### Solution

Use:

``` text
Python 3.13
```

Check your version:

``` powershell
python --version
```

Expected:

``` text
Python 3.13.x
```

------------------------------------------------------------------------

# 58. Problem 2 --- Agent Cannot Search the Knowledge Base

Check:

1.  Foundry IQ is connected.
2.  The knowledge base is active.
3.  Azure AI Search is connected.
4.  The storage account contains the PDFs.
5.  The `kb-knowledgebase...` tool is attached to the agent.
6.  Web Search has been removed if unsupported.
7.  The correct knowledge base is selected.

------------------------------------------------------------------------

# 59. Problem 3 --- No Approval Request

Check whether approval was enabled for the correct tool.

The relevant tool is the one beginning with:

``` text
kb-knowledgebase
```

Do not assume that changing approval on another search tool will affect
Foundry IQ.

------------------------------------------------------------------------

# 60. Problem 4 --- Agent Fails Before Knowledge Search

Check whether:

``` text
Web Search
```

is attached to the agent.

If the lab model does not support Web Search, remove it.

Keep the Foundry IQ knowledge tool:

``` text
kb-knowledgebase...
```

------------------------------------------------------------------------

# 61. Problem 5 --- Authentication Error

Run:

``` powershell
az login
```

Then verify that you are using the Azure subscription containing your
Foundry project.

You can inspect your Azure account with:

``` powershell
az account show
```

------------------------------------------------------------------------

# 62. Problem 6 --- Project Endpoint Error

Check your `.env` file.

Example:

``` env
PROJECT_ENDPOINT="YOUR_PROJECT_ENDPOINT"
AGENT_NAME="product-expert-agent"
```

Make sure:

-   The endpoint belongs to the correct Foundry project.
-   The agent name exactly matches the agent created in Foundry.
-   There are no accidental spaces or quotation errors.

------------------------------------------------------------------------

# 63. Problem 7 --- Storage Account Name Already Exists

Azure storage account names must be globally unique.

Instead of:

``` text
aistorage
```

use a unique name such as:

``` text
aistorage123456
```

Follow Azure's storage naming requirements.

------------------------------------------------------------------------

# 64. Problem 8 --- Knowledge Base Still Processing

If the knowledge base isn't active immediately:

``` text
Wait
   ↓
Refresh
   ↓
Check status
```

Document indexing and processing can take some time.

------------------------------------------------------------------------

# 65. Final Application Flow

The complete application works like this:

``` text
1. User enters question
          |
          v
2. Add question to conversation
          |
          v
3. Send request to agent
          |
          v
4. Agent decides whether to use Foundry IQ
          |
          v
5. MCP approval request
          |
          v
6. User approves or denies
          |
          +-------- No --------+
          |                    |
          |                    v
          |              Continue response
          |
         Yes
          |
          v
7. Foundry IQ searches knowledge base
          |
          v
8. Relevant information retrieved
          |
          v
9. Agent generates answer
          |
          v
10. Response displayed
          |
          v
11. Conversation context maintained
```

------------------------------------------------------------------------

# 66. Key Python Code to Remember

## Create the Project Client

``` python
project_client = AIProjectClient(
    credential=credential,
    endpoint=project_endpoint
)
```

## Get OpenAI Client

``` python
openai_client = project_client.get_openai_client()
```

## Get Agent

``` python
agent = project_client.agents.get(
    agent_name=agent_name
)
```

## Create Conversation

``` python
conversation = openai_client.conversations.create(
    items=[]
)
```

## Add Message

``` python
openai_client.conversations.items.create(
    conversation_id=conversation.id,
    items=[
        {
            "type": "message",
            "role": "user",
            "content": user_message
        }
    ]
)
```

## Create Agent Response

``` python
response = openai_client.responses.create(
    conversation=conversation.id,
    extra_body={
        "agent_reference": {
            "name": agent.name,
            "type": "agent_reference"
        }
    },
    input=""
)
```

## Detect Approval Request

``` python
approval_requests = [
    item
    for item in (getattr(response, "output", None) or [])
    if getattr(item, "type", None)
    == "mcp_approval_request"
]
```

## Send Approval

``` python
approval_items.append(
    {
        "type": "mcp_approval_response",
        "approval_request_id":
            approval_request.id,
        "approve": approved
    }
)
```

------------------------------------------------------------------------

# 67. Summary

In this exercise, you:

-   Created a Microsoft Foundry project.
-   Created an AI agent.
-   Created an Azure AI Search resource.
-   Created an Azure Storage account.
-   Uploaded product PDF documents.
-   Created a Foundry IQ knowledge base.
-   Connected Foundry IQ to an AI agent.
-   Tested the agent in the Foundry playground.
-   Configured approval for the knowledge tool.
-   Created a Python client application.
-   Connected to the Foundry project using `AIProjectClient`.
-   Retrieved the agent programmatically.
-   Created and maintained a conversation.
-   Sent messages to the agent.
-   Handled MCP approval requests.
-   Retrieved information through Foundry IQ.
-   Tested conversational context.

------------------------------------------------------------------------

# 68. What You Learned

The most important concepts are:

``` text
Microsoft Foundry
       |
       +-- AI Agent
       |
       +-- Foundry IQ
       |      |
       |      +-- Knowledge Base
       |             |
       |             +-- Azure AI Search
       |                    |
       |                    +-- Product Documents
       |
       +-- Python SDK
              |
              +-- AIProjectClient
              |
              +-- OpenAI Client
              |
              +-- Conversations
              |
              +-- Responses
              |
              +-- MCP Approval
```

This demonstrates how to build an AI agent that can retrieve enterprise
information from a knowledge base while keeping the user in control of
external tool usage.

------------------------------------------------------------------------

# 69. Clean Up Azure Resources

When you finish the lab, delete the resources you created if you no
longer need them.

Open:

``` text
https://portal.azure.com
```

Navigate to the resource group containing:

-   Microsoft Foundry resources.
-   Azure AI Search.
-   Storage account.
-   Other resources created for this exercise.

Select:

``` text
Delete resource group
```

Enter the resource group name and confirm.

> **Warning:** Deleting a resource group deletes the resources inside
> it. Make sure you are deleting only resources that you no longer need.

------------------------------------------------------------------------

# 70. Final Checklist

Before finishing the lab, verify:

-   Python 3.13 is installed.
-   Foundry project is created.
-   AI agent is created.
-   Azure AI Search resource is created.
-   Storage account is created.
-   Product PDFs are uploaded.
-   Foundry IQ knowledge base is active.
-   Foundry IQ is connected to the agent.
-   Web Search is removed if unsupported.
-   `kb-knowledgebase...` tool is attached.
-   Tool approval is enabled.
-   VS Code repository is cloned.
-   `.env` is configured.
-   Python dependencies are installed.
-   Azure CLI login is successful.
-   `agent.py` runs successfully.
-   Knowledge retrieval works.
-   Approval requests work.
-   Conversation history works.
-   Azure resources are cleaned up when finished.

------------------------------------------------------------------------

# 71. Complete `agent.py` --- Final Code

The following is the complete client application for this lab.

It:

1.  Loads the Foundry project endpoint and agent name.
2.  Authenticates with Azure.
3.  Connects to Microsoft Foundry.
4.  Gets the OpenAI-compatible client.
5.  Gets the agent.
6.  Creates one persistent conversation.
7.  Sends user questions to the agent.
8.  Detects MCP approval requests.
9.  Shows the MCP server, tool, and arguments.
10. Asks the user for approval.
11. Sends the approval decision.
12. Prints the final grounded answer.
13. Keeps the same conversation ID for follow-up questions.
14. Supports `history` and `quit`.

## `.env`

Create:

``` env
PROJECT_ENDPOINT="YOUR_PROJECT_ENDPOINT"
AGENT_NAME="product-expert-agent"
```

Example:

``` env
PROJECT_ENDPOINT="https://your-resource.services.ai.azure.com/api/projects/your-project"
AGENT_NAME="product-expert-agent"
```

Do not commit `.env` to Git.

## Complete `agent.py`

``` python
import json
import os

from dotenv import load_dotenv
from azure.ai.projects import AIProjectClient
from azure.identity import DefaultAzureCredential


def get_environment():
    """Load and validate environment variables."""

    load_dotenv()

    project_endpoint = os.getenv("PROJECT_ENDPOINT")
    agent_name = os.getenv("AGENT_NAME")

    if not project_endpoint:
        raise ValueError(
            "PROJECT_ENDPOINT is missing from the .env file."
        )

    if not agent_name:
        raise ValueError(
            "AGENT_NAME is missing from the .env file."
        )

    return project_endpoint, agent_name


def create_clients(project_endpoint):
    """Create the Foundry project client and OpenAI client."""

    credential = DefaultAzureCredential()

    project_client = AIProjectClient(
        endpoint=project_endpoint,
        credential=credential,
    )

    openai_client = project_client.get_openai_client()

    return project_client, openai_client


def get_agent(project_client, agent_name):
    """Retrieve the agent created in Microsoft Foundry."""

    agent = project_client.agents.get(
        agent_name=agent_name
    )

    print(
        f"\nConnected to agent: "
        f"{agent.name} "
        f"(id: {agent.id})"
    )

    return agent


def show_approval_request(approval_request):
    """Display MCP approval information."""

    print("\n" + "=" * 60)
    print("MCP APPROVAL REQUEST")
    print("=" * 60)

    print(
        f"Server : "
        f"{getattr(approval_request, 'server_label', 'Unknown')}"
    )

    print(
        f"Tool   : "
        f"{getattr(approval_request, 'name', 'Unknown')}"
    )

    arguments = getattr(
        approval_request,
        "arguments",
        None,
    )

    print("\nArguments:")

    try:
        if isinstance(arguments, str):
            arguments = json.loads(arguments)

        print(
            json.dumps(
                arguments,
                indent=2,
                default=str,
            )
        )

    except Exception:
        print(arguments)


def request_agent_response(
    openai_client,
    agent,
    conversation_id,
    user_input="",
):
    """Send a request to the agent."""

    return openai_client.responses.create(
        conversation=conversation_id,
        input=user_input,
        extra_body={
            "agent_reference": {
                "name": agent.name,
                "type": "agent_reference",
            }
        },
    )


def handle_approvals(
    openai_client,
    agent,
    response,
):
    """
    Handle zero, one, or multiple MCP approval requests.

    The loop continues until the agent no longer has
    an MCP approval request waiting for a decision.
    """

    while True:

        approval_requests = [
            item
            for item in (
                getattr(response, "output", None)
                or []
            )
            if getattr(item, "type", None)
            == "mcp_approval_request"
        ]

        # No approval is required.
        if not approval_requests:
            return response

        approval_items = []

        for approval_request in approval_requests:

            show_approval_request(
                approval_request
            )

            answer = input(
                "\nApprove this action? (yes/no): "
            ).strip().lower()

            approved = answer in {
                "yes",
                "y",
            }

            if approved:
                print(
                    "\nApproval granted."
                )
            else:
                print(
                    "\nApproval denied."
                )

            approval_items.append(
                {
                    "type": "mcp_approval_response",
                    "approval_request_id":
                        approval_request.id,
                    "approve": approved,
                }
            )

        # Continue the same response after
        # sending the approval decisions.
        response = openai_client.responses.create(
            previous_response_id=response.id,
            input=approval_items,
            extra_body={
                "agent_reference": {
                    "name": agent.name,
                    "type": "agent_reference",
                }
            },
        )


def send_message(
    openai_client,
    agent,
    conversation,
    user_message,
    conversation_history,
):
    """Send one user message and return the final response."""

    # Add the user message to the conversation.
    openai_client.conversations.items.create(
        conversation_id=conversation.id,
        items=[
            {
                "type": "message",
                "role": "user",
                "content": user_message,
            }
        ],
    )

    # Keep a simple local history for display.
    conversation_history.append(
        {
            "role": "user",
            "content": user_message,
        }
    )

    # Ask the agent to process the message.
    response = request_agent_response(
        openai_client=openai_client,
        agent=agent,
        conversation_id=conversation.id,
    )

    # Handle MCP approval requests.
    response = handle_approvals(
        openai_client=openai_client,
        agent=agent,
        response=response,
    )

    assistant_text = (
        getattr(response, "output_text", None)
        or ""
    )

    print("\n" + "-" * 60)
    print("ASSISTANT")
    print("-" * 60)
    print(assistant_text)
    print("-" * 60)

    conversation_history.append(
        {
            "role": "assistant",
            "content": assistant_text,
        }
    )

    return response


def show_history(conversation_history):
    """Display the locally stored conversation history."""

    if not conversation_history:
        print("\nNo conversation history yet.")
        return

    print("\n" + "=" * 60)
    print("CONVERSATION HISTORY")
    print("=" * 60)

    for index, message in enumerate(
        conversation_history,
        start=1,
    ):
        role = message["role"].upper()
        content = message["content"]

        print(
            f"\n{index}. {role}:"
        )
        print(content)

    print("=" * 60)


def main():
    """Main application."""

    try:
        # -------------------------------------------------
        # 1. Load configuration
        # -------------------------------------------------

        project_endpoint, agent_name = (
            get_environment()
        )

        print(
            "Microsoft Foundry IQ Agent Client"
        )
        print("=" * 60)

        # -------------------------------------------------
        # 2. Create clients
        # -------------------------------------------------

        project_client, openai_client = (
            create_clients(project_endpoint)
        )

        # -------------------------------------------------
        # 3. Get the agent
        # -------------------------------------------------

        agent = get_agent(
            project_client,
            agent_name,
        )

        # -------------------------------------------------
        # 4. Create one persistent conversation
        # -------------------------------------------------

        conversation = (
            openai_client.conversations.create(
                items=[]
            )
        )

        print(
            f"\nCreated conversation: "
            f"{conversation.id}"
        )

        print("\nCommands:")
        print("  history  - show conversation history")
        print("  quit     - exit the application")

        # -------------------------------------------------
        # 5. Local history
        # -------------------------------------------------

        conversation_history = []

        # -------------------------------------------------
        # 6. Chat loop
        # -------------------------------------------------

        while True:

            print("\n" + "=" * 60)

            user_message = input(
                "You: "
            ).strip()

            if not user_message:
                continue

            if user_message.lower() in {
                "quit",
                "exit",
            }:
                print(
                    "\nGoodbye!"
                )
                break

            if user_message.lower() == "history":
                show_history(
                    conversation_history
                )
                continue

            # -------------------------------------------------
            # 7. Send message to agent
            # -------------------------------------------------

            try:

                send_message(
                    openai_client=openai_client,
                    agent=agent,
                    conversation=conversation,
                    user_message=user_message,
                    conversation_history=(
                        conversation_history
                    ),
                )

            except Exception as ex:

                print(
                    "\nError communicating "
                    f"with the agent: {ex}"
                )

    except Exception as ex:

        print(
            "\nApplication error:"
        )
        print(ex)


if __name__ == "__main__":
    main()
```

------------------------------------------------------------------------

# 72. Run the Complete Application

Open a terminal in:

``` text
Labfiles/
└── 04-integrate-agent-with-foundry-iq/
    └── Python/
```

Create the virtual environment:

``` powershell
python -m venv labenv
```

Activate it:

``` powershell
.\labenv\Scripts\Activate.ps1
```

Install the packages:

``` powershell
pip install -r requirements.txt
```

Sign in to Azure:

``` powershell
az login
```

Check the selected subscription:

``` powershell
az account show
```

Run:

``` powershell
python agent.py
```

Expected startup:

``` text
Microsoft Foundry IQ Agent Client
============================================================

Connected to agent: product-expert-agent (id: ...)

Created conversation: ...
```

------------------------------------------------------------------------

# 73. Test the Complete Application

Ask:

``` text
What types of tents does Contoso offer?
```

If MCP approval is enabled, you should see something similar to:

``` text
============================================================
MCP APPROVAL REQUEST
============================================================
Server : ...
Tool   : ...
Arguments:
{
    ...
}

Approve this action? (yes/no):
```

Enter:

``` text
yes
```

The agent should retrieve information from the Foundry IQ knowledge base
and return a grounded answer.

Then test:

``` text
Tell me about the weatherproof features of your tents.
```

Then:

``` text
What camping accessories are available?
```

Finally, test conversation context:

``` text
How much do those items cost?
```

Because the same conversation ID is reused, the follow-up question can
refer to products discussed earlier.

------------------------------------------------------------------------

# 74. Final Troubleshooting Checklist

If the Playground or Python application returns:

``` text
403 Forbidden
```

check these first:

``` text
Azure AI Search
      ↓
Access control (IAM)
      ↓
Search Index Data Reader
      ↓
Foundry Project managed identity
```

Then check:

``` text
Azure AI Search
      ↓
Settings
      ↓
Keys
      ↓
API Access Control
      ↓
Both / Role-based access control
```

Then verify:

``` text
Foundry IQ
      ↓
Knowledge Base
      ↓
Status = Active
```

Then verify:

``` text
Azure AI Search
      ↓
Indexes
      ↓
Expected index exists
      ↓
Document count > 0
```

Do not recreate the knowledge base just because the MCP endpoint returns
403.

------------------------------------------------------------------------

# 75. Final Architecture

``` text
                         Microsoft Foundry
                                |
                                v
                         +-------------+
                         | AI Agent    |
                         +------+------+
                                |
                                v
                         +-------------+
                         | Foundry IQ  |
                         +------+------+
                                |
                                v
                         +-------------+
                         | Knowledge   |
                         | Base        |
                         +------+------+
                                |
                                v
                         +-------------+
                         | Azure AI    |
                         | Search      |
                         +------+------+
                                |
                                v
                         +-------------+
                         | Search      |
                         | Index       |
                         +------+------+
                                |
                                v
                         +-------------+
                         | Product     |
                         | PDFs        |
                         +-------------+

Python Application
        |
        v
AIProjectClient
        |
        v
OpenAI Client
        |
        v
Conversation
        |
        v
AI Agent
        |
        v
MCP Approval
        |
        v
Foundry IQ
        |
        v
Knowledge Retrieval
        |
        v
Grounded Answer
```

------------------------------------------------------------------------

# 76. Final Checklist

Before distributing this lab, verify that every student has completed:

-   [ ] Python 3.13 installed.
-   [ ] Microsoft Foundry project created.
-   [ ] Foundry Project managed identity enabled.
-   [ ] AI agent created.
-   [ ] Azure AI Search resource created.
-   [ ] Azure AI Search API access configured.
-   [ ] **Foundry Project managed identity has
    `Search Index Data Reader` on Azure AI Search.**
-   [ ] Storage account created.
-   [ ] Product PDFs uploaded.
-   [ ] Foundry IQ knowledge source created.
-   [ ] Knowledge base created.
-   [ ] Knowledge base status is **Active**.
-   [ ] Search index exists.
-   [ ] Search index contains documents.
-   [ ] Foundry IQ Search connection configured.
-   [ ] Foundry IQ connected to the agent.
-   [ ] Web Search removed if unsupported.
-   [ ] `kb-knowledgebase...` tool attached.
-   [ ] MCP/tool approval configured.
-   [ ] VS Code repository cloned.
-   [ ] `.env` configured.
-   [ ] Virtual environment created.
-   [ ] Dependencies installed.
-   [ ] Azure CLI login completed.
-   [ ] `agent.py` runs successfully.
-   [ ] MCP approval request appears when expected.
-   [ ] Knowledge retrieval works.
-   [ ] Follow-up questions retain conversation context.
-   [ ] Azure resources are cleaned up after the lab.
