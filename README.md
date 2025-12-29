# Autonomous Multi-Agent Sales Orchestrator 🤖

A production-grade autonomous agent designed to automate the end-to-end sales process for an educational institution (*Preuniversitario Newman*). 

Unlike standard chatbots, this system acts as a **Sales Engineer**: it manages conversation state, retrieves context from a vector database, handles objections using Chain-of-Thought (CoT) reasoning, and autonomously decides when to close a sale or escalate to a human.

## 🏗 Architecture

The system is orchestrated via **n8n** and integrates the following stack:

* **Cognitive Engine:** Gemini 1.5 Pro (via API).
* **State & Memory:** Google Firestore (NoSQL).
* **Channel Interface:** WhatsApp Business API (via Chatwoot).
* **Orchestrator:** Self-hosted n8n workflow.

## 🧠 Workflow Logic

The `sales_agent_workflow.json` contains the complete decision tree. Key logic blocks include:

1.  **Event Ingestion:** Webhook captures incoming messages from WhatsApp/Chatwoot.
2.  **Context Retrieval (RAG):**
    * Queries **Firestore** to retrieve the student's lead score and conversation history.
    * Determines if the user is a returning lead ("Bumeran" logic).
3.  **Cognitive Processing:**
    * The LLM analyzes the intent.
    * **Decision Node:** Determines if the lead is "Hot" (*Venta Caliente*).
4.  **Action Execution:**
    * **Sales Closing:** Sends payment links if the user is ready.
    * **Objection Handling:** Uses a knowledge base to answer doubts about the curriculum.
    * **Human Handoff:** If the sentiment is negative or complex, it tags the conversation as "Pending" in Chatwoot for human intervention.

## 🚀 Deployment

1.  Install [n8n](https://n8n.io/).
2.  Import the `sales_agent_workflow.json` file.
3.  Configure your credentials for:
    * Google Firestore (Service Account).
    * Gemini API Key.
    * Chatwoot API Token.

## 📂 File Structure

* `sales_agent_workflow.json`: The core logic export from n8n.
