# 🧠 Health Intake — AI-Powered Health Logging with Siri & Airtable

This n8n workflow lets you track your nutrition, exercise, calories, weight, and mood via **Siri voice input**, processed by **OpenAI GPT-4o**, and stored in **Airtable**. It's designed for quick, natural health tracking with minimal user effort.

## 🚀 Features

- 🔊 Accepts voice-based health logs through Siri (via webhook). Set up a Shortcut on your iPhone
- 🤖 Uses OpenAI (GPT-4o) to extract structured health data from free-form speech.
- 📊 Stores structured entries (e.g., intake, calories, exercise, mood) in Airtable.
- 🧠 Supports natural language queries about your past health records.
- 📅 Logs are automatically timestamped and categorized.

## 📌 Prerequisites

- A running n8n instance (self-hosted or cloud).
- Airtable account with:
  - A base named `HealthInfo`
  - A table (e.g., `HealthInfo_new`) with fields: `Date`, `Intake`, `Calories`, `Exercise`, `CaloriesBurned`, `Weight`, `Mood`
- OpenAI API key with GPT-4o support.
- Siri Shortcut to send POST requests to the n8n webhook.

## 🧩 Nodes Overview

| Node Name          | Function                                                                 |
|--------------------|--------------------------------------------------------------------------|
| `Siri Request`     | Webhook to receive Siri POST requests                                    |
| `Health Agent`     | LangChain agent using GPT-4o with system prompt for structured extraction|
| `OpenAI Chat Model`| GPT-4o language model connected to agent                                 |
| `read_healthInfo`  | Reads existing Airtable records                                          |
| `save_healthInfo`  | Saves structured health data to Airtable                                |
| `Respond to Siri`  | Sends back a voice-friendly text response                                |
| `Sticky Note`      | Notes the workflow purpose                                               |

## 🔁 Workflow Logic

1. User activates a **Siri shortcut**, sending a voice input to `POST /webhook/health`.
2. `Health Agent` receives the input and uses the GPT-4o model to:
   - Classify the entry type (e.g., nutrition, exercise).
   - Estimate calories or extract mood.
   - Format a structured JSON object with a timestamp.
3. The result is:
   - Saved to Airtable (`save_healthInfo`).
   - Queried from Airtable (`read_healthInfo`) for follow-up questions.
4. `Respond to Siri` replies with a natural-sounding summary (for query-based messages).

## 🛠️ Setup Instructions

1. **Webhook Configuration**
   - Node: `Siri Request`
   - Path: `/health`
   - Method: `POST`
   - Create a Siri Shortcut to send a JSON payload to this endpoint.

2. **Airtable Configuration**
   - Create a base and table with fields:
     - `Date` (string), `Intake`, `Calories`, `Exercise`, `CaloriesBurned`, `Weight`, `Mood`
   - Connect with a **Personal Access Token** and assign in `save_healthInfo` and `read_healthInfo` nodes.

3. **OpenAI Integration**
   - Use `OpenAI Chat Model` node.
   - Model: `gpt-4o-mini` or higher.
   - Assign credentials to both the model and agent nodes.

4. **System Prompt Customization (Optional)**
   - The `Health Agent` has a custom system message.
   - You can update this to refine how data is parsed or summarized.

## 📥 Input Example

Say to Siri:

> "I had a turkey sandwich with lettuce and tomato, feeling content."

The system will create:

```json
{
  "timestamp": "2025-06-09T14:23:00Z",
  "entry_type": "nutrition",
  "description": "Turkey sandwich with lettuce and tomato",
  "calories_in": 350,
  "mood": "content"
}
