# Microsoft Foundry – Create an AI Agent with Voice

## 1. Create a Microsoft Foundry Project

Microsoft Foundry uses a **Project** to keep everything related to your AI application in one place, such as:

- AI models
- Agents
- Data
- Resources
- Other AI assets

### Step 1: Open Microsoft Foundry

Open your browser and go to:

[Microsoft Foundry](https://ai.azure.com?utm_source=chatgpt.com)

Sign in using your **Azure account**.

### Step 2: Enable the New Foundry Experience

At the top of the page, look for the **New Foundry** option.

If it is not enabled, turn it on.

### Step 3: Create a Project

If you don't already have a project, Microsoft Foundry will ask you to create one.

Create a project with these settings:

| Setting              | Value                                            |
| -------------------- | ------------------------------------------------ |
| **Project name**     | `Project64953676`                                |
| **Foundry resource** | `Project64953676-resource`                       |
| **Subscription**     | Your Azure subscription                          |
| **Resource group**   | `ResourceGroup1`                                 |
| **Region**           | Select one of the recommended AI Foundry regions |

Then click **Create**.

> **Note:** Depending on your Azure permissions, you may need to turn off the option for automatically setting up recommended resources.

Wait for the project to be created.

After creation, your project home page will open.

---

# 2. Create an Agent

Now we will create an AI agent.

### Step 1: Start Creating an Agent

On the **Home** page:

**Build an agent → Start building**

Alternatively:

**Build → Agents → Create**

### Step 2: Give the Agent a Name

Enter:

```text
speech-agent
```

After creating it, the agent will open in the **Agent Playground**.

---

# 3. Select a Model

In the Agent Playground, look for the **Model** dropdown.

Make sure:

1. A model has been deployed.
2. The deployed model is selected for your agent.

The agent needs a model because the model is what generates the answers.

---

# 4. Give Instructions to the Agent

In the **Instructions** section, enter:

```text
You are an AI agent that provides information about AI and related topics. You answer questions concisely and precisely.
```

Then click **Save**.

### Test the Agent

In the Chat pane, ask:

```text
What can you help me with?
```

The agent should answer according to the instructions you provided.

---

# 5. Enable Azure Speech Voice Live

Now we will add **voice** to the agent.

Voice mode allows you to **speak to the agent** instead of typing.

### Step 1: Enable Voice Mode

On the left side, below the model selection area, find:

**Voice mode**

Turn it on.

### Step 2: Open Voice Configuration

If the Configuration pane does not open automatically:

1. Find the **⚙️ gear icon** above the chat area.
2. Click it.

### Step 3: Configure Voice

In the **Configuration** pane, find **Voice mode**.

Here you can configure:

- **Speech input** – how your voice is received.
- **Speech output** – how the agent speaks back.
- **Voice** – the voice used by the agent.

You can try different voices and preview them.

Choose the voice you like.

### Step 4: Save

Close the Configuration pane.

Click **Save** to save the agent configuration.

---

# 6. Talk to the Agent Using Your Voice

Now your agent is ready for a voice conversation.

> **Tip:** Voice works better in a quiet environment. A microphone or headset is recommended.

### Step 1: Start a Voice Session

In the Chat pane, look for:

**Start session**

Click it.

If your browser asks for microphone permission, select **Allow**.

The agent will start listening to you.

### Step 2: Speak to the Agent

When the status says:

**Listening…**

Say:

```text
How does speech recognition work?
```

The agent will receive your voice.

### Step 3: Processing

The status will change to:

**Processing…**

This means the system is processing your speech and generating an answer.

Sometimes processing happens very quickly, so you may not see this status for long.

### Step 4: Agent Speaks

The status will then change to:

**Speaking…**

The agent will use **text-to-speech** to speak its answer.

In simple terms:

```text
Your voice
     ↓
Speech Recognition
     ↓
AI Model
     ↓
Text Answer
     ↓
Text-to-Speech
     ↓
Agent Voice
```

---

# 7. View the Conversation as Text

At the bottom of the chat screen, you can find the **CC button**.

CC means **Closed Captions**.

Click it to see:

- What you said
- What the agent answered

as text.

You can continue asking questions, for example:

```text
How does speech synthesis work?
```

The agent will respond using voice.

---

# 8. End the Voice Session

When you are finished:

1. Click the **X** button.
2. The voice session will end.
3. A **transcript** of the conversation will be displayed.

A transcript is simply the text version of your conversation.

---

# 9. View the Agent Client Code

You can also use your agent from your own application.

At the top of the chat screen, select:

**Call agent**

This shows sample code that demonstrates how an application can communicate with your agent.

The code handles things such as:

### 1. Connecting to the project

It connects your application to your Microsoft Foundry project.

### 2. Audio streaming

It can handle:

- Audio coming from the microphone
- Audio going to the speaker

### 3. Audio devices

It can work with devices such as:

- Microphones
- Speakers
- Headsets

---

# 10. Python Code

Before running the Python code, install the required libraries.

### Install the Azure AI Projects library

```bash
pip install azure-ai-projects>=2.1.0
```

The code also uses `azure.identity`, which is provided by the Azure Identity package. If it is not already installed, install it with:

```bash
pip install azure-identity
```

---

## Python Code Explained

### Step 1: Import the libraries

```python
from azure.identity import DefaultAzureCredential
from azure.ai.projects import AIProjectClient
```

**`DefaultAzureCredential`**

This allows your Python program to authenticate with Azure using available Azure credentials.

**`AIProjectClient`**

This is used to connect your Python program to your Microsoft Foundry project.

---

### Step 2: Define the project endpoint

```python
endpoint = "https://project64953676-resource.services.ai.azure.com/api/projects/project64953676"
```

The endpoint tells the program **which Microsoft Foundry project it should connect to**.

Your endpoint will normally be different if you create a different project.

---

### Step 3: Create the project client

```python
project_client = AIProjectClient(
    endpoint=endpoint,
    credential=DefaultAzureCredential(),
)
```

Here we create a connection to the Microsoft Foundry project.

You can think of it as:

```text
Python Program
      ↓
AIProjectClient
      ↓
Microsoft Foundry Project
```

---

### Step 4: Specify the Agent

```python
my_agent = "speech-agent"
my_version = "6"
```

Here:

- `speech-agent` = name of your agent
- `6` = version of the agent

So the code is telling Microsoft Foundry:

> "I want to use the `speech-agent`, version 6."

---

### Step 5: Get the OpenAI Client

```python
openai_client = project_client.get_openai_client()
```

This gets an OpenAI-compatible client from your Microsoft Foundry project.

You can then use this client to interact with your agent.

---

### Step 6: Send a Question to the Agent

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
    },
)
```

The important part is:

```python
"content": "Tell me what you can help with."
```

This is the question sent to the agent.

And:

```python
"agent_reference": {
    "name": my_agent,
    "version": my_version,
    "type": "agent_reference"
}
```

tells Microsoft Foundry **which agent should answer the question**.

---

### Step 7: Print the Answer

```python
print(f"Response output: {response.output_text}")
```

This prints the agent's response in your terminal.

---

# 11. Complete Python Code

```python
from azure.identity import DefaultAzureCredential
from azure.ai.projects import AIProjectClient

