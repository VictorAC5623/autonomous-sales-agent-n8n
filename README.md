# Autonomous Multi-Agent Sales Ecosystem 🤖

**A production-grade autonomous sales orchestrator deployed in the education sector.**

This repository contains the source code for a modular AI system designed to automate end-to-end sales processes, moving beyond simple Q&A bots to a full "Sales Engineer" agent capable of context retention, objection handling, and autonomous closing.

## 🏗 System Architecture

The ecosystem is built on a **Micro-Workflow Architecture** using **n8n** as the orchestrator. It integrates the following stack:

* **Cognitive Engine:** Gemini 1.5 Pro (via Google Vertex AI/Studio).
* **State & Vector Store:** Google Firestore (NoSQL).
* **Human Interface:** Chatwoot (Open Source CRM).
* **Channel:** WhatsApp Business API (Meta).

## 🧩 Modules Overview

The system is composed of 4 synchronized workflows acting as independent services:

### 1. Core Agent (`sales_agent_workflow.json`)
The central brain of the operation.
* **Function:** Handles incoming messages, retrieves context (RAG) from Firestore, and uses Chain-of-Thought (CoT) prompting to decide the next best action.
* **Key Logic:**
    * **Intent Classification:** Distinguishes between "Hot Leads" (Purchase Intent > 85%) and general inquiries.
    * **Dynamic RAG:** Pulls specific product data (Price, Curricula) based on user interest.
    * **Personality Engine:** Switches between personas ("Santiago" vs "Sofía") based on user demographics.

### 2. Human Bridge Middleware (`chatwoot_bridge.json`)
A bi-directional synchronization layer between WhatsApp and Chatwoot.
* **Function:** Ensures smooth "Human Handoff".
* **Logic:** Filters bot-generated messages (marked with 🤖) to prevent loops and manages conversation status (Open/Pending) based on agent availability.

### 3. The Hunter - Retention Loop (`retention_loop_hunter.json`)
An asynchronous state machine for lead recovery.
* **Function:** Monitors the `leads` collection in Firestore.
* **Logic:** Runs a CRON job daily at 10:00 AM. Identifies leads stuck in "Pending" state for 48-90 hours and triggers a hyper-personalized re-engagement message to revive the sale.

### 4. Campaign Batch Launcher (`campaign_batch_launcher.json`)
A batch-processing workflow for mass outreach.
* **Function:** Reads leads from Google Sheets and executes messaging campaigns.
* **Engineering:** Implements `SplitInBatches` logic (chunks of 500) to respect Meta API rate limits and logs delivery status/errors back to the database in real-time.

## 🚀 Key Technical Features

* **State Management:** Unlike stateless LLM calls, this system maintains long-term memory of the user's journey in Firestore.
* **Offline-First Data Sync:** Integrated with a custom Android Data Collection App (source in separate repo) to handle leads from physical trade fairs.
* **Guardrails:** Includes strict prompt engineering to prevent hallucination and enforce business rules (e.g., "Never invent prices").

## 🛠 Deployment

1.  **Prerequisites:** Self-hosted n8n instance, Google Cloud Service Account (Firestore), and WhatsApp Business API credentials.
2.  **Installation:** Import the `.json` files into n8n.
3.  **Configuration:** Replace the placeholder credentials (`YOUR_API_KEY`, `YOUR_SHEET_ID`) with your production keys.

---
*Authored by **Victor Altamirano** - Physicist & AI Engineer*
