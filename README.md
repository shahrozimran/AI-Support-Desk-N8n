# AI Support Desk — AI-Powered Support Automation

### Intelligent Ticket Classification, Priority Routing & Response Drafting with n8n and OpenRouter

An AI-powered customer support automation workflow built using **n8n**, **OpenRouter**, and **JavaScript**. The system processes customer support requests, classifies issues, identifies urgency, generates draft replies, and routes tickets for human review. It also generates a formatted HTML support report.

> **Project Type:** Workflow Automation | Artificial Intelligence | Customer Support
>
> **Status:** Demonstration Project
>
> **Execution:** Locally hosted n8n

---

## Workflow Architecture

The workflow combines manual demo execution and an interactive support intake form with AI classification, validation, priority-based routing, and report generation.

![AI Support Desk n8n Workflow](N8n%20Workflow.PNG)

---

## Video Demonstration

Watch the recorded demonstration of the AI Support Desk workflow.

[![Watch AI Support Desk Demonstration](https://img.youtube.com/vi/zbi29ILjZYU/hqdefault.jpg)](https://youtu.be/zbi29ILjZYU)

**[▶ Watch the Full Demonstration on YouTube](https://youtu.be/zbi29ILjZYU)**

---

## Project Overview

AI Support Desk is designed to assist support teams with repetitive ticket-triage activities. It uses a language model through OpenRouter to analyze submitted requests, generate structured classifications, estimate priority, and prepare suggested responses.

All generated responses remain **drafts requiring human review**. The system does not automatically email customers, issue refunds, modify accounts, or resolve reported incidents.

## Key Features

- **Support Intake Form:** Collects the subject and message of a support request.
- **Manual Demo Trigger:** Runs two predefined example support tickets.
- **Input Normalization:** Validates and standardizes request data.
- **AI Classification:** Classifies tickets as technical, billing, account, or general.
- **Priority Detection:** Assigns high, medium, or low priority based on the reported impact.
- **Reply Generation:** Creates a concise draft response for a support agent to review.
- **Confidence Score:** Returns an AI-estimated classification confidence value.
- **Validation Rules:** Checks the model's structured output and applies additional safety rules.
- **Human Escalation:** Routes high-priority, low-confidence, or invalid results for urgent human review.
- **Standard Review Queue:** Routes other tickets to a category-specific review queue.
- **Visual Report Generation:** Builds a formatted HTML report containing the classification, priority, summary, original request, and response draft.
- **PDF Export:** The generated HTML report can be opened in a browser and printed or saved as a PDF.

## Workflow Execution Process

```text
Manual Demo Trigger       Support Intake Form
         |                        |
         v                        v
  Example Requests                |
         |                        |
         +------------+-----------+
                      |
                      v
              Normalize Request
                      |
                      v
           OpenRouter LLM Request
                      |
                      v
          Validate & Apply Rules
                      |
                      v
              Escalation Check
                 /          \
                /            \
         Urgent Review    Standard Review
                \            /
                 \          /
                  v        v
              Create Visual Report
                      |
                      v
              Form Submission?
                      |
             Show Report in Form
              (form path only)
```

### Processing Logic

1. The user starts the workflow manually or submits the support intake form.
2. The request is validated and transformed into a structured model request.
3. OpenRouter processes the ticket and returns a JSON response containing its category, priority, summary, draft reply, and confidence.
4. The workflow checks the response against validation rules. Reported production outages are forced to high priority.
5. High-priority tickets, confidence values below `0.75`, and invalid AI outputs are marked for urgent human review.
6. Remaining requests are directed to the standard review queue.
7. The workflow generates a visual HTML support report.
8. When the form-specific execution path is used, the workflow includes a form completion node for displaying the report.

## Technologies Used

| Technology | Purpose |
|---|---|
| n8n | Visual workflow orchestration and automation |
| OpenRouter API | Access to language models for ticket analysis |
| JavaScript | Input preprocessing, validation, and report generation |
| HTTP Request | Communication with the OpenRouter API |
| JSON | Structured workflow configuration and AI response format |
| HTML/CSS | Support report formatting and presentation |

## AI Classification Rules

| Category | Description |
|---|---|
| Technical | Software errors, system failures, or technical issues |
| Billing | Payment, invoice, or billing-related requests |
| Account | Account access or account-related support |
| General | General inquiries and how-to questions |

### Priority Levels

| Priority | Example |
|---|---|
| High | Production checkout outage or widespread payment failures |
| Medium | An individual user is blocked from completing a task |
| Low | Routine guidance or general questions with no reported impact |

### Human Review Policy

A ticket requires urgent human review when:

- Its priority is high.
- Its AI-estimated confidence is below `0.75`.
- The AI response is invalid or the model request fails.

The confidence score is a model-generated estimate and is not a statistically calibrated probability.

## How to Install and Run

### Prerequisites

- A working n8n instance
- Internet access for OpenRouter API requests
- An OpenRouter account and API key

### Step 1 — Download the Workflow

Download the workflow JSON file from this repository.

### Step 2 — Import into n8n

1. Open your n8n instance.
2. Create a new workflow.
3. Use the workflow menu to select **Import from File**.
4. Select the downloaded JSON file.
5. Review the imported nodes and connections.

### Step 3 — Configure OpenRouter Authentication

Open the **OpenRouter LLM — Classify and Draft** HTTP Request node.

Configure an **HTTP Header Auth** credential:

```text
Header Name: Authorization
Header Value: Bearer YOUR_OPENROUTER_API_KEY
```

The API endpoint is:

```text
https://openrouter.ai/api/v1/chat/completions
```

**Important:** Store the API key in n8n Credentials. Never hardcode it into the workflow JSON or commit it to GitHub.

### Step 4 — Execute the Demo

1. Open the **Run Demo** trigger.
2. Execute the workflow.
3. Inspect the AI classification and validation results.
4. Check the urgent and standard review branches.
5. Open **Create Visual Report** and inspect its binary report output.
6. Download the HTML report and open it in a browser.
7. Use **Ctrl + P → Save as PDF** if a PDF copy is required.

### Step 5 — Test the Support Form

1. Open the **Support Intake Form** node.
2. Start a test session or publish the workflow when appropriate.
3. Submit a request with a subject and message.
4. Inspect the classification, routing, and generated report.

## Example Test Scenarios

**Scenario 1 — High Priority**

Subject: Production checkout is down

A production checkout failure affects every customer and prevents orders from being completed. The workflow should classify the issue as high priority and require urgent review.

**Scenario 2 — Low Priority**

Subject: How do I change the dashboard theme?

A user requests instructions for switching between light and dark themes. With no reported business impact, this is expected to be a low-priority general inquiry, subject to the AI output and validation rules.

## Generated Report

The HTML report includes:

- Ticket identifier and original subject
- Priority and category
- AI-estimated classification confidence
- Issue summary
- Original customer request
- Suggested response draft
- Human review instructions and routing destination
- Suggested internal response target
- Model information and report timestamp

## Security and Privacy

- API credentials are not included in the published workflow.
- Authentication is configured separately using n8n's credential manager.
- Support ticket content is sent to OpenRouter and potentially its selected model provider.
- Users should avoid entering passwords, payment card details, or other sensitive information.
- Generated customer replies remain drafts until reviewed and sent by an authorized person.
- The project is intended for demonstration and educational purposes; production deployment would require additional security, privacy, and reliability review.

## Limitations

- GitHub hosts the project files but does not execute the n8n workflow.
- A running n8n instance and valid OpenRouter credentials are required for execution.
- The human review queues represent workflow routing states, not a persistent ticket-management database.
- No automatic email or customer message is sent.
- Model availability and response quality depend on the OpenRouter service and selected model.

## Demonstration and Academic Review

The repository provides the exported workflow, its visual architecture, and a recorded demonstration. This allows the project to be inspected without direct access to the original local computer.

**Video:** https://youtu.be/zbi29ILjZYU

---

**AI Support Desk — Built with n8n and OpenRouter**
