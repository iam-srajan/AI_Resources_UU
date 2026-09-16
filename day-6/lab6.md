# Build AI Agents with Microsoft Foundry and VS Code

In this lab, you will build a complete AI agent using:

- **Microsoft Foundry portal**
- **Foundry Toolkit for VS Code**
- **Python**
- **File search**
- **Code interpreter**

You will first create and configure an agent in the Foundry portal. Then you will connect to the same agent from VS Code and interact with it using Python.

**Estimated time:** 45 minutes

> **Note:** Some technologies used in this lab are in preview or active development. You may see unexpected behavior, warnings, or errors.

---

# Prerequisites

Before starting, make sure you have:

- An Azure subscription with enough permissions and quota to create Azure AI resources.
- Visual Studio Code installed.
- Python 3.13 installed.
- Git installed.
- Basic knowledge of Azure AI services and Python.

> **Important:** Python 3.14 is not supported yet because some dependencies do not have a Python 3.14 build. This lab was tested with **Python 3.13.12**.

---

# 1. Create a Microsoft Foundry Project

Microsoft Foundry uses **projects** to organize models, resources, data, and other assets needed to build AI solutions.

## Step 1: Open Microsoft Foundry

Open the Foundry portal:

https://ai.azure.com

Sign in using the credentials provided for your lab.

> **Note:** Close any tips or quick-start panels that appear when you sign in. If necessary, select the Foundry logo in the top-left corner to return to the home page.

> **Important:** This lab uses the **New Foundry experience**.

## Step 2: Start building

From the top banner, select **Start building** to use the new Microsoft Foundry experience.

When prompted, create a new project:

**Project name:** `Project65112212`

## Step 3: Configure the project

Expand **Advanced options** and use these settings:

| Setting                    | Value                               |
| -------------------------- | ----------------------------------- |
| Microsoft Foundry resource | `Project65112212-resource`          |
| Region                     | Select an available region near you |
| Subscription               | Your Azure subscription             |
| Resource group             | `ResourceGroup1`                    |

> **Note:** Some Azure AI resources have regional model quotas. If you reach a quota limit later in the lab, you may need to create the resource in another region.

Select **Create** and wait for the project to finish creating.

## Step 4: Create the agent

After the project is created, a welcome dialog may appear.

1. Select **Next** to read the welcome information.
2. Select **Create agent**.

You can also:

1. Select **Start building** on the home page.
2. Select **Create agents** from the drop-down menu.

Set the agent name to:

```text
it-support-agent
```

Then create the agent.

The agent playground will open. An available deployed model should already be selected.

---

# 2. Configure the Agent

Now configure the agent with instructions and grounding data.

## Step 1: Add agent instructions

In the agent playground, set **Instructions** to:

```text
You are an IT Support Agent for Contoso Corporation.

You help employees with technical issues and IT policy questions.

Guidelines:

- Always be professional and helpful
- Use the IT policy documentation to answer questions accurately
- If you don't know the answer, admit it and suggest contacting IT support directly
- When creating tickets, collect all necessary information before proceeding
```

## Step 2: Download the IT policy document

Open a new browser tab and go to:

https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-agents/main/Labfiles/01-build-agent-portal-and-vscode/IT_Policy.txt

Save the file to your local computer.

The file contains sample IT policies covering:

- Password resets
- Software installation requests
- Hardware troubleshooting

## Step 3: Add tools

Return to the agent playground.

Under **Tools**:

1. Select **Add**.
2. Add:
   - **File search**
   - **Code interpreter**

## Step 4: Upload the IT policy file

Next to **Add**, select **Upload files**.

Under **Attach files**:

1. Browse to the `IT_Policy.txt` file you downloaded.
2. Upload it.
3. Select **Attach**.
4. Wait for the file to finish indexing.

You should see a confirmation when the file is ready.

## Step 5: Download the performance data

Download the system performance data file:

https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-agents/main/Labfiles/01-build-agent-portal-and-vscode/system_performance.csv

Save the file to your local computer.

The CSV contains simulated system metrics, including:

- CPU usage
- Memory usage
- Disk usage
- Measurements over time

## Step 6: Upload the performance data

Next to **Code interpreter**, select **+ Files**.

Upload:

```text
system_performance.csv
```

## Step 7: Save the agent

Select **Save** to save the agent configuration.

---

# 3. Test the Agent in Microsoft Foundry

Now test whether the agent can use the grounding data and code interpreter.

## Test 1: Password reset policy

In the chat area on the right side of the playground, enter:

```text
What's the policy for password resets?
```

The agent should use the IT policy document and provide accurate information about password reset procedures.

