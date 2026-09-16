# Explore and Compare Models

The **Microsoft Foundry model catalog** is a central place where you can discover, compare, deploy, and test different AI models.

In this exercise, you will learn how to:

- Explore models in the Microsoft Foundry model catalog
- Read and understand model cards
- Compare models using benchmarks
- Use the model leaderboard
- Compare models side by side
- Deploy multiple models
- Test models in the Model Playground
- Generate a synthetic evaluation dataset
- Evaluate a model systematically
- Analyze evaluation results and failures

**Estimated time:** 45 minutes

> **Note:** Some technologies used in this exercise are in preview or active development. You may encounter unexpected behavior, warnings, or errors.

---

# 1. Prerequisites

Before starting this exercise, make sure you have:

- An active **Azure subscription**
- Permissions to create Azure AI resources
- A web browser
- Access to the Microsoft Foundry portal

---

# 2. Create a Microsoft Foundry Project

Microsoft Foundry uses **projects** to organize the models, resources, data, and other assets used to develop an AI solution.

## Step 2.1 — Open Microsoft Foundry

Open the Microsoft Foundry portal:

[Microsoft Foundry Portal](https://ai.azure.com?utm_source=chatgpt.com)

Sign in using the credentials provided by your lab or training environment.

> **Security:** Do not share your Azure username or password in screenshots, documentation, GitHub repositories, or chat messages.

After signing in:

1. Close any **Tips** or **Quick Start** panels that appear.
2. If the **New Foundry** option is not already enabled, enable it.

---

## Step 2.2 — Create the Project

If you are prompted to create a project, create one using the following settings:

| Setting              | Value                                   |
| -------------------- | --------------------------------------- |
| **Project name**     | `Project64772005`                       |
| **Foundry resource** | `Project64772005-resource`              |
| **Subscription**     | Your Azure subscription                 |
| **Resource group**   | `ResourceGroup1`                        |
| **Region**           | Select an AI Foundry recommended region |

To configure these settings:

1. Select **Create a new project**.
2. Enter the project name.
3. Expand **Advanced options**.
4. Enter the resource, subscription, resource group, and region information.
5. Select **Create**.
6. Wait for the project to finish creating.
7. Open the project home page.

> **Tip:** Make a note of the region you selected. You may need it later when working with your Azure resources.

---

# 3. Explore Models in the Model Catalog

Microsoft Foundry provides a **model catalog** containing many different AI models.

Some models are provided directly by Microsoft/Azure, while others are provided by partners or the open-source community.

Different models can have different:

- Capabilities
- Performance
- Costs
- Context window sizes
- Supported input/output formats
- Safety characteristics
- Supported features

The goal is to choose a model that fits your application's requirements.

---

## Step 3.1 — Open the Model Catalog

From your Foundry project:

1. Open **Discover**.
2. Select the **Models** tab.

You should now see the Microsoft Foundry model catalog.

---

## Step 3.2 — Search for GPT-5.2

Use the search box and search for:

```text
gpt-5.2
```

Select **GPT-5.2** from the search results.

This opens the model's **model card**.

---

# 4. Understand the Model Card

A **model card** provides information that helps you decide whether a model is appropriate for your application.

Review the information on the **Details** page.

Pay attention to things such as:

- Model capabilities
- Supported features
- Input and output formats
- Context length
- Performance information
- Safety information
- Available endpoints
- Deployment information

### Why is this important?

You should not choose an AI model only because it is popular or powerful.

For example:

> A model may provide excellent quality but have a higher cost, while another model may provide slightly lower quality but much better speed and cost efficiency.

The model card helps you make an informed decision.

---

# 5. Review Model Benchmarks

On the GPT-5.2 model page:

1. Open the **Benchmarks** page.
2. Review the available benchmark information.
3. Compare the performance of GPT-5.2 with other models used for similar scenarios.

### What are benchmarks?

**Benchmarks** are standardized tests used to measure how well a model performs on particular tasks.

For example, benchmarks can help measure:

- Reasoning
- Quality
- Safety
- Coding
- Mathematical ability
- General knowledge
- Throughput

> **Important:** Benchmark scores are useful for comparison, but they do not always predict how a model will perform in your specific application. You should also test the model using realistic examples from your own scenario.

After reviewing the benchmarks, select the **Back (←)** arrow next to the GPT-5.2 page title to return to the model catalog.

---

# 6. Compare Models Using the Model Leaderboard

The **Model Leaderboard** provides a visual way to compare models across multiple dimensions.

## Step 6.1 — Open the Leaderboard

From the model catalog:

1. Select **View leaderboard**.
2. Review the models displayed in the leaderboard.

The leaderboard allows you to compare models based on factors such as:

- Quality
- Safety
- Cost
- Performance

Look at which models score highly for the AI quality metrics.

---

# 7. Explore the Trade-Off Chart

The Trade-off Chart helps you understand the relationship between different model characteristics.

For example:

> A more capable model may provide better quality but could cost more or have lower throughput.

---

## Step 7.1 — Compare Quality and Cost

Scroll down to the **Trade-off chart** section.

From the metric dropdown:

1. Select **Benchmark Cost**.
2. Compare **GPT-5.2** and **GPT-5-mini**.

Look at how model quality relates to cost.

You can also add other models if you want to explore additional comparisons.

---

## Step 7.2 — Compare Quality and Throughput

From the metric dropdown, select:

**Throughput**

Compare GPT-5.2 and GPT-5-mini again.

### What is throughput?

**Throughput** describes how much work a model can process over a period of time.

Higher throughput can be important for applications that need to handle many requests.

---

## Step 7.3 — Compare Quality and Safety

From the metric dropdown, select:

**Safety**

Compare the models again.

This helps you understand how model quality relates to safety performance.

---

# 8. Compare Models Side by Side

The table above the trade-off charts allows you to select models and compare them directly.

## Step 8.1 — Select Models

In the model comparison table:

1. Select **GPT-5.2**.
2. Select **GPT-5-mini**.
3. Optionally select other models.
4. Select **Compare models**.

A side-by-side comparison should open.

---

## Step 8.2 — Review the Comparison

Review the following categories.

### 1. Performance Benchmarks

Compare:

- Quality
- Safety
- Throughput

---

### 2. Input and Output

Check which formats the models support for:

- Input prompts
- Model responses

This is important because your application may need a particular input or output format.

---

### 3. Context

Review:

- Context window size
- Maximum output tokens
- Model training information

### What is context?

The **context window** determines how much information the model can consider during a conversation or request.

For example:

```text
User question
      ↓
Previous conversation
      ↓
Documents
      ↓
Instructions
      ↓
Model context
      ↓
Response
```

A larger context window can be useful when working with long conversations or large documents.

---

### 4. Endpoints

Check:

- Which API endpoints are available
- How applications can consume the model
- Whether the model can be used with agents

---

### 5. Supported Features

Review the specific capabilities supported by each model.

These features should be compared against the requirements of your application.

---

# 9. Deploy the Models

Now that you have compared the models, you will deploy the two models used for testing:

- **GPT-5.2**
- **GPT-5-mini**

---

# 10. Deploy GPT-5.2

## Step 10.1 — Open GPT-5.2

From the model catalog:

1. Search for:

```text
gpt-5.2
```

2. Select the GPT-5.2 model.
3. On the model page, select **Deploy**.
4. Use the **default deployment settings**.
5. Wait for the deployment to finish.

After deployment, the model should open in the **Model Playground**.

---

## Step 10.2 — Record the Deployment Name

Take note of the **deployment name** assigned to GPT-5.2.

For example:

```text
GPT-5.2 deployment
        ↓
Deployment name
        ↓
____________________
```

> **Important:** The deployment name may not be exactly the same as the model name. You will need the deployment name later when comparing the models.

---

# 11. Deploy GPT-5-mini

Now deploy the second model.

From the Model Playground:

1. Open the **Model** dropdown.
2. Select **Browse more models**.
3. Search for:

```text
gpt-5-mini
```

4. Select **GPT-5-mini**.
5. Deploy the model.
6. Wait for the deployment to finish.

The newly deployed model should automatically become selected in the Model Playground.

---

## Step 11.1 — Record the Deployment Name

Make a note of the deployment name assigned to GPT-5-mini.

You should now have something similar to:

| Model      | Deployment                      |
| ---------- | ------------------------------- |
| GPT-5.2    | Your GPT-5.2 deployment name    |
| GPT-5-mini | Your GPT-5-mini deployment name |

---

# 12. Compare Models in the Model Playground

You now have two model deployments.

The Model Playground allows you to send the **same prompt to multiple models** and compare their responses.

This is useful because benchmark scores alone don't tell you everything about how a model behaves.

---

## Step 12.1 — Select the Models

In the Model Playground:

1. Make sure the **GPT-5-mini** deployment is selected in the Models list.
2. On the right side of the page, find **Compare models**.
3. Select the **GPT-5.2** deployment.

The side-by-side comparison view should open.

You should now have separate chat panes for the two models.

---

# 13. Test Both Models With the Same Prompt

Select the **Chat** tab for both models.

Enter the following prompt:

```text
I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
```

Submit the prompt.

You should see responses from both models.

---

## Step 13.1 — Ask for an Explanation

Now enter this follow-up prompt:

```text
Explain your reasoning.
```

Compare the responses again.

---

## Step 13.2 — Compare the Responses

When comparing the two models, look at:

### Accuracy

Did the model provide the correct solution?

### Reasoning quality

Did the model explain the solution clearly and logically?

### Response style

Compare:

- Clarity
- Detail
- Structure
- Conciseness
- Ease of understanding

You may find that two models produce different responses even when given exactly the same prompt.

### Key lesson

> **Model comparison is not only about benchmark scores.**

A model that performs well on benchmarks may not necessarily be the best model for your particular application.

Testing with realistic prompts is also important.

---

# 14. Evaluate a Model Using a Synthetic Dataset

Manual testing is useful when you have only a few questions.

However, real applications may need to handle hundreds or thousands of different inputs.

Instead of testing every question manually, you can use an **evaluation**.

In this exercise, you will evaluate GPT-5.2 using a synthetically generated dataset containing travel-related questions.

---

# 15. Step 1 — Select the Evaluation Target

In the Model Playground:

1. Select the **Evaluations** tab.
2. Select **Create**.
3. The **Create new evaluation** wizard will open.
4. For the evaluation target, select:

**Model**

5. In the model list, deselect any models that were automatically selected.
6. Select only the **GPT-5.2** deployment.
7. Select **Next**.

### What is an evaluation?

An evaluation is a systematic way of measuring how well an AI model performs across a collection of test cases.

Instead of asking:

> "Does this one answer look good?"

you can ask:

> "How does this model perform across many different questions?"

---

# 16. Step 2 — Generate the Test Dataset

Instead of uploading your own dataset, you will use Foundry's **Synthetic Generation** feature.

## Step 16.1 — Select Synthetic Generation

In the **Data** step:

1. Find **Dataset source**.
2. Select:

**Synthetic generation**

Synthetic generation allows Foundry to automatically create test questions for the evaluation.

---

## Step 16.2 — Generate the Dataset

Select **Generate**.

Use the following settings:

| Setting            | Value                                                                                         |
| ------------------ | --------------------------------------------------------------------------------------------- |
| **Dataset name**   | Leave as default                                                                              |
| **Model**          | `gpt-5.2`                                                                                     |
| **Number of rows** | `45`                                                                                          |
| **Prompt**         | `Create various travel related questions, and include some content safety and security tests` |
| **Seed data**      | Leave blank                                                                                   |

The dataset will contain **45 automatically generated questions**.

Select **Next**.

---

# 17. Step 3 — Configure the Model

In the **Configure models** step, find the **Developer prompt** field.

Enter:

```text
You are a helpful travel assistant that provides accurate, detailed, and practical travel advice to help users plan their trips.
```

Leave the remaining settings at their default values.

Select **Next**.

---

# 18. Step 4 — Configure Evaluation Criteria

The **Criteria** step determines how the model's responses will be evaluated.

Foundry provides suggested evaluators that use an AI model as a **judge**.

These evaluators examine the generated responses and assign scores based on different criteria.

---

## Step 18.1 — Adjust the Criteria

Review all the suggested evaluators.

Under **Agents and Safety**:

- Remove all criteria.

Leave the remaining evaluators enabled.

Select **Next**.

---

# 19. Step 5 — Review and Submit the Evaluation

The **Review** step shows the configuration of your evaluation.

Verify:

- Target model
- Dataset
- Developer prompt
- Evaluation criteria
- Other configuration settings

Give the evaluation a name, such as:

```text
travel-assistant-eval
```

Review everything carefully.

When everything looks correct:

**Select Submit**

This starts the evaluation run.

---

# 20. Wait for the Evaluation to Complete

The evaluation may take several minutes.

The exact time can depend on:

- Number of test cases
- Model availability
- Azure/data-center load
- Evaluation criteria
- Current service conditions

Wait until the evaluation run finishes.

---

# 21. Review the Evaluation Results

When the evaluation completes:

1. Select the evaluation run.
2. Open the results page.

You should see an overview of the evaluation metrics.

---

## Step 21.1 — Review the Scores

Review the results table.

Look at the scores produced by each evaluator.

Scroll horizontally to view additional columns and pages.

You will likely see mostly passing results, although some failures may occur.

> **Do not automatically assume that a failure means the model is bad.** Read the failed example and determine whether the model's response was actually inappropriate for your application.

---

# 22. Analyze Evaluation Failures

If there are failures, examine them carefully.

Select:

**Analyze results**

Then:

1. Select **GPT-5.2** from the dropdown.
2. Select **Start analysis**.

Foundry will analyze the failures and group them according to possible reasons.

---

## What Should You Look For?

Examine:

- Why the response failed
- What type of question caused the failure
- Whether the evaluator's judgment makes sense
- Whether the model's response was actually problematic
- What configuration changes might improve the results

Some failures may occur because the model refuses to answer certain questions due to safety or security considerations.

For example:

```text
User question
      ↓
Model evaluates request
      ↓
Safety concern detected
      ↓
Model refuses or limits response
      ↓
Evaluator marks response
```

A failure in this situation does not necessarily mean the model behaved incorrectly.

---

# 23. Review AI Suggestions

The analysis page may provide suggestions for improving your configuration.

Review these suggestions and consider whether they would improve your application.

Possible areas to consider include:

- Developer instructions
- Prompt design
- Model selection
- Evaluation criteria
- Safety configuration
- Dataset quality

The purpose of evaluation is not simply to obtain a high score.

The real goal is to understand:

> **How well does the model perform for my specific application, and what can I improve?**

---

# 24. Key Concepts Learned

By completing this exercise, you have learned several important concepts.

### Model Catalog

A central place to discover and explore AI models.

### Model Card

Provides information about a model's capabilities, limitations, and supported features.

### Benchmark

A standardized test used to compare model performance.

### Model Leaderboard

Provides a visual comparison of models across different metrics.

### Trade-off Analysis

Helps you understand relationships between:

- Quality
- Cost
- Throughput
- Safety

### Model Playground

Allows you to manually test models with your own prompts.

### Model Comparison

Allows you to send the same prompt to different models and compare their responses.

### Synthetic Dataset

A dataset automatically generated by an AI model for testing or evaluation.

### Evaluation

A systematic process for measuring model performance across many test cases.

### AI Judge / Evaluator

An AI model that evaluates another model's responses against defined criteria.

---

# 25. Overall Workflow

The complete process you followed can be summarized as:

```text
Microsoft Foundry
       │
       ▼
Create Project
       │
       ▼
Explore Model Catalog
       │
       ▼
Read Model Cards
       │
       ▼
Review Benchmarks
       │
       ▼
Compare Models
       │
       ▼
Deploy Models
       │
       ├──────────────┐
       ▼              ▼
    GPT-5.2       GPT-5-mini
       │              │
       └──────┬───────┘
              ▼
       Model Playground
              │
              ▼
       Compare Responses
              │
              ▼
        Create Evaluation
              │
              ▼
      Generate Test Dataset
              │
              ▼
        Run Evaluation
              │
              ▼
       Review Results
              │
              ▼
        Analyze Failures
              │
              ▼
      Improve Your Solution
```

---

# 26. Quick Checklist

Use this checklist to confirm that you completed the exercise:

## Project Setup

- [ ] Opened Microsoft Foundry
- [ ] Created the Foundry project
- [ ] Selected an appropriate region

## Model Exploration

- [ ] Opened the Model Catalog
- [ ] Searched for GPT-5.2
- [ ] Reviewed the GPT-5.2 model card
- [ ] Reviewed model benchmarks
- [ ] Opened the Model Leaderboard
- [ ] Explored the Trade-off Chart
- [ ] Compared GPT-5.2 and GPT-5-mini

## Model Deployment

- [ ] Deployed GPT-5.2
- [ ] Recorded its deployment name
- [ ] Deployed GPT-5-mini
- [ ] Recorded its deployment name

## Model Testing

- [ ] Opened Model Playground
- [ ] Compared GPT-5.2 and GPT-5-mini
- [ ] Tested both models with the fox/chicken/grain problem
- [ ] Asked both models to explain their reasoning
- [ ] Compared accuracy and response quality

## Evaluation

- [ ] Created a model evaluation
- [ ] Selected GPT-5.2
- [ ] Generated a synthetic dataset
- [ ] Created 45 test questions
- [ ] Added the travel assistant developer prompt
- [ ] Configured evaluation criteria
- [ ] Submitted the evaluation
- [ ] Reviewed the evaluation results
- [ ] Analyzed failures
- [ ] Reviewed AI improvement suggestions

---

# 27. Final Takeaway

The most important lesson from this exercise is:

> **Choosing an AI model is a balance between quality, cost, speed, safety, features, and your application's specific requirements.**

A model with the highest benchmark score is not automatically the best choice.

A good AI developer should:

```text
Explore
   ↓
Compare
   ↓
Deploy
   ↓
Test
   ↓
Evaluate
   ↓
Analyze
   ↓
Improve
```

This process helps you choose the right model based on **real application requirements**, rather than simply choosing a model based on its name or benchmark score.
