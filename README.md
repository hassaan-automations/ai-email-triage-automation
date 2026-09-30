# AI-Powered Email Triage & CRM Automation Pipeline

An enterprise-ready, event-driven automation built on **n8n** that intercepts incoming emails via Gmail, classifies intent and urgency using **Google Gemini LLM**, parses non-deterministic text into deterministic JSON, routes data logically, logs prospects to **Airtable CRM**, and drafts context-aware responses.

---

## Architecture Overview
Gmail Trigger (Poll/Webhook)
│
▼
Google Gemini LLM (Zero-Shot JSON Classifier)
│
▼
JavaScript Sanitizer (Custom Node: Boundary Matching & Deserialization)
│
▼
Branching Router (If/Switch)
├── [Sales]   ──► Airtable CRM Sync ──► Gmail Draft/Send Reply
├── [Support] ──► Auto-Acknowledgment Email Dispatch
└── [Spam]    ──► Message Labeling & Inbox Archival
---

## Features

- **Zero-Cost Cognitive Classification:** Leverages Google Gemini Flash model via structured prompts for low-latency triage.
- **Resilient JSON Deserialization:** Custom JavaScript engine handles newline escapes, markdown wrappers, and array payloads returned by LLMs without breaking.
- **Two-Tier Logic Routing:** Segregates business-critical inbound leads from regular customer queries and malicious/unsolicited spam.
- **Automated CRM Sync:** Dynamically syncs sales inquiries, priority flags, summaries, and proposed replies into Airtable.
- **Contextual Draft Generation:** Drafts automated responses referencing the original thread context to reduce response turnaround time.

---

## Tech Stack

- **Orchestration:** n8n (Self-hosted / Cloud)
- **AI Engine:** Google Gemini (`gemini-flash`)
- **Data Store:** Airtable (REST API / Personal Access Token)
- **Messaging:** Gmail API (OAuth 2.0)
- **Language:** JavaScript (Node.js runtime within n8n)

---

## Setup & Deployment Instructions

### 1. Import Workflow
1. Download `workflow.json` from this repository.
2. In n8n, navigate to **Workflows** > **Add Workflow** > **Import from File**.

### 2. Configure Credentials
- **Gmail OAuth2:** Connect an authorized Google account with `gmail.modify` or `gmail.compose` permissions.
- **Google Gemini API:** Provide your Google AI Studio API key.
- **Airtable API:** Connect using a Personal Access Token (`data.records:write`, `schema.bases:read`).

### 3. Airtable Base Schema Setup
Ensure your target Airtable table includes the following column structure:

| Field Name | Type | Notes |
| :--- | :--- | :--- |
| `category` | Single Line Text / Single Select | `Sales`, `Support`, `Spam` |
| `priority` | Single Line Text / Single Select | `High`, `Medium`, `Low` |
| `Summary` | Long Text | High-level email summary |
| `draft reply`| Long Text | Contextual response draft |

---

## Edge Case Handling

- **LLM Hallucinations & Formatting Drift:** Handled using substring matching on brace delimiters (`{ ... }`) combined with string sanitizers to ensure schema integrity before entering database stages.
- **Airtable Typecasting:** Enabled typecast overrides to prevent pipeline terminations when encountering dynamic tags.

---

## Author
Built by Hassaan Aziz.