## Test 2: Software request

Try:

```text
How do I request new software?
```

Check the response and notice how the agent uses the uploaded IT policy as grounding data.

## Test 3: Analyze system performance

Now test the code interpreter:

```text
Can you analyze the system performance data and tell me if there are any concerning trends?
```

The agent should use the CSV file and code interpreter to analyze the system performance data.

## Test 4: Create a chart

Ask the agent:

```text
Create a chart showing CPU usage over time from the performance data
```

The agent should use the code interpreter to generate a visualization.

### What you have completed

At this point, you have created an agent with:

- Grounding data
- File search
- Code interpreter

Next, you will interact with the same agent programmatically from VS Code.

---

# 4. Interact with the Agent Using VS Code

As a developer, you may work in the Foundry portal, but you will also spend time working in Visual Studio Code.

The **Foundry Toolkit for VS Code** extension lets you work with Foundry project resources directly from VS Code.

---

# 5. Install and Configure the Foundry Toolkit

If the Foundry Toolkit extension is already installed, you can skip this section.

## Step 1: Open VS Code

Open **Visual Studio Code**.

## Step 2: Install the extension

Open **Extensions** from the left sidebar.

You can also press:

```text
Ctrl + Shift + X
```

Search for:

```text
Foundry Toolkit
```

Install the Microsoft extension.

> **Note:** The extension is currently listed as **Foundry Toolkit**, but some VS Code labels, commands, or older screenshots may still say **AI Toolkit**. In this lab, these names refer to the same extension experience.

## Step 3: Sign in

After installing the extension:

1. Select the **Foundry Toolkit** icon in the VS Code sidebar.
2. Sign in to your Azure account if prompted.

---

# 6. Test the Agent in VS Code

You can test your agent directly from the Foundry Toolkit without writing code.

## Step 1: Select the default project

Under **Microsoft Foundry Resources**:

1. Select **Set Default Project**.
2. Choose your project.

If a default project is already active, its name will appear in the resources list.

You can select the project icon again if you want to choose another project.

## Step 2: Open the agent

Expand the project section.

Under **Prompt Agents**, find:

```text
it-support-agent
```

Select the agent name to open **Agent Builder**.

The Agent Builder playground will open inside VS Code.

## Step 3: Test the agent

In the playground chat, enter:

```text
What is the policy for reporting a lost or stolen device?
```

Review the response.

The agent should use the grounding data that you uploaded earlier.

> **Tip:** The built-in playground is useful for quickly testing your agent's instructions and knowledge without writing code.

---

# 7. Create a Python Application

Now create a Python application that interacts with your agent programmatically.

## Step 1: Clone the lab repository

In VS Code, open the **Command Palette**.

You can use:

```text
Ctrl + Shift + P
```

or:

**View > Command Palette**

Search for:

```text
Git: Clone
```

Select it.

Enter the repository URL:

```text
https://github.com/MicrosoftLearning/mslearn-ai-agents.git
```

Choose a location on your computer where you want to clone the repository.

When VS Code asks whether you want to open the cloned repository, select **Open**.

## Step 2: Open the Python lab folder

In VS Code, select:

**File > Open Folder**

Navigate to:

```text
mslearn-ai-agents/Labfiles/01-build-agent-portal-and-vscode/Python
```

Select **Select Folder**.

## Step 3: Open the Python file

In the Explorer pane, open:

```text
agent_with_functions.py
```

If the file is empty, replace its contents with the following code.

> **Note:** The code logic is kept the same as the lab code.

