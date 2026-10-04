# 🧾 Receipt Expense Tracker

An **n8n automation workflow** that extracts expense data from uploaded receipt images using AI vision (Groq + LLaMA 4 Scout) and logs them to a Google Sheet — fully hands-free.

---

## ✨ Features

- **📸 Upload & Go** — Simple web form to upload receipt images (JPEG, PNG, etc.)
- **🤖 AI-Powered Extraction** — Uses Groq's LLaMA 4 Scout vision model to read and parse receipts
- **📊 Auto-Logging** — Appends extracted data to a Google Sheet automatically
- **🏷️ Smart Categorization** — Classifies expenses into categories like Food, Groceries, Travel, Shopping, Utilities, Health, Entertainment, and Other
- **💱 Multi-Currency** — Detects and records the currency from the receipt (INR, USD, etc.)

---

## 🏗️ Architecture

```
┌─────────────────┐     ┌──────────────┐     ┌──────────────────┐     ┌─────────────┐     ┌───────────────┐
│  Form Trigger    │────▶│  Convert to  │────▶│  Groq API Call   │────▶│  Parse JSON │────▶│ Google Sheets │
│  (Upload Receipt)│     │  Base64      │     │  (LLaMA 4 Scout) │     │  Response   │     │ (Append Row)  │
└─────────────────┘     └──────────────┘     └──────────────────┘     └─────────────┘     └───────────────┘
```

### Workflow Nodes

| # | Node | Type | Purpose |
|---|------|------|---------|
| 1 | **On form submission** | Form Trigger | Presents a web form for uploading a receipt image |
| 2 | **Code in JavaScript** | Code | Converts the uploaded binary file to a Base64 string |
| 3 | **HTTP Request** | HTTP Request | Sends the Base64 image to Groq's vision API for extraction |
| 4 | **Code in JavaScript1** | Code | Parses the AI response JSON into clean fields |
| 5 | **Append row in sheet1** | Google Sheets | Appends the extracted data as a new row in the spreadsheet |

---

## 📋 Extracted Fields

The AI extracts the following from each receipt:

| Field | Description | Example |
|-------|-------------|---------|
| **Vendor** | Store or merchant name | `Walmart`, `Starbucks` |
| **Date** | Date printed on receipt (YYYY-MM-DD) | `2026-10-04` |
| **Total Amount** | Final grand total paid | `1250.00` |
| **Currency** | 3-letter currency code | `INR`, `USD` |
| **Category** | Expense category | `Groceries`, `Food` |

---

## 🚀 Setup Guide

### Prerequisites

- [n8n](https://n8n.io/) instance (self-hosted or cloud)
- [Groq API Key](https://console.groq.com/) (free tier available)
- Google account with Sheets access

### Step 1 — Import the Workflow

1. Open your n8n instance
2. Go to **Workflows → Import from File**
3. Select `My_workflow_7_fixed.json`

### Step 2 — Configure Groq API Key

1. Open the **HTTP Request** node
2. Find the `Authorization` header
3. Replace `PASTE_YOUR_NEW_GROQ_KEY_HERE` with your actual Groq API key:
   ```
   Bearer gsk_your_actual_key_here
   ```

### Step 3 — Set Up Google Sheets

1. Create a new Google Sheet (or use an existing one)
2. Add the following column headers in Row 1:

   | A | B | C | D | E |
   |---|---|---|---|---|
   | Vendor | Date | Total Amount | Currency | Category |

3. Open the **Append row in sheet1** node
4. Connect your **Google Sheets OAuth2** credentials
5. Select your spreadsheet and sheet

### Step 4 — Activate

1. Toggle the workflow to **Active**
2. Access the form URL provided by the Form Trigger node
3. Upload a receipt and watch the magic ✨

---

## 🛠️ Configuration

### Changing the AI Model

The workflow uses `meta-llama/llama-4-scout-17b-16e-instruct` by default. To switch models, edit the JSON body in the **HTTP Request** node and update the `model` field. Compatible Groq vision models include:

- `meta-llama/llama-4-scout-17b-16e-instruct` (default)
- `meta-llama/llama-4-maverick-17b-128e-instruct`

### Customizing Categories

Modify the prompt inside the **HTTP Request** node's JSON body. Find the `category` instruction and edit the list:

```
category (one of: Food, Groceries, Travel, Shopping, Utilities, Health, Entertainment, Other)
```

### Adding More Fields

1. Update the extraction prompt in the **HTTP Request** node
2. Add the new field mapping in the **Code in JavaScript1** (parser) node
3. Add the corresponding column in your Google Sheet
4. Map the new column in the **Append row in sheet1** node

---

## 📂 Project Structure

```
receipt-expense-tracker/
├── My_workflow_7_fixed.json   # n8n workflow definition (import this)
└── README.md                  # This file
```

---

## ⚠️ Important Notes

- **API Key Security** — Never commit your Groq API key to version control. The workflow file contains a placeholder (`PASTE_YOUR_NEW_GROQ_KEY_HERE`) that must be replaced after import.
- **Image Quality** — Better quality receipt images produce more accurate extractions. Ensure receipts are well-lit and clearly readable.
- **Null Handling** — If the AI cannot read a field from the receipt, it returns `null` rather than guessing.
- **Date Accuracy** — The AI is instructed to extract only the date printed on the receipt, not today's date.

---

## 🧰 Tech Stack

| Component | Technology |
|-----------|------------|
| Automation | [n8n](https://n8n.io/) |
| AI Vision | [Groq API](https://groq.com/) — LLaMA 4 Scout |
| Data Storage | [Google Sheets](https://sheets.google.com/) |
| Language | JavaScript (n8n Code nodes) |

---

## 📜 License

This project is open source. Feel free to use, modify, and share.
