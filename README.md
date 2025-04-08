# BKFC: Build a Knowledge base From Chats 💬🧠

This repository contains a Google Colaboratory notebook designed to extract structured knowledge and insights from Google Chat conversations using Generative AI (Vertex AI Gemini).

## Motivation

Team chat applications like Google Chat are hubs for valuable information: decisions are made, questions are asked and answered, project updates are shared, and action items are assigned.

However, this knowledge often gets buried in the stream of messages, making it difficult to retrieve later.

BKFC aims to solve this by:

1.  **Automatically fetching** recent chat messages from specified Google Chat spaces.
2.  **Leveraging the power of Large Language Models** (specifically Google's Gemini via Vertex AI) to analyze the conversation content.
3.  **Extracting structured insights** such as summaries, questions & answers, action items, project updates, feedback, and technical details.
4.  **Generating human-readable summaries** (Markdown) and machine-readable data (JSON Lines) for easy consumption and integration.

This turns transient chat conversations into a queryable, summarized knowledge base.

## How it Works (Briefly)

1.  **Authentication:** The notebook authenticates with Google Cloud using OAuth 2.0 for Desktop Apps to access the Google Chat and Vertex AI APIs securely.
2.  **Fetching Data:** It uses the Google Chat API to list relevant spaces and fetch messages within a configurable time window (e.g., the last 7 days).
3.  **Processing:** Messages are sorted and grouped by chat space. The text content for each space is compiled.
4.  **AI Analysis:** Each space's compiled text is sent to a configured Gemini model via the Vertex AI API. A specific JSON schema (`ChatInsight`) is provided to instruct the model on what information to extract (e.g., summaries, Q&A pairs, action items).
5.  **Output Generation:** The structured JSON responses from the AI are parsed.
6.  **Export:** The extracted insights are saved in two formats:
    * `chat_insights.jsonlines`: Each line is a JSON object representing the insights for one chat space. Useful for programmatic processing.
    * `chat_insights.md`: A Markdown file containing a formatted, human-readable summary of the insights for all processed spaces.

## Prerequisites & Setup

Before running the notebook, you need to configure access to Google Cloud services.

1.  **Google Cloud Project:**
    * Have a Google Cloud Project available. Note your **Project ID**.
    * Enable the **Google Chat API** and **Vertex AI API** within this project (Navigate to `APIs & Services > Library` in the GCP Console).

2.  **OAuth Credentials:**
    * Configure the **OAuth consent screen** (`APIs & Services > OAuth consent screen`):
        * Set User Type (likely "Internal" if for your org, or "External" if needed).
        * Provide an App Name (e.g., `BKFC Extractor`), User Support Email, and Developer Contact info.
        * Add the following **Scopes**:
            * `https://www.googleapis.com/auth/chat.spaces.readonly`
            * `https://www.googleapis.com/auth/chat.messages.readonly`
            * `https://www.googleapis.com/auth/cloud-platform` (Required for Vertex AI access via Application Default Credentials)
    * Create **OAuth client ID** credentials (`APIs & Services > Credentials`):
        * Click `+ CREATE CREDENTIALS` -> `OAuth client ID`.
        * Select Application type: **Desktop app**.
        * Give it a name (e.g., `BKFC Colab Client`).
        * **Copy the Client ID and Client Secret.**

3.  **Colab Secrets:**
    * Open the `BKFC.ipynb` notebook in Google Colab.
    * Click the **Secrets** icon (🔑) in the left sidebar.
    * Add the following secrets (ensure "Notebook access" is toggled ON for each):
        * `BKFC_CLIENT_ID`: Paste the Client ID obtained above.
        * `BKFC_CLIENT_SECRET`: Paste the Client Secret obtained above.
        * `BKFC_PROJECT_ID`: Paste your GCP Project ID.

4.  **Review Notebook Configs:**
    * Check the parameters under the `# CONFIGS` section in the notebook, especially `MODEL` and `LOCATION`, and adjust if necessary. The Project ID, Client ID, and Secret should be picked up automatically from Colab Secrets if you set them up.

## Usage

1.  **Open the Notebook:** Open `BKFC.ipynb` in Google Colaboratory.
2.  **Complete Setup:** Ensure you have followed all steps in the **Prerequisites & Setup** section above.
3.  **Run Authentication Cell:** Execute the cell under `# SERVICES > ## Authentication` (`!gcloud auth application-default login...`).
    * Follow the URL printed in the cell output.
    * Authenticate using the Google account associated with your GCP project.
    * Grant the requested permissions (related to the scopes you configured).
    * Copy the authorization code provided on the success page.
    * Paste this code back into the input field in the Colab cell's output and press Enter.
4.  **Run Remaining Cells:** Execute the rest of the notebook cells sequentially from top to bottom.
5.  **Review Output:** The notebook will print status updates and token counts. The final cells generate and optionally download `chat_insights.jsonlines` and `chat_insights.md`.

## Customization & Adaptation

This notebook provides a foundation. You are encouraged to copy, modify, and adapt it to your specific needs! Here are some ideas:

* **Extraction Schema:** Modify the `ChatInsight` Pydantic model (and potentially the `ANALYSIS_TEMPLATE`) to extract different types of information relevant to your team (e.g., specific keywords, sentiment, decisions made).
* **Filtering:** Change the `SINCE_DAYS` parameter or modify the filtering logic in the `## Spaces` and `## Messages` sections to target specific spaces or different timeframes.
* **Model & Parameters:** Experiment with different Gemini models (`MODEL` parameter) or adjust the `TEMPERATURE` for more creative or deterministic outputs.
* **Integration:** Instead of saving to local files, modify the export section to:
    * Save data to Google Cloud Storage.
    * Insert insights into a BigQuery table.
    * Send summaries to another chat space or email.
    * Store results in a vector database for semantic search.
* **Error Handling:** Enhance the error handling, especially around API calls.
* **Scheduling:** Use Google Cloud Scheduler and Cloud Functions (or Vertex AI Pipelines) to run this notebook automatically on a regular basis.

## Contributing

Feel free to fork this repository, submit issues, or propose enhancements via Pull Requests if you have ideas for improvements.

## License

This project is licensed under the Apache License 2.0. See the `LICENSE` file for details (You should add an actual LICENSE file to your repo).
