# MCP Integration Lab — Simple Guide

This lab has two parts:

1. **Part 1 — `agent.py`**: Connect an Azure AI Agent to a _remote_ MCP server (Microsoft Learn Docs) so it can answer questions using official documentation.
2. **Part 2 — `server.py` + `client.py`**: Build your _own_ MCP server with custom inventory tools, and a client that connects your agent to it.

---

## Part 1: Connect to a remote MCP server

### What's happening

`agent.py` creates an AI agent, gives it a "tool" that points at Microsoft's public Docs MCP server, then sends it a question. The agent decides on its own to use that tool to look up real documentation before answering.

### Setup steps (do these once)

1. **Install the Foundry Toolkit extension** in VS Code (Extensions panel → search "Foundry Toolkit" → Install). Older labels may say "AI Toolkit" — same thing.
2. **Sign in** to Azure when prompted, then in the Foundry Toolkit pane select **Create Project**.
   - Resource group: `ResourceGroup1`
   - Project name: `ai-agents-project65116950`
3. **Deploy a model**: after the project is created, click **Deploy a new model** → search `gpt-5` → **Deploy**.
   - Deployment name: `gpt-5`
   - Deployment type: `Global Standard` (or `Standard` if that's not available)
   - Tokens per minute: `150000` or higher
   - Click **Deploy to Microsoft Foundry**
4. **Copy your project endpoint**: right-click the deployment → **Copy Project Endpoint**. (Or find it in the Foundry Portal at https://ai.azure.com under your project's endpoint field.)
5. **Clone the lab repo**:
   ```
   https://github.com/MicrosoftLearning/mslearn-ai-agents.git
   ```
   Open the folder `mslearn-ai-agents/Labfiles/03-mcp-integration` in VS Code.
6. **Set up your Python environment** — right-click `requirements.txt` → **Open in Integrated Terminal**, then run:
   ```powershell
   python -m venv labenv
   .\labenv\Scripts\Activate.ps1
   pip install -r requirements.txt
   ```
7. **Edit the `.env` file**: paste your project endpoint into `PROJECT_ENDPOINT`, and make sure `MODEL_DEPLOYMENT_NAME` matches your deployment name (`gpt-5`). Save with `Ctrl+S`.

### Run it

```powershell
az login
python agent.py
```

The agent will automatically call the Microsoft Learn Docs MCP tool and print an answer built from real documentation, then clean itself up.

---

## Part 2: Build a custom MCP server + client

### What's happening

- `server.py` starts a small MCP server with two custom tools: one that returns fake inventory numbers, one that returns fake weekly sales numbers.
- `client.py` starts that server as a background process, connects to it, wraps its tools so an AI agent can call them, and gives you a chat prompt to ask questions like "what needs restocking?"

### Run it

1. Make sure your venv is active (`.\labenv\Scripts\Activate.ps1`).
2. Just run the client — **do not run `server.py` on its own at the same time**, since `client.py` launches it for you internally:
   ```powershell
   python client.py
   ```
3. Try prompts like:
   - `Show me the current inventory levels for all products.`
   - `Are there any products that should be restocked?`
   - `Which products would you recommend for clearance?`
   - `What are the best sellers this week?`
4. Type `quit` to exit.

### Cleanup (after you're done with the whole lab)

- In VS Code's Azure Resources view, expand **Models**, right-click your deployed model → **Delete**.
- In the Azure Portal, open your resource group and select **Delete resource group** to remove everything else.

---

## Complete Code

### `agent.py`

```python
import os

from dotenv import load_dotenv

# Add references
from azure.identity import DefaultAzureCredential
from azure.ai.projects import AIProjectClient
from azure.ai.projects.models import PromptAgentDefinition, MCPTool
from openai.types.responses.response_input_param import (
    McpApprovalResponse,
    ResponseInputParam,
)


# Load environment variables from .env file
load_dotenv()

project_endpoint = os.getenv("PROJECT_ENDPOINT")
model_deployment = os.getenv("MODEL_DEPLOYMENT_NAME")


# Connect to the agents client
with (
    DefaultAzureCredential() as credential,
    AIProjectClient(
        endpoint=project_endpoint,
        credential=credential,
    ) as project_client,
    project_client.get_openai_client() as openai_client,
):

    # Initialize agent MCP tool
    mcp_tool = MCPTool(
        server_label="api-specs",
        server_url="https://learn.microsoft.com/api/mcp",
        require_approval="always",
    )

    # Create a new agent with the MCP tool
    agent = project_client.agents.create_version(
        agent_name="MyAgent",
        definition=PromptAgentDefinition(
            model=model_deployment,
            instructions=(
                "You are a helpful agent that can use MCP tools to assist users. "
                "Use the available MCP tools to answer questions and perform tasks."
            ),
            tools=[mcp_tool],
        ),
    )

    print(
        f"Agent created (id: {agent.id}, "
        f"name: {agent.name}, version: {agent.version})"
    )

    # Create conversation thread
    conversation = openai_client.conversations.create()

    print(f"Created conversation (id: {conversation.id})")

    # Send initial request that will trigger the MCP tool
    response = openai_client.responses.create(
        conversation=conversation.id,
        input=(
            "Give me the Azure CLI commands to create an Azure Container App "
            "with a managed identity."
        ),
        extra_body={
            "agent_reference": {
                "name": agent.name,
                "type": "agent_reference",
            }
        },
    )

    # Process any MCP approval requests that were generated
    # The agent may issue several tool calls, each needing its own approval,
    # so we loop until there are none left.
    while True:

        # Collect any MCP approval requests from the latest response
        input_list: ResponseInputParam = []

        for item in response.output:

            if item.type == "mcp_approval_request":

                if item.server_label == "api-specs" and item.id:

                    # Automatically approve the MCP request
                    # to allow the agent to proceed
                    input_list.append(
                        McpApprovalResponse(
                            type="mcp_approval_response",
                            approve=True,
                            approval_request_id=item.id,
                        )
                    )

        # No more approvals needed -> the agent has produced
        # its final response
        if not input_list:
            break

        # Send the approval response back and retrieve the next response
        response = openai_client.responses.create(
            input=input_list,
            previous_response_id=response.id,
            extra_body={
                "agent_reference": {
                    "name": agent.name,
                    "type": "agent_reference",
                }
            },
        )

    print(f"\nAgent response: {response.output_text}")

    # Clean up resources by deleting the agent version
    project_client.agents.delete_version(
        agent_name=agent.name,
        agent_version=agent.version,
    )

    print("Agent deleted")
```

### `server.py`

```python
# Add references
from fastmcp import FastMCP

# Create an MCP server
mcp = FastMCP(name="Inventory")

# Add an inventory check mcp tool
@mcp.tool()
def get_inventory_levels() -> dict:
    """Returns current inventory for all products."""
    return {
        "Moisturizer": 6,
        "Shampoo": 8,
        "Body Spray": 28,
        "Hair Gel": 5,
        "Lip Balm": 12,
        "Skin Serum": 9,
        "Cleanser": 30,
        "Conditioner": 3,
        "Setting Powder": 17,
        "Dry Shampoo": 45
    }

# Add a weekly sales mcp tool
@mcp.tool()
def get_weekly_sales() -> dict:
    """Returns number of units sold last week."""
    return {
        "Moisturizer": 22,
        "Shampoo": 18,
        "Body Spray": 3,
        "Hair Gel": 2,
        "Lip Balm": 14,
        "Skin Serum": 19,
        "Cleanser": 4,
        "Conditioner": 1,
        "Setting Powder": 13,
        "Dry Shampoo": 17
    }

# Run the MCP server
mcp.run(show_banner=False)
```

### `client.py`

> Note: `command` uses `sys.executable` and `env` uses a full copy of your environment (instead of `"python"` / `None`). This guarantees the server subprocess launches with the same virtual environment as the client, so it can find installed packages like `fastmcp`. Everything else matches the lab's original logic and naming exactly.

```python
import os
import sys
import asyncio
import json

from dotenv import load_dotenv
from contextlib import AsyncExitStack
from azure.ai.projects import AIProjectClient
from azure.ai.projects.models import FunctionTool
from azure.identity import DefaultAzureCredential
from azure.ai.projects.models import PromptAgentDefinition, FunctionTool
from openai.types.responses.response_input_param import (
    FunctionCallOutput,
    ResponseInputParam,
)

# Add references
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

# Clear the console
os.system("cls" if os.name == "nt" else "clear")

# Load environment variables from .env file
load_dotenv()
project_endpoint = os.getenv("PROJECT_ENDPOINT")
model_deployment = os.getenv("MODEL_DEPLOYMENT_NAME")


async def connect_to_server(exit_stack: AsyncExitStack):
    server_params = StdioServerParameters(
        command=sys.executable,
        args=["server.py"],
        env=os.environ.copy(),
    )
    # Start the MCP server
    stdio_transport = await exit_stack.enter_async_context(
        stdio_client(server_params)
    )
    stdio, write = stdio_transport
    # Create an MCP client session
    session = await exit_stack.enter_async_context(
        ClientSession(stdio, write)
    )
    await session.initialize()
    # List available tools
    response = await session.list_tools()
    tools = response.tools
    print(
        "\nConnected to server with tools:",
        [tool.name for tool in tools],
    )
    return session


async def chat_loop(session):
    # Connect to the agents client
    with (
        DefaultAzureCredential() as credential,
        AIProjectClient(
            endpoint=project_endpoint,
            credential=credential,
        ) as project_client,
        project_client.get_openai_client() as openai_client,
    ):
        # Get the mcp tools available from the server
        response = await session.list_tools()
        tools = response.tools

        # Build a function for each tool
        def make_tool_func(tool_name):
            async def tool_func(**kwargs):
                result = await session.call_tool(tool_name, kwargs)
                return result
            tool_func.__name__ = tool_name
            return tool_func

        # Store the functions in a dictionary for easy access
        # when processing function calls
        functions_dict = {
            tool.name: make_tool_func(tool.name)
            for tool in tools
        }

        # Create FunctionTool definitions for the agent
        mcp_function_tools: FunctionTool = []
        for tool in tools:
            function_tool = FunctionTool(
                name=tool.name,
                description=tool.description,
                parameters={
                    "type": "object",
                    "properties": {},
                    "additionalProperties": False,
                },
                strict=True,
            )
            mcp_function_tools.append(function_tool)

        # Create the agent
        agent = project_client.agents.create_version(
            agent_name="inventory-agent",
            definition=PromptAgentDefinition(
                model=model_deployment,
                instructions="""
                You are an inventory assistant. Here are some general guidelines:
                - Recommend restock if item inventory < 10  and weekly sales > 15
                - Recommend clearance if item inventory > 20 and weekly sales < 5
                """,
                tools=mcp_function_tools,
            ),
        )

        # Create a thread for the chat session
        conversation = openai_client.conversations.create()

        # Create an input list to hold function call outputs
        # to send back to the model
        input_list: ResponseInputParam = []

        while True:
            user_input = input(
                "Enter a prompt for the inventory agent. Use 'quit' to exit.\nUSER: "
            ).strip()

            if user_input.lower() == "quit":
                print("Exiting chat.")
                break

            # Send a prompt to the agent
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

            # Retrieve the agent's response, which may include
            # function calls to the MCP server tools
            response = openai_client.responses.create(
                conversation=conversation.id,
                extra_body={
                    "agent_reference": {
                        "name": agent.name,
                        "type": "agent_reference",
                    }
                },
                input=input_list,
            )

            # Check the run status for failures
            if response.status == "failed":
                print(f"Response failed: {response.error}")

            # Process function calls
            for item in response.output:
                if item.type == "function_call":
                    # Retrieve the matching function tool
                    function_name = item.name
                    kwargs = json.loads(item.arguments)
                    required_function = functions_dict.get(function_name)

                    # Invoke the function
                    output = await required_function(**kwargs)

                    # Append the output text
                    input_list.append(
                        FunctionCallOutput(
                            type="function_call_output",
                            call_id=item.call_id,
                            output=output.content[0].text,
                        )
                    )

            # Send function call outputs back to the model
            # and retrieve a response
            if input_list:
                response = openai_client.responses.create(
                    input=input_list,
                    previous_response_id=response.id,
                    extra_body={
                        "agent_reference": {
                            "name": agent.name,
                            "type": "agent_reference",
                        }
                    },
                )

            print(f"Agent response: {response.output_text}")

        # Delete the agent when done
        print("Cleaning up agents:")
        project_client.agents.delete_version(
            agent_name=agent.name,
            agent_version=agent.version,
        )
        print("Deleted inventory agent.")


async def main():
    exit_stack = AsyncExitStack()
    try:
        session = await connect_to_server(exit_stack)
        await chat_loop(session)
    finally:
        await exit_stack.aclose()


if __name__ == "__main__":
    asyncio.run(main())
```
