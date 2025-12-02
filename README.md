# 🤖 n8n Gmail AI Auto-Labeler

An intelligent n8n workflow that uses Google Gemini AI to automatically classify and label Gmail emails, keeping your inbox organized.

![n8n](https://img.shields.io/badge/n8n-Workflow-EA4B71?logo=n8n)
![Google Gemini](https://img.shields.io/badge/Google_Gemini-AI-4285F4?logo=google)
![Gmail](https://img.shields.io/badge/Gmail-API-EA4335?logo=gmail)

## 📸 Demo

![Workflow Demo](demo.png)

## ✨ Features

### Dual Execution Modes
- ⚡ **Auto Mode** - Gmail Trigger polls inbox every minute for new unread emails
- 🔄 **Manual Mode** - Process existing emails in bulk (label past emails)

### AI-Powered Intelligence
- 🧠 **Google Gemini AI** - Advanced email content analysis
- 📊 **Historical Learning** - 80% majority rule based on sender patterns
- 🔍 **Two AI Tools**:
  - **Get Emails By Sender** - Fetches up to 10 past emails from same sender
  - **Get Emails By Label** - Retrieves category examples for comparison

### Smart Automation
- 🏷️ **Auto-Labeling** - Applies correct Gmail labels automatically
- 📦 **Auto-Archiving** - Removes labeled emails from inbox
- 🛡️ **Skip Logic** - Prevents re-processing already labeled emails
- 🔁 **Batch Processing** - Handles multiple emails efficiently

---

## 🏷️ Label Categories

| Label | Description |
|-------|-------------|
| **Meetings** | Calendar invites, Zoom/Meet links, appointments |
| **Expenses** | Bills, invoices, receipts, payment reminders |
| **Income** | Payments received, payouts, deposits |
| **Notify / Verify** | Verification codes, login alerts, 2FA codes |
| **Orders / Deliveries** | Order confirmations, shipping updates, tracking |
| **Inquiries** | Business outreach, sales proposals, partnerships |
| **Trash Likely** | Spam, newsletters, promotions, marketing emails |

> If no category fits, the email remains in inbox.

---

## 📦 Prerequisites

- **n8n** (v1.0.0+) - [Get it here](https://n8n.io/)
- **Gmail OAuth2** credentials
- **Google Gemini API** key - [Get it here](https://ai.google.dev/) (Free: 15 req/min)

---

## 🚀 Installation

1. **Import Workflow**
   - Open n8n → Add workflow → Import from File
   - Select `Workflow.json`

2. **Create Gmail Labels** (must match exactly):
   - Meetings
   - Income
   - Inquiries
   - Notify / Verify
   - Expenses
   - Orders / Deliveries
   - Trash Likely
---

## ⚙️ Configuration

### 1. Get Gmail Label IDs
- Run the manual trigger → Execute "Get Labels Info" node
- Copy each label's ID (e.g., `Label_2420158633459891270`)

### 2. Update Code Nodes
Update these **TWO nodes** with your label IDs:

**"Check Label Existence"** node:
```javascript
const labelMap = {
  "Label_YOUR_ID_HERE": "Meetings",
  "Label_YOUR_ID_HERE": "Inquiries",
  // ... add all labels
};
```

**"Convert Label to Label ID"** node:
```javascript
const labelToId = {
  "Meetings": "Label_YOUR_ID_HERE",
  "Inquiries": "Label_YOUR_ID_HERE",
  "Notify / Verify": "Label_YOUR_ID_HERE",
  "Expenses": "Label_YOUR_ID_HERE",
  "Orders / Deliveries": "Label_YOUR_ID_HERE",
  "Trash Likely": "Label_YOUR_ID_HERE"
};
```

### 3. Add Credentials
- **Gmail OAuth2**: Connect your Gmail account
- **Google Gemini API**: Add your API key

---

## 🎮 Usage

### Auto Mode (Real-Time Processing)
1. **Activate** the workflow (toggle to "Active")
2. Workflow automatically processes new unread emails every minute
3. Monitor via "Executions" tab

### Manual Mode (Bulk Processing)
1. Click **"When clicking 'Execute workflow'"** manual trigger
2. The "Get many messages" node fetches existing emails (default: 10)
3. Processes and labels all fetched emails in one execution
4. Useful for labeling past emails or testing

> **Tip**: Adjust `limit` in "Get many messages" node to process more/fewer emails

---

## 🧠 How AI Classification Works

The workflow uses a **LangChain AI Agent** with Google Gemini and two custom tools:

### AI Tools Available to Agent
1. **Get Emails By Sender Email** (Always called first)
   - Fetches up to 10 historical emails from same sender
   - Returns: subject, body, previously assigned labels
   - Enables sender pattern recognition

2. **Get Emails By Label** (Called when uncertain)
   - Fetches up to 10 examples from a specific label category
   - Helps compare and confirm classification
   - Used for edge cases and validation

### Classification Process
1. **Fetch sender history** using Tool #1
2. **Apply 80% rule** - If 80%+ sender emails have same label, strongly prefer it
3. **No history?** - Classify based purely on subject + body content
4. **Still uncertain?** - Use Tool #2 to compare with category examples
5. **Return structured JSON**: `{ "label": "Category" }` or `{ "label": "None" }`

---

## 🎨 Customization

- **Add labels**: Create in Gmail → Get ID → Update both Code nodes + AI prompt
- **Change polling**: Edit "Gmail Trigger" node → `mode: "every5Minutes"`
- **Adjust threshold**: Modify "80% rule" in AI system prompt
- **Email limit**: Change `limit: 10` in Gmail tool nodes

---

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| Workflow not running | Check if activated + verify credentials |
| Labels not applied | Verify label IDs in Code nodes |
| AI returns "None" | Check API quota (15/min) + test with obvious emails |
| Re-processing emails | Verify "Check Label Existence" node has correct IDs |
| Rate limits | Reduce polling frequency or upgrade API tier |

---
