# 🧠 Health Intake - AI Agent Workflow (n8n)

This workflow is designed to collect, analyze, and respond to health-related data submitted via a webhook (e.g., through Siri or another voice assistant) using OpenAI's GPT model and store it in Airtable.

---

## 📌 Workflow Overview

This n8n workflow automates the process of capturing health data (intake, calories, exercise, mood, weight) using natural language input. It performs the following steps:

1. **Receive Input**: Accepts POST requests through a webhook.
2. **Interpret Input**: Sends the query to a custom AI Agent powered by OpenAI (gpt-4o-mini).
3. **Store Data**: Saves structured health data into Airtable if applicable.
4. **Read Data**: Fetches past records for summarization (daily, weekly, monthly).
5. **Respond**: Returns AI-generated summary or acknowledgment back to the sender.

---

## 🚀 Use Case Scenarios

- Log food intake and estimate calories.
- Record exercise and calories burned.
- Track mood or weight updates.
- Request summaries like "Summarize my day/week/month".

---

## 🧩 Node Structure

| Node Name            | Type                | Description |
|----------------------|---------------------|-------------|
| Webhook              | Trigger             | Receives POST requests at `/health`. |
| AI Agent             | LangChain Agent     | Custom agent with embedded instructions and example prompts. |
| OpenAI Chat Model    | OpenAI Chat Model   | Processes natural language with GPT-4o-mini. |
| save_healthInfo      | Airtable (Create)   | Records health data (intake, calories, exercise, etc.) into Airtable. |
| read_healthInfo      | Airtable (Search)   | Fetches existing data to generate summary responses. |
| Respond to Webhook   | Respond             | Sends AI-generated reply to the requester. |

---

## 🛠️ How It Works

1. User sends a message like:  
   - `"I ate a salad"`  
   - `"I walked 2 miles"`  
   - `"Summarize my day"`  

2. The message is passed via the **Webhook** node.

3. The **AI Agent** processes the prompt with contextual rules and sample responses.

4. The **OpenAI Chat Model** executes the LLM instructions to interpret or summarize.

5. Depending on the type of request:
   - Data is **saved** to Airtable (`save_healthInfo`).
   - Past data is **read** from Airtable (`read_healthInfo`) and summarized.

6. The **Respond to Webhook** node sends back a concise reply.

---

## 💡 Examples

| User Input                       | AI Response                                   |
|----------------------------------|-----------------------------------------------|
| "I ate a Big Mac"                | Recorded eating a Big Mac at 500 calories.    |
| "I walked 2 miles today"         | Recorded you walked 2 miles and burned 500 cal.|
| "Summarize my day"              | Your calorie intake is X, burned Y, etc.      |
| "I’m feeling very tired"         | Mood recorded as very tired.                  |

---

## 🔐 Credentials Required

- **OpenAI API Key**: Used by the OpenAI Chat Model node.
- **Airtable API Token**: Used to access and manipulate data in Airtable.

Ensure both credentials are properly set up in the n8n Credentials Manager:
- OpenAI → `openAiApi`
- Airtable → `airtableTokenApi`

---

## 📅 Airtable Structure

Airtable table must include the following fields:
- `Intake` (string)
- `Calories` (string)
- `Exercise` (string)
- `CaloriesBurned` (string)
- `Weight` (string)
- `Mood` (string)
- `Date` (auto-generated)

---

## 📲 Webhook Endpoint

- **Path**: `/health`
- **Method**: `POST`
- **Payload**: Plain text in query string or header (e.g., `"I drank coffee"`)

---

## 🧠 Custom AI Agent Instructions

The agent has been configured with specific domain rules and examples to:
- Infer calories or mood from statements.
- Avoid storing empty entries.
- Summarize daily, weekly, or monthly stats with insights.

---

## ✅ Example Curl Command

```bash
curl -X POST https://your-n8n-domain/webhook/health \
-H "Content-Type: application/json" \
-d '{"query":"I ate two eggs and toast"}'
```

---

## 📎 Notes

- No data will be stored if all fields are empty.
- The agent logic is maintained using `@n8n/n8n-nodes-langchain.agent`.
- Requires n8n v1.7+ with LangChain support enabled.

---

## 📘 Credits

Developed using **n8n**, **OpenAI**, and **Airtable** by Nskha.

---
