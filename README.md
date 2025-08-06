# WhatsApp-Driven Google Drive Assistant (n8n)

This project is a fully functional WhatsApp-based assistant for managing Google Drive files, built entirely as a workflow in n8n. It allows a user to send simple chat commands via WhatsApp to list, move, delete, and generate AI-powered summaries of their documents.

This project was built to fulfill the requirements of an internship task.

## ✨ Features

* **List Files**: Get information about a specific file or folder.
* **Move Files**: Move a file to a different folder.
* **Safe Deletion**: A two-step confirmation process is required before any file is deleted.
* **AI Summarization**: Generate concise, bullet-point summaries of text documents (`.pdf`, `.txt`, `.docx`, etc.) using the Google Gemini API.
* **Audit Logging**: Every command sent to the assistant is automatically logged in a Google Sheet with a timestamp.

---

## 🛠️ Tech Stack

* **Automation**: n8n (self-hosted via Docker)
* **Messaging**: Twilio Sandbox for WhatsApp
* **Cloud Storage**: Google Drive API
* **Logging**: Google Sheets API
* **AI Summarization**: Google Gemini API
* **Public Webhook**: ngrok

---

## 🚀 Setup and Installation

Follow these steps to set up and run the project locally.

### Prerequisites

* **Docker Desktop**: Must be installed and running.
* **n8n**: This project is designed to run on a local n8n instance.
* **Twilio Account**: A free account is sufficient.
* **Google Cloud Account**: To create API credentials.
* **Google AI Studio Account**: To get a Gemini API Key.

### Step 1: Clone the Repository

```bash
git clone [https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github](https://docs.github.com/en/migrations/importing-source-code/using-the-command-line-to-import-source-code/adding-locally-hosted-code-to-github)
cd [repository-name]
```

### Step 2: Set up n8n with Docker

1.  Open a terminal and run the following command to start a new n8n container. This will also create a volume to persist your data.

    ```bash
    docker run -it --rm --name n8n -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n
    ```
2.  Navigate to `http://localhost:5678` in your browser and complete the n8n owner setup.

### Step 3: Set up Google Cloud Credentials

1.  Go to the [Google Cloud Console](https://console.cloud.google.com/).
2.  Create a new project.
3.  Enable the **Google Drive API** and the **Google Sheets API**.
4.  Go to **APIs & Services > Credentials**.
5.  Configure the **OAuth consent screen**. Set it to **External** and provide the required app name and contact details. Add your own Google account as a **Test User**.
6.  Click **+ Create Credentials > OAuth client ID**.
7.  Set the **Application type** to **Web application**.
8.  For the **Authorized redirect URI**, you will need to get the URL from n8n when creating your Google credential (see Step 5).

### Step 4: Import the n8n Workflow & Configure Credentials

1.  In your n8n instance, go to **Workflows** and click **Import from File**.
2.  Select the `workflow.json` file from this repository.
3.  The workflow will appear on your canvas. You will see error icons on the nodes that require credentials.
4.  Go to the **Credentials** tab in n8n and click **Add credential**.
5.  Create the following three credentials:
    * **Twilio**: Search for "Twilio" and add your **Account SID** and **Auth Token** from your [Twilio Console](https://console.twilio.com/).
    * **Google Drive API**: Search for "Google Drive API". Use the **Client ID** and **Client Secret** from the Google Cloud Console (Step 3). When you do this, n8n will give you a **Redirect URI** to paste back into your Google Cloud credential settings. You will also need to add the scope `https://www.googleapis.com/auth/drive`.
    * **Google Gemini**: Search for "Google Gemini". Get your API key from [Google AI Studio](https://aistudio.google.com/) and paste it here.

### Step 5: Set up the Webhook

1.  Open a new, separate terminal and run `ngrok` to create a public URL for your local n8n instance:
    ```bash
    ngrok http 5678
    ```
2.  `ngrok` will provide a public forwarding URL (e.g., `https://random-string.ngrok-free.app`). Copy it.
3.  In your n8n workflow, click the **Webhook** node and copy its **Test URL** path (e.g., `/webhook-test/...`).
4.  Combine the ngrok URL and the webhook path to create your full public webhook URL.
5.  Go to your **Twilio Console > Messaging > Try it out > Send a WhatsApp message > Sandbox settings**.
6.  Paste the full public webhook URL into the **"When a message comes in"** field and click **Save**.

---

## 💬 Command Reference

Send the following commands from the WhatsApp number you connected to your Twilio Sandbox.

| Command | Arguments | Example | Description |
| :--- | :--- | :--- | :--- |
| `LIST` | `[FileName]` | `LIST MyReport.pdf` | Finds a specific file and confirms its existence. |
| `DELETE` | `[FileName]` | `DELETE OldReport.pdf` | Initiates the two-step deletion process for a file. |
| `CONFIRM` | `DELETE [FileID]` | `CONFIRM DELETE 1a2b...`| Confirms the deletion using the File ID from the reply. |
| `MOVE` | `[SourceFile] [DestinationFolder]` | `MOVE MyReport.pdf Archive` | Moves a file to a specified folder. |
| `SUMMARY`| `[FileName]` | `SUMMARY MyReport.pdf` | Generates an AI-powered summary of the file. |

**Note:** The current version of the `MOVE` command does not support file or folder names with spaces.

---

## 📜 License

This project is licensed under the MIT License. See the `LICENSE` file for details.