```python
import base64
import os
from pathlib import Path

from azure.ai.projects import AIProjectClient
from azure.identity import DefaultAzureCredential
from dotenv import load_dotenv

OUTPUT_DIR = Path("agent_outputs")


def get_output_path(filename):
    """Create a unique path for generated files."""
    OUTPUT_DIR.mkdir(exist_ok=True)

    file_name = Path(filename).name
    stem = Path(file_name).stem or "output"
    suffix = Path(file_name).suffix

    output_path = OUTPUT_DIR / file_name

    counter = 1

    while output_path.exists():
        output_path = OUTPUT_DIR / f"{stem}_{counter}{suffix}"
        counter += 1

    return output_path


def save_bytes(file_bytes, filename):
    """Save binary content to a local file."""
    output_path = get_output_path(filename)

    with open(output_path, "wb") as file_handle:
        file_handle.write(file_bytes)

    return output_path


def save_image(image_data, filename):
    """Save base64 image data to a file."""
    return save_bytes(base64.b64decode(image_data), filename)


def download_container_file(openai_client, annotation, downloaded_files):
    """Download a cited container file once and return its local path."""
    cache_key = (annotation.container_id, annotation.file_id)

    if cache_key in downloaded_files:
        return downloaded_files[cache_key]

    file_content = openai_client.containers.files.content.retrieve(
        file_id=annotation.file_id,
        container_id=annotation.container_id,
    )

    output_path = save_bytes(
        file_content.read(),
        annotation.filename or f"{annotation.file_id}.bin",
    )

    downloaded_files[cache_key] = output_path

    return output_path


def format_output_text(content_item, openai_client, downloaded_files):
    """Replace sandbox file citations with local file paths."""
    text = content_item.text or ""

    replacements = []
    referenced_files = set()

    for annotation in content_item.annotations or []:

        if getattr(annotation, "type", "") != "container_file_citation":
            continue

        output_path = download_container_file(
            openai_client,
            annotation,
            downloaded_files,
        )

        replacement_text = (
            f"{annotation.filename} (saved to {output_path})"
        )

        referenced_files.add(output_path)

        start_index = getattr(annotation, "start_index", None)
        end_index = getattr(annotation, "end_index", None)

        if start_index is not None and end_index is not None:
            replacements.append(
                (start_index, end_index, replacement_text)
            )
            continue

        annotated_text = getattr(annotation, "text", "")

        if annotated_text:
            text = text.replace(
                annotated_text,
                replacement_text,
            )

    for start_index, end_index, replacement_text in sorted(
        replacements,
        reverse=True,
    ):
        text = (
            f"{text[:start_index]}"
            f"{replacement_text}"
            f"{text[end_index:]}"
        )

    return text, referenced_files


def main():

    # Initialize the project client

    load_dotenv()

    project_endpoint = os.environ.get("PROJECT_ENDPOINT")
    agent_name = os.environ.get(
        "AGENT_NAME",
        "it-support-agent",
    )

    if not project_endpoint:
        print("Error: PROJECT_ENDPOINT environment variable not set")
        print("Please set it in your .env file or environment")
        return

    print("Connecting to Microsoft Foundry project...")

    credential = DefaultAzureCredential()

    project_client = AIProjectClient(
        credential=credential,
        endpoint=project_endpoint
    )

    # Get the OpenAI client for Responses API

    openai_client = project_client.get_openai_client()

    # Get the agent created in the portal

    print(f"Loading agent: {agent_name}")

    agent = project_client.agents.get(
        agent_name=agent_name
    )

    print(
        f"Connected to agent: {agent.name} (id: {agent.id})"
    )

    # Create a conversation

    conversation = openai_client.conversations.create(
        items=[]
    )

    print(
        f"Conversation created (id: {conversation.id})"
    )

    # Chat loop

    print("\n" + "=" * 60)
    print("IT Support Agent Ready!")
    print("Ask questions, request data analysis, or get help.")
    print("Type 'exit' to quit.")
    print("=" * 60 + "\n")

    while True:

        user_input = input("You: ").strip()

        if user_input.lower() in ["exit", "quit", "bye"]:
            print("Goodbye!")
            break

        if not user_input:
            continue

        # Add user message to conversation

        openai_client.conversations.items.create(
            conversation_id=conversation.id,
            items=[
                {
                    "type": "message",
                    "role": "user",
                    "content": user_input,
                }
            ],
        )

        # Get response from agent

        print("\n[Agent is thinking...]")

        response = openai_client.responses.create(
            conversation=conversation.id,
            extra_body={
                "agent_reference": {
                    "name": agent.name,
                    "type": "agent_reference",
                }
            },
            input="",
        )

        # Display response and save any generated files locally

        handled_output = False
        downloaded_files = {}
        referenced_files = set()
        image_count = 0

        if hasattr(response, "output") and response.output:

            for item in response.output:

                item_type = getattr(item, "type", "")

                if (
                    item_type == "message"
                    and getattr(item, "content", None)
                ):

                    for content_item in item.content:

                        if (
                            getattr(content_item, "type", "")
                            != "output_text"
                        ):
                            continue

                        formatted_text, message_files = (
                            format_output_text(
                                content_item,
                                openai_client,
                                downloaded_files,
                            )
                        )

                        referenced_files.update(message_files)

                        if formatted_text:
                            print(
                                f"\nAgent: {formatted_text}\n"
                            )
                            handled_output = True

                elif hasattr(item, "text") and item.text:

                    print(f"\nAgent: {item.text}\n")
                    handled_output = True

                elif item_type == "image":

                    image_count += 1
                    filename = f"chart_{image_count}.png"

                    if (
                        hasattr(item, "image")
                        and hasattr(item.image, "data")
                    ):

                        file_path = save_image(
                            item.image.data,
                            filename,
                        )

                        print(
                            "\n[Agent generated a chart - "
                            f"saved to: {file_path}]"
                        )

                    else:
                        print(
                            "\n[Agent generated an image]"
                        )

                    handled_output = True

            for file_path in downloaded_files.values():

                if file_path not in referenced_files:

                    print(
                        "\n[Agent generated a file - "
                        f"saved to: {file_path}]"
                    )

                    handled_output = True

        if (
            not handled_output
            and hasattr(response, "output_text")
            and response.output_text
        ):

            print(
                f"\nAgent: {response.output_text}\n"
            )


if __name__ == "__main__":
    main()
```

