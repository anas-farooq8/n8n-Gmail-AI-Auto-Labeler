# 🤖 n8n Gmail AI Auto-Labeler

An intelligent n8n workflow that uses Google Gemini AI to automatically classify and label incoming Gmail emails, keeping your inbox organized.

![n8n](https://img.shields.io/badge/n8n-Workflow-EA4B71?logo=n8n)
![Google Gemini](https://img.shields.io/badge/Google_Gemini-AI-4285F4?logo=google)
![Gmail](https://img.shields.io/badge/Gmail-API-EA4335?logo=gmail)

## ✨ Features

- 🧠 **AI-Powered Classification** - Uses Google Gemini to analyze email content
- 📊 **Historical Learning** - 80% majority rule based on sender history
- 🏷️ **Auto-Labeling** - Applies labels and archives emails automatically
- 🛡️ **Smart Skip Logic** - Avoids re-processing already labeled emails
- ⚡ **Real-Time** - Polls Gmail every minute for new unread emails

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

1. **Test**: Send test emails → Run manual trigger → Verify labels
2. **Activate**: Toggle workflow to "Active"
3. **Monitor**: Check "Executions" tab for logs and errors

---

## 🧠 How AI Classification Works

1. **Fetch sender history** (up to 10 previous emails)
2. **Apply 80% rule** - If 80%+ emails from sender have same label, prefer it
3. **No history?** - Classify based on subject + body content
4. **Still uncertain?** - Compare with category examples
5. **Return** `{ "label": "Category" }` or `{ "label": "None" }`

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
