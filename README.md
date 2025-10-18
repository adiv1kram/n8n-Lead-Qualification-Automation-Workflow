# n8n Lead Qualification Automation Workflow

This repository contains the exported `.json` files for an n8n workflow that automates the process of lead qualification using a Generative AI model.

## Overview

This project automatically captures new leads from Gmail or a manual webhook, uses a language model (Gemini) to analyze and categorize their intent, and then performs actions based on that category. High-intent leads are added to a CRM (Google Sheet), while medium/low-intent leads receive an automated follow-up email. Finally, every lead is logged in a master sheet, and a real-time notification is sent to Slack.

## Features

- **Multi-Trigger Input:** Ingests leads from both **Gmail** (for live, automated processing) and a **Webhook** (for manual testing or form submissions).
- **AI-Powered Categorization:** Uses a language model (Google Gemini) to analyze the lead's message and categorize it as `High Intent`, `Medium Intent`, or `Low Intent`.
- **Modular Design:** Utilizes a reusable **Subflow** for the AI categorization step, keeping the main workflow clean and easy to maintain.
- **Conditional Branching:** Uses `IF` nodes to route leads to different actions based on their categorized intent.
- **Automated CRM Entry:** Automatically adds `High Intent` leads to a Google Sheet acting as a CRM.
- **Automated Email Replies:** Automatically sends a follow-up email via **Gmail** to `Medium` and `Low Intent` leads.
- **Comprehensive Logging:** Records every lead and its category in a master **Google Sheet** for tracking.
- **Real-Time Alerts:** Sends a summary of every new lead to a **Slack** channel.
- **Error Handling:** Implements simple retry logic on all external API calls (Gemini, Google, Slack) to handle temporary network failures.

## Workflow Structure

This project consists of two separate n8n workflows that work together.

1.  **`Main workflow.json`**: This is the primary workflow that manages the entire process. It handles triggers, data standardization, branching logic, and all actions (Google Sheets, Gmail, Slack). It calls the subflow for the analysis step.
2.  **`Subflow - Lead Categorization.json`**: This is a reusable, modular workflow. Its only job is to receive a text message, send it to the Gemini API with a specific prompt, and return a structured JSON object containing the `category` and `confidence` score.

## How to Use

To use this project in your own n8n instance:

1.  **Download** both `.json` files from this repository.
2.  **Import Workflows:** In your n8n canvas, go to **File > Import from File...** and import both `Main workflow.json` and `Subflow - Lead Categorization.json`.
3.  **Activate Subflow:** Open the `Subflow - Lead Categorization` workflow, make sure it is saved, and toggle it to **Active**.
4.  **Create Credentials:** You must create n8n credentials for the following services used in the workflows:
    * **Google Gemini API:** For the `Google Gemini` node.
    * **Google Sheets:** (Using OAuth) For the `Google Sheets` nodes.
    * **Gmail:** (Using OAuth) For the `Gmail` node.
    * **Slack:** (Using OAuth) For the `Slack` node.
5.  **Configure Main Workflow:** Open the `Main workflow` and update the following nodes with your own credentials and settings:
    * **Gmail Trigger:** Connect your Gmail credential and set any filters.
    * **Execute Sub-workflow:** Ensure it is pointing to your imported subflow.
    * **Google Sheets (CRM):** Select your credential, Google Sheet document, and tab. Map the columns.
    * **Gmail (Send):** Select your credential.
    * **Google Sheets (Master Log):** Select your credential, Google Sheet document, and tab. Map the columns.
    * **Slack:** Select your credential and the Slack channel you want to post to.
6.  **Activate Main Workflow:** Once configured, save the workflow and toggle it to **Active**. It is now live and will process leads from your Gmail trigger or test webhook.

## Tech Stack

-   **n8n:** Workflow automation platform
-   **Google Gemini:** AI Language Model for intent analysis
-   **Google Sheets:** Used as a CRM and master log
-   **Gmail:** For sending automated email replies
-   **Slack:** For real-time team notifications