Save the file with:

```text
Ctrl + S
```

---

# 8. Configure the Environment

In the Explorer pane, you should already have:

```text
.env.example
requirements.txt
```

## Step 1: Create the `.env` file

Duplicate:

```text
.env.example
```

Rename the copy to:

```text
.env
```

## Step 2: Add your project endpoint

Open the `.env` file.

Replace `your_project_endpoint_here` with your actual Foundry project endpoint:

```text
PROJECT_ENDPOINT=<your_project_endpoint>
AGENT_NAME=it-support-agent
```

### How to find the project endpoint

In VS Code:

1. Open the **Foundry Toolkit** extension.
2. Right-click your active project.
3. Select **Copy Endpoint**.

If **Copy Endpoint** is not available in your version of Foundry Toolkit:

1. Open the Microsoft Foundry portal.
2. Open your project.
3. Go to the project overview page.
4. Copy the project endpoint.

Save the `.env` file.

---

# 9. Install Dependencies and Log In

Open a terminal in VS Code:

**Terminal > New Terminal**

Navigate to the working directory.

## Step 1: Create a virtual environment

```bash
python -m venv labenv
```

## Step 2: Activate the virtual environment

On Windows PowerShell:

```powershell
.\labenv\Scripts\Activate.ps1
```

## Step 3: Install the required packages

```bash
pip install -r requirements.txt
```

## Step 4: Sign in to Azure

```bash
az login
```

## Step 5: Run the application

```bash
python agent_with_functions.py
```

---

# 10. Test the Python Application

When the application starts, you should see a message similar to:

```text
IT Support Agent Ready!
Ask questions, request data analysis, or get help.
Type 'exit' to quit.
```

Now test the agent with different prompts.

## Test 1: Search the IT policy

```text
What's the policy for password resets?
```

The agent should use **File Search** to find the answer in the IT policy document.

## Test 2: Analyze CPU usage

```text
Analyze the system performance data and identify any periods where CPU usage exceeded 80%
```

The agent should use the **Code Interpreter** to analyze the CSV data.

## Test 3: Create a visualization

```text
Create a line chart showing memory usage trends over time
```

The agent should generate a chart.

Generated charts and cited files are saved in:

```text
agent_outputs
```

The application also prints the local file path in the terminal.

## Test 4: Statistical analysis

Ask:

```text
What are the average, minimum, and maximum values for disk usage in the performance data?
```

The code interpreter should calculate the requested statistics.

## Test 5: Combined analysis

Ask:

```text
Find any correlation between high CPU usage and memory usage in the performance data
```

The agent should analyze the performance data and look for a relationship between CPU and memory usage.

---

# 11. What You Have Learned

You have now built and tested an AI agent using Microsoft Foundry.

The agent can:

- Answer IT policy questions using **File Search**.
- Use uploaded documents as grounding data.
- Analyze CSV data using **Code Interpreter**.
- Perform calculations and statistical analysis.
- Generate charts and other files.
- Be tested in the Microsoft Foundry portal.
- Be tested in the Foundry Toolkit for VS Code.
- Be accessed programmatically from a Python application.

The important flow is:

```text
Microsoft Foundry Project
        ↓
Create Agent
        ↓
Add Instructions
        ↓
Add File Search
        ↓
Upload IT Policy
        ↓
Add Code Interpreter
        ↓
Upload Performance CSV
        ↓
Test Agent in Foundry
        ↓
Open Agent in VS Code
        ↓
Create Python Application
        ↓
Connect Python Application to Agent
        ↓
Test File Search + Code Interpreter
```

---

# 12. Cleanup

To avoid unnecessary Azure charges, delete the resources you created after finishing the lab.

## Option 1: Delete the Foundry project

In the Foundry portal:

1. Open your project.
2. Go to **Settings**.
3. Select **Delete project**.

## Option 2: Delete the resource group

Alternatively, open the Azure portal and delete the entire resource group.

---

# End of Lab
