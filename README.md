### 📂 LinkedIn Job Alert Automation Project (`README.md`)

```markdown
# 🤖 AI-Powered LinkedIn Job Alert & Scraping Pipeline

> 🚀 **An autonomous job intelligence agent built on n8n** that programmatically scrapes LinkedIn job listings via **Apify**, logs structured leads into **Google Sheets**, and leverages an **AI Agent powered by Groq (Llama-3.3-70b)** to evaluate, summarize, and deliver targeted job notifications.

---

## 🎯 Project Overview & Business Problem

Manually browsing job portals, matching criteria, and manually keeping track of application links is repetitive and time-consuming. Simple keyword scrapers also fail because they lack the intelligence to understand context or job requirements properly.

This project solves these issues by building an end-to-end **AI Job Hunter Pipeline**. It accepts search input via a **Webhook**, dispatches background scraping tasks via **Apify**, manages non-blocking status polling loops, cleans extracted data payloads with **JavaScript**, archives structured records into **Google Sheets**, and feeds job details into a **70-Billion parameter Large Language Model (Groq / Llama 3.3)** to analyze and generate detailed summaries for top matched opportunities.

---

## 📊 Business Impact & Feature Highlights

| 📈 Metric / Feature | 🛠️ Implementation Strategy | 💼 Business Value Delivered |
| :--- | :--- | :--- |
| **Autonomous Scraping** | Apify LinkedIn Actor Integration | Real-time extraction without manual browsing |
| **Resilient Loop Architecture** | Polling Loop with Wait State Nodes | Ensures reliability during long-running tasks |
| **AI Evaluation Engine** | Groq Llama-3.3-70B via LangChain Agent | Deep contextual analysis beyond keyword matching |
| **Centralized Database** | Google Sheets Integration | Auto-built application repository |

---

## ⚙️ Workflow Architecture & Technical Flow

```text
              ┌─────────────────────────────────────┐
              │ Webhook Trigger: Receive Job Search │
              └──────────────────┬──────────────────┘
                                 │
                                 ▼
              ┌─────────────────────────────────────┐
              │ Set Target Parameters (Title, City) │
              └──────────────────┬──────────────────┘
                                 │
                                 ▼
              ┌─────────────────────────────────────┐
              │ HTTP: Trigger Apify Scraper Actor   │
              └──────────────────┬──────────────────┘
                                 │
                                 ▼
    ┌────────────────────────────────────────────────────────┐
    │ HTTP: Check Scraper Run Status (Apify Run ID)           │
    └────────────────────────────┬───────────────────────────┘
                                 │
               ┌─────────────────┴─────────────────┐
               │                                   │
               ▼ (If Still Running)                ▼ (If Status: SUCCEEDED)
     ┌───────────────────┐               ┌──────────────────────────┐
     │ Wait Node (20s)   │               │ Get Apify Dataset Items  │
     └─────────┬─────────┘               └────────────┬─────────────┘
               │                                      │
               └──────────────────────────────────────┼───────────────────┐
                                                      │                   │
                                                      ▼                   │
                                         ┌──────────────────────────┐     │
                                         │ JavaScript Code Node     │     │
                                         │ (Normalize JSON Payload) │     │
                                         └────────────┬─────────────┘     │
                                                      │                   │
                                                      ▼                   │
                                         ┌──────────────────────────┐     │
                                         │ Google Sheets Storage    │     │
                                         └────────────┬─────────────┘     │
                                                      │                   │
                                                      ▼                   │
                                         ┌──────────────────────────┐     │
                                         │ LangChain AI Agent       │     │
                                         │ (Groq Llama-3.3-70B)     │     │
                                         └────────────┬─────────────┘     │
                                                      │                   │
                                                      ▼                   │
                                         ┌──────────────────────────┐     │
                                         │ Webhook Response Output  │◄────┘
                                         └──────────────────────────┘


🔍 Detailed Execution Steps & Processing Logic
1. 📥 Ingestion & Trigger Phase
Webhook Endpoint (Receive Job Search1): Listens for search queries containing target criteria (Job Title, Location, Output Count Limit).

Parameter Assignment (Set Search Parameters): Sets dynamic variable defaults (e.g., Job Title: "Developer", Location: "Dhaka", Fetch Count: 20).

2. 🕵️ Scraping & Asynchronous Polling Loop
Actor Execution (HTTP - Start Apify LinkedIn Jobs Scraper): Launches an external Apify cloud actor (crawlworks~linkedin-jobs-scraper) with specified parameters (Remote only, Full-Time, targeted search strings).

Status Polling Loop (HTTP - Check Apify Run Status, IF Node, Wait Node):

Checks if the scraper job status is "SUCCEEDED".

If the task is still processing, it routes execution to a Wait Node (20 seconds) before re-checking the status loop.

Prevents API timeout errors while handling long scraping executions gracefully.

3. 🧹 Data Extraction & Normalization
Dataset Retrieval (HTTP - Get Apify Dataset Items): Downloads output items directly from Apify's default dataset storage once scraping completes.

Payload Normalization (Code in JavaScript): Uses defensive JavaScript mapping (job.title || job.jobTitle || job.positionName) to unify varying field structures into clean standard keys (jobTitle, companyName, location, postedTime, jobUrl, companyUrl).

4. 🧠 AI Processing & Centralized Storage
Data Storage (Append row in sheet): Appends cleaned job listings directly into a structured Google Sheets spreadsheet.

LLM Filtering (AI Agent + Groq Chat Model):

Connects an n8n LangChain AI Agent to the ultra-fast Groq Cloud API running llama-3.3-70b-versatile.

Evaluates extracted details, formats clean readable output summaries, and delivers curated alerts back to the caller.

🛠️ Tech Stack & Key Integrations
⚙️ Workflow Orchestrator: n8n (Workflow Engine & LangChain AI Nodes)

🕵️ Web Scraping Infrastructure: Apify Actor API (LinkedIn Jobs Scraper)

🧠 AI Infrastructure: Groq Cloud (llama-3.3-70b-versatile) via n8n-nodes-langchain

📊 Database / Sheet Ingestion: Google Sheets API

💻 Custom Logic: JavaScript (ES6+)



💡 Consultant Mindset & Technical Edge
🧠 Why Choose This Architectural Approach?

🔹 Asynchronous Loop Handling: Scraping processes take time. Rather than freezing execution, using a status loop (Check Status ➔ Wait ➔ Check Status) ensures high availability and enterprise stability.

🔹 High-Speed Inference with Groq: By leveraging Groq LPU acceleration for Llama-3.3-70B, job analysis runs in milliseconds, making dynamic responses possible within webhook workflows.

🔹 Modular Data Pipeline: Scraped data is saved to Google Sheets before AI analysis—ensuring no scraped data is lost even if downstream AI formatting encounters an issue.