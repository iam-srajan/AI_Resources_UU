# Prepare for an AI Development Project

In this exercise, you will use the **Microsoft Foundry portal** to create an AI project, deploy a generative AI model, test it, and connect to the project from **Visual Studio Code**.

**Estimated time:** 30 minutes

> **Note:** Some technologies used in this exercise are in preview or active development. You may encounter unexpected behavior, warnings, or errors.

---

# 1. Prerequisites

Before starting the exercise, make sure you have the following installed and configured:

- An active **Azure subscription**
- **Visual Studio Code**
- **Python 3.13.x**
- **Git**
- **Azure CLI**

### Python version

Python 3.14 is available, but some dependencies may not yet be compiled for Python 3.14.

For this lab, use:

```text
Python 3.13.12
```

> **Recommendation:** If you already have Python 3.14 installed, use Python 3.13 for this exercise to avoid dependency-related problems.

---

# 2. Create a Microsoft Foundry Project

Microsoft Foundry uses **projects** to organize the models, resources, data, and other assets required to build an AI solution.

## Step 2.1 — Open Microsoft Foundry

Open a web browser and go to:

[Microsoft Foundry Portal](https://ai.azure.com?utm_source=chatgpt.com)

Sign in using the Azure credentials provided for your lab environment.

> **Security note:** Do not share your Azure username or password publicly. If credentials are provided by your training environment, use those credentials only for the lab.

After signing in:

1. Close any **Tips**, **Quick Start**, or introductory panels that appear.
2. Look at the toolbar at the top of the page.
3. If the **New Foundry** option is available but disabled, enable it.

---

## Step 2.2 — Create the Project

Create a new project with the following settings:

| Setting              | Value                                   |
| -------------------- | --------------------------------------- |
| **Project name**     | `Project64771178`                       |
| **Foundry resource** | `Project64771178-resource`              |
| **Subscription**     | Your Azure subscription                 |
| **Resource group**   | `ResourceGroup1`                        |
| **Region**           | Select an AI Foundry recommended region |

### Important

When selecting the region, **make a note of the region you choose**.

You may need this information later when connecting to your project or working with Azure resources.

After entering the required information:

1. Select **Create**.
2. Wait for Azure to finish creating the project.
3. When the project is ready, the **project home page** should open automatically.

---

# 3. Deploy and Test a Generative AI Model

A generative AI project normally requires at least one generative AI model.

In this section, you will find a model in the Microsoft Foundry model catalog, deploy it, and test it.

## Step 3.1 — Open the Model Catalog

From the Microsoft Foundry portal:

1. Open the **Discover** page.
2. Select the **Models** tab.
3. Browse the Microsoft Foundry model catalog.

---

## Step 3.2 — Find the GPT-5.2 Model

Use the search box to search for:

```text
gpt-5.2
```

Select **GPT-5.2** from the search results.

You will see the model's **model card**.

### What is a model card?

A model card provides useful information about a model, including:

- What the model can do
- Its capabilities
- Its limitations
- Recommended use cases
- Information that can help you decide whether the model is suitable for your application

Review the model information before deploying it.

---

## Step 3.3 — Deploy the Model

Select:

**Deploy**

Use the **default deployment settings** unless the lab specifically asks you to change them.

The deployment process creates a model deployment that your project can use.

Wait for the deployment to complete.

Once the model has been deployed successfully, the **Model Playground** should open automatically.

---

# 4. Test the Model in the Playground

The Model Playground allows you to interact with the deployed model without writing any code.

## Step 4.1 — Add Instructions

In the **Instructions** box, enter:

```text
You are an AI assistant that can provide information and advice about AI software development.
```

---

## Step 4.2 — Send a Question

In the chat window, enter a question such as:

```text
Describe three key considerations for working with Large Language Models for AI application development.
```

Send the message and review the model's response.

The model should provide several considerations that are important when building applications using Large Language Models (LLMs).

### What did you just do?

You have now:

1. Created a Microsoft Foundry project.
2. Deployed a generative AI model.
3. Configured instructions for the model.
4. Sent a prompt to the model.
5. Viewed the generated response.

This is the basic workflow used when experimenting with generative AI models.

---

# 5. View the Foundry Resource and Project Endpoints

Microsoft Foundry has both **resource-level** and **project-level** concepts.

Understanding the difference is important when developing applications.

## Step 5.1 — Open the Management Center

In the Microsoft Foundry portal:

1. Select **Manage** from the top menu.
2. The **Management Center** will open.

The Management Center allows you to view and manage your projects and their associated Azure resources.

---

## Step 5.2 — Understand Resource Level vs. Project Level

### Foundry Resource

The **Foundry resource** is the Azure resource created to support your Foundry projects.

It can provide:

- Connections to Foundry services
- Access to models
- Resource-level configuration
- User access management

A single Foundry resource can support multiple projects.

---

### Foundry Project

The **project** is the workspace where you work on a specific AI solution.

A project can contain or use:

- Model deployments
- Data
- Connections
- Agents
- Tools
- Other project-specific assets

Think of it this way:

```text
Azure Subscription
        │
        ▼
Resource Group
        │
        ▼
Foundry Resource
        │
        ├── Project 1
        ├── Project 2
        └── Project 3
```

The exact structure and available features can vary as Microsoft Foundry evolves.

---

## Step 5.3 — Open the Parent Resource

In the Management Center:

1. Find your project.
2. Select the **Parent resource** associated with the project.
3. Open the resource configuration details.

You should see information about the Foundry resource.

### Resource Endpoint

The Foundry resource has an **endpoint**.

This endpoint can be used by client applications to access resource-level functionality, including Foundry tools and services that are shared across projects.

---

# 6. Find Your Project Endpoints

Return to the project home page by selecting **Home** from the top menu.

Look for the connection information associated with your project.

Depending on the current Foundry interface, you may see information such as:

- **Key**
- **Project endpoint**
- **Azure OpenAI endpoint**

These values can be used by client applications to connect to your Azure AI resources.

---

## What does each endpoint mean?

### 1. Key

A key can be used for **key-based authentication** when accessing models and tools.

For production applications, Microsoft generally recommends using **Microsoft Entra ID authentication** where appropriate instead of relying on long-lived keys.

> **Security:** Never publish or share API keys, passwords, or other credentials in GitHub repositories, screenshots, or public messages.

---

### 2. Project Endpoint

The **project endpoint** provides access to models and Foundry-specific capabilities.

It can be used with the **OpenAI Responses API** to access models provided through Foundry, including OpenAI models.

It can also be used with Foundry-specific services, such as the **Foundry Agent Service**.

---

### 3. Azure OpenAI Endpoint

The **Azure OpenAI endpoint** is used to access models through Azure OpenAI APIs.

Depending on the API and SDK being used, this can include APIs such as:

- Chat Completions API
- Responses API

---

# 7. Install the Foundry Toolkit Extension for Visual Studio Code

As a developer, you may use the Foundry portal for managing resources and experimenting with models.

However, you will probably spend much of your development time in **Visual Studio Code**.

The **Foundry Toolkit for VS Code** makes it easier to work with Foundry projects and resources directly from Visual Studio Code.

---

## Step 7.1 — Open Visual Studio Code

Start **Visual Studio Code**.

---

## Step 7.2 — Open Extensions

In the Visual Studio Code left navigation bar:

1. Select **Extensions**.
2. Search for:

```text
Foundry Toolkit
```

3. Find the **Foundry Toolkit for VS Code** extension.
4. Select **Install**.

The installation may take a minute or two.

---

# 8. Connect Visual Studio Code to Your Foundry Project

After installing the extension:

1. Open the **Foundry Toolkit** from the Visual Studio Code navigation bar.
2. Wait for the extension to load.
3. Expand:

```text
Microsoft Foundry Resources
```

4. Set your **default project**.

You will be asked to connect to Azure.

Sign in using your Azure account and select the Foundry project you created earlier.

Select:

```text
Project64771178
```

Your Visual Studio Code environment should now be connected to the project.

---

# 9. View the Deployed Model in Visual Studio Code

After setting the default project:

1. Expand the project.
2. Expand **Models**.
3. Find the model you deployed earlier:

```text
gpt-5.2
```

4. Select the model.

You should be able to view information about the model deployment.

This allows you to inspect your Foundry resources without leaving Visual Studio Code.

---

# 10. Open the Model Playground in Visual Studio Code

The Foundry Toolkit also provides a model playground inside Visual Studio Code.

To open it:

1. In the **Foundry Toolkit** pane, find **Developer Tools**.
2. Expand **Build**.
3. Select **Model Playground**.
4. Select the **gpt-5.2** model if it is not already selected.

An interactive playground will open inside Visual Studio Code.

You can now test the model directly from your development environment.

---

# 11. What You Have Learned

By completing this exercise, you have learned how to:

- Create a **Microsoft Foundry project**
- Understand the relationship between a **Foundry resource** and a **project**
- Explore the **Microsoft Foundry model catalog**
- Deploy a generative AI model
- Test a model using the **Model Playground**
- Configure model instructions
- Understand **project and resource endpoints**
- Install the **Foundry Toolkit for Visual Studio Code**
- Connect Visual Studio Code to your Foundry project
- View deployed models from Visual Studio Code
- Test a model using the Visual Studio Code playground

### Basic workflow

You can think of the entire exercise as:

```text
Azure Subscription
       ↓
Microsoft Foundry Resource
       ↓
Foundry Project
       ↓
Model Catalog
       ↓
Deploy Model
       ↓
Model Playground
       ↓
Test Model
       ↓
Visual Studio Code
       ↓
Build AI Application
```

---

# 12. Clean Up Azure Resources

When you finish experimenting with Microsoft Foundry, you should delete the resources created for the lab.

This is important because **Azure resources can incur charges while they are running**.

## Step 12.1 — Open Azure Portal

Open:

[Azure Portal](https://portal.azure.com?utm_source=chatgpt.com)

Sign in with your Azure account.

---

## Step 12.2 — Open the Resource Group

Navigate to the resource group used for this exercise:

```text
ResourceGroup1
```

Review the resources inside it.

Make sure you are deleting the resources associated with this lab and **not resources belonging to another project**.

---

## Step 12.3 — Delete the Resource Group

If the resource group is dedicated to this lab:

1. Open the resource group.
2. Select **Delete resource group**.
3. Enter the resource group name when prompted.
4. Confirm the deletion.
5. Wait for Azure to finish deleting the resources.

> **Important:** Deleting a resource group permanently deletes the resources inside it. Double-check the resource group before confirming.

---

# 13. End the Lab

After completing the cleanup:

**Be sure to end the lab from your lab/training environment.**

This ensures that the lab session is properly closed.

---

# Quick Checklist

Use this checklist to make sure you completed everything:

- [ ] Azure subscription available
- [ ] Python 3.13 installed
- [ ] Visual Studio Code installed
- [ ] Git installed
- [ ] Azure CLI installed
- [ ] Microsoft Foundry portal opened
- [ ] Foundry project created
- [ ] Region noted
- [ ] GPT-5.2 model found
- [ ] GPT-5.2 deployed
- [ ] Model tested in Playground
- [ ] Resource and project endpoints reviewed
- [ ] Foundry Toolkit installed in VS Code
- [ ] Azure account connected in VS Code
- [ ] Foundry project selected as default project
- [ ] GPT-5.2 viewed in VS Code
- [ ] Model Playground opened in VS Code
- [ ] Azure resources cleaned up
- [ ] Lab ended