endpoint = "https://project64953676-resource.services.ai.azure.com/api/projects/project64953676"

project_client = AIProjectClient(
    endpoint=endpoint,
    credential=DefaultAzureCredential(),
)

my_agent = "speech-agent"
my_version = "6"

openai_client = project_client.get_openai_client()

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
    },
)

print(f"Response output: {response.output_text}")
```

---

# 12. What Are We Actually Building?

The complete flow is:

```text
                    Microsoft Foundry
                           │
                           ▼
                    Create Project
                           │
                           ▼
                    Create Agent
                    "speech-agent"
                           │
                           ▼
                    Select AI Model
                           │
                           ▼
                  Give Agent Instructions
                           │
                           ▼
                    Enable Voice Mode
                           │
                           ▼
                 Azure Speech Voice Live
                           │
                           ▼
              ┌─────────────────────────┐
              │       User speaks       │
              └────────────┬────────────┘
                           ▼
                  Speech Recognition
                           │
                           ▼
                      AI Agent
                           │
                           ▼
                     AI Response
                           │
                           ▼
                    Text-to-Speech
                           │
                           ▼
                 Agent speaks to user
```

### In simple words:

**Microsoft Foundry** → creates and manages the AI project.

**Agent** → decides how to behave and answer questions.

**AI Model** → generates the answer.

**Azure Speech** → converts your voice to text and the agent's text back to voice.

**Python code** → allows you to connect your own application to the agent.

---

# 13. Clean Up Azure Resources

Azure resources can cost money if you leave them running.

When you have finished experimenting, delete resources you no longer need.

### Step 1

Open:

[Azure Portal](https://portal.azure.com?utm_source=chatgpt.com)

### Step 2

Find the resource group:

```text
ResourceGroup1
```

### Step 3

Open the resource group.

### Step 4

Select:

**Delete resource group**

### Step 5

Enter the resource group name to confirm the deletion.

The resource group and its resources will then be deleted.

> **Important:** Only delete the resource group if you are sure that the resources inside it are no longer needed.
