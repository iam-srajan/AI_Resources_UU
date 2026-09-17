# Custom Function Tools Lab — Simple Guide

This lab builds an **astronomy assistant agent**. It can:

- Look up the next visible astronomical event for a continent
- Calculate telescope rental costs
- Generate a text report summarizing an observation

The agent does this by calling **custom Python functions** you define, exposed to it as "function tools."

> **Note on the code below:** Your original paste had some characters mangled by markdown formatting (underscores turned into `*`, and `__name__` / `__main__` turned into bold `**name**` / `**main**`). I've restored those to valid Python. Nothing about the logic or naming was changed otherwise.

---

## Setup steps (do these once)

1. **Install the Foundry Toolkit extension** in VS Code (Extensions panel → search "Foundry Toolkit" → Install). Older labels may still say "AI Toolkit" — same thing.
2. **Sign in** to Azure when prompted, then in the Foundry Toolkit pane select **Create Project**.
   - Resource group: `ResourceGroup1`
   - Project name: `ai-agents-project65114867`
3. **Deploy a model**: after the project is created, click **Deploy a new model** → search `gpt-5` → **Deploy**.
   - Deployment name: `gpt-5`
   - Deployment type: `Global Standard` (or `Standard` if that's not available)
   - Model version: leave as default
   - Tokens per minute: leave as default
   - Click **Deploy to Microsoft Foundry**
4. **Copy your project endpoint**: right-click the deployment → **Copy Project Endpoint**. (Or find it in the Foundry Portal at https://ai.azure.com under your project's endpoint field.)
5. **Clone the lab repo**:
   ```
   https://github.com/MicrosoftLearning/mslearn-ai-agents.git
   ```
   Open the folder `mslearn-ai-agents/Labfiles/02-agent-custom-tools` in VS Code, then expand the **Python** folder.
6. **Set up your Python environment** — right-click `requirements.txt` → **Open in Integrated Terminal**, then run:
   ```powershell
   python -m venv labenv
   .\labenv\Scripts\Activate.ps1
   pip install -r requirements.txt
   ```
   > This lab needs **Python 3.13** — 3.14 isn't supported yet because some dependencies don't have a 3.14 build.
7. **Edit the `.env` file**: paste your project endpoint into `PROJECT_ENDPOINT`, and make sure `MODEL_DEPLOYMENT_NAME` matches your deployment name (`gpt-5`). Save with `Ctrl+S`.

---

## Step 1: Add the event-lookup function

Open `functions.py`. This file already has some helper code and data (like the `EVENTS` list and existing imports) set up by the starter repo. Find the comment `Determine the next visible astronomical event for a given location` and add this function:

```python
# Determine the next visible astronomical event for a given location
def next_visible_event(location: str) -> str:
    """Returns the next visible astronomical event for a location."""
    today = int(datetime.now().strftime("%m%d"))
    loc = location.lower().replace(" ", "_")

    # Retrieve the next event visible from the location, starting with events later this year
    for name, event_type, date, date_str, locs in EVENTS:
        if loc in locs and date >= today:
            return json.dumps({
                "event": name,
                "type": event_type,
                "date": date_str,
                "visible_from": sorted(locs),
            })

    return json.dumps({"message": f"No upcoming events found for {location}."})
```

This looks through the sample event data for the next event visible from the given location and returns it as a JSON string. Save the file (`Ctrl+S`).

---

## Step 2: Build the agent (`agent.py`)

The agent needs three tools defined and wired up: `next_visible_event`, `calculate_observation_cost`, and `generate_observation_report`. The full assembled file is below — you don't need to type it piece by piece if you're using the version in this guide, but if you're following along step-by-step in the lab UI, each labeled comment block corresponds to one step in the official instructions (Add references → Connect to the project client → Define the event/cost/report function tools → Create the agent → chat loop → process function calls → send outputs back → delete the agent).

---

## Step 3: Run it

```powershell
az login
python agent.py
```

Try a prompt like:

```
Find me the next event I can see from South America and give me the cost for 5 hours of premium telescope time at normal priority.
```

The agent should call both `next_visible_event` and `calculate_observation_cost` in the same turn and combine the results into an answer.

Then try a follow-up:

```
Generate that information in a report for Bellows College.
```

This calls `generate_observation_report`, and you'll see a new `report-<event-type>.txt` file appear in your project folder with the summary.

Type `quit` to exit. Use `deactivate` to leave the virtual environment when you're done.

---

## Cleanup

- In VS Code's Azure Resources view, expand **Models**, right-click your deployed model → **Delete**.
- In the Azure Portal, open your resource group and select **Delete resource group** to remove everything else.
- Remember to **end the lab** in the Microsoft Learn portal.

---

## Complete Code

### `functions.py` (the function you add)

```python
# Determine the next visible astronomical event for a given location
def next_visible_event(location: str) -> str:
    """Returns the next visible astronomical event for a location."""
    today = int(datetime.now().strftime("%m%d"))
    loc = location.lower().replace(" ", "_")

    # Retrieve the next event visible from the location, starting with events later this year
    for name, event_type, date, date_str, locs in EVENTS:
        if loc in locs and date >= today:
            return json.dumps({
                "event": name,
                "type": event_type,
                "date": date_str,
                "visible_from": sorted(locs),
            })

    return json.dumps({"message": f"No upcoming events found for {location}."})
```

> The rest of `functions.py` (imports like `datetime`/`json`, the `EVENTS` data, `calculate_observation_cost`, and `generate_observation_report`) already exists in the starter repo and wasn't included in what you pasted, so it isn't reproduced here — only the function this lab step asks you to add.

### `agent.py`

```python
import os
import json

from dotenv import load_dotenv

# Add references
from azure.ai.projects import AIProjectClient
from azure.ai.projects.models import FunctionTool
from azure.identity import DefaultAzureCredential
from azure.ai.projects.models import PromptAgentDefinition, FunctionTool
from openai.types.responses.response_input_param import (
    FunctionCallOutput,
    ResponseInputParam,
)

from functions import (
    next_visible_event,
    calculate_observation_cost,
    generate_observation_report,
)


def main():
    # Clear the console
    os.system("cls" if os.name == "nt" else "clear")

    # Load environment variables from .env file
    load_dotenv()

    project_endpoint = os.getenv("PROJECT_ENDPOINT")
    model_deployment = os.getenv("MODEL_DEPLOYMENT_NAME")

    # Connect to the project client
    with (
        DefaultAzureCredential() as credential,
        AIProjectClient(
            endpoint=project_endpoint,
            credential=credential,
        ) as project_client,
        project_client.get_openai_client() as openai_client,
    ):

        # Define the event function tool
        event_tool = FunctionTool(
            name="next_visible_event",
            description="Get the next visible event in a given location.",
            parameters={
                "type": "object",
                "properties": {
                    "location": {
                        "type": "string",
                        "description": "continent to find the next visible event in (e.g. 'north_america', 'south_america', 'australia')",
                    },
                },
                "required": ["location"],
                "additionalProperties": False,
            },
            strict=True,
        )

        # Define the observation cost function tool
        cost_tool = FunctionTool(
            name="calculate_observation_cost",
            description="Calculate the cost of an observation based on the telescope tier, number of hours, and priority level.",
            parameters={
                "type": "object",
                "properties": {
                    "telescope_tier": {
                        "type": "string",
                        "description": "the tier of the telescope (e.g. 'standard', 'advanced', 'premium')",
                    },
                    "hours": {
                        "type": "number",
                        "description": "the number of hours for the observation",
                    },
                    "priority": {
                        "type": "string",
                        "description": "the priority level of the observation (e.g. 'low', 'normal', 'high')",
                    },
                },
                "required": ["telescope_tier", "hours", "priority"],
                "additionalProperties": False,
            },
            strict=True,
        )

        # Define the observation report generation function tool
        report_tool = FunctionTool(
            name="generate_observation_report",
            description="Generate a report summarizing an astronomical observation",
            parameters={
                "type": "object",
                "properties": {
                    "event_name": {
                        "type": "string",
                        "description": "the name of the astronomical event being observed",
                    },
                    "location": {
                        "type": "string",
                        "description": "the location of the observer",
                    },
                    "telescope_tier": {
                        "type": "string",
                        "description": "the tier of the telescope used for the observation (e.g. 'standard', 'advanced', 'premium')",
                    },
                    "hours": {
                        "type": "number",
                        "description": "the number of hours the telescope was used for the observation",
                    },
                    "priority": {
                        "type": "string",
                        "description": "the priority level of the observation (e.g. 'low', 'normal', 'high')",
                    },
                    "observer_name": {
                        "type": "string",
                        "description": "the name of the person who conducted the observation",
                    },
                },
                "required": [
                    "event_name",
                    "location",
                    "telescope_tier",
                    "hours",
                    "priority",
                    "observer_name",
                ],
                "additionalProperties": False,
            },
            strict=True,
        )

        # Create a new agent with the function tools
        agent = project_client.agents.create_version(
            agent_name="astronomy-agent",
            definition=PromptAgentDefinition(
                model=model_deployment,
                instructions="""
                    You are an astronomy observations assistant that helps users find
                    information about astronomical events and calculate telescope rental costs.
                    Use the available tools to assist users with their inquiries.
                """,
                tools=[event_tool, cost_tool, report_tool],
            ),
        )

        # Create a thread for the chat session
        conversation = openai_client.conversations.create()

        # Create a list to hold function call outputs that will be sent back as input to the agent
        input_list: ResponseInputParam = []

        while True:
            user_input = input(
                "Enter a prompt for the astronomy agent. Use 'quit' to exit.\nUSER: "
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

            # Retrieve the agent's response, which may include function calls
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
                    result = None

                    if item.name == "next_visible_event":
                        result = next_visible_event(
                            **json.loads(item.arguments)
                        )

                    elif item.name == "calculate_observation_cost":
                        result = calculate_observation_cost(
                            **json.loads(item.arguments)
                        )

                    elif item.name == "generate_observation_report":
                        result = generate_observation_report(
                            **json.loads(item.arguments)
                        )

                    # Append the output text
                    input_list.append(
                        FunctionCallOutput(
                            type="function_call_output",
                            call_id=item.call_id,
                            output=result,
                        )
                    )

            # Send function call outputs back to the model and retrieve a response
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

            # Display the agent's response
            print(f"AGENT: {response.output_text}")

        # Delete the agent when done
        project_client.agents.delete_version(
            agent_name=agent.name,
            agent_version=agent.version,
        )

        print("Deleted agent.")


if __name__ == "__main__":
    main()
```
