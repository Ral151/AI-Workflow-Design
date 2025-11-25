# 🤖 Intelligent Calendar Assistant with AI-Powered Meeting Summary (CCAI9024 ASSIGNMENT)

An automated workflow that integrates **Telegram Bot**, **Google Calendar**, and **AI** to provide intelligent calendar management and meeting summarization.

---

## 📋 **Table of Contents**

- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Workflow Components](#workflow-components)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

## ✨ **Features**

### **1. 🗓️ AI Calendar Bot**
- Natural language calendar management via Telegram
- Create, read, update, and delete calendar events
- Query schedule using conversational commands
- Conversational memory (remembers context within session)

**Examples:**
```
"Schedule team meeting tomorrow at 2 PM"
"What's on my calendar today?"
"Delete the meeting on November 27"
"Move the 3 PM meeting to 4 PM"
```

### **2. 📝 Meeting Summary Agent**
- Paste meeting notes (>200 characters) into Telegram
- AI automatically generates summary
- Extracts action items with deadlines
- Creates calendar events for all action items
- Supports both **timed events** (meetings) and **all-day events** (tasks)
- Preserves AM/PM time formats

**Automatic Detection:**
- Detects dates: "December 15, 2025"
- Detects times: "at 2 PM", "@ 10:30 AM"
- Creates appropriate event types

### **3. ⏰ Daily Reminders**
- Automated daily TO-DO list sent to Telegram
- Categorizes events by urgency:
  - 🚨 **Overdue**
  - 🔥 **Urgent** (Next 24 hours)
  - ⚠️ **This Week**
  - 📆 **Upcoming** (grouped by month)
- Distinguishes between meetings (⏰) and tasks (📋)
- Scheduled delivery (default: 9 AM daily)

---

## 🏗️ **Architecture**

```mermaid
graph LR
    A[👤 User] -->|Messages| B[💬 Telegram Bot]
    B -->|Poll/Send| C[⚙️ n8n Workflow]
    C -->|Query| D[🤖 Google Gemini AI]
    C -->|Summarize| E[🤖 HuggingFace API]
    C -->|CRUD| F[📅 Google Calendar]
    
    style A fill:#ff6b6b
    style B fill:#339af0
    style C fill:#4dabf7
    style D fill:#9775fa
    style E fill:#9775fa
    style F fill:#51cf66
```

### **Data Flow**

1. **Telegram Polling** (every 3 seconds) → Fetch new messages
2. **Message Processing** → Detect type (command/calendar/meeting notes)
3. **AI Processing** → Route to appropriate handler
4. **Action Execution** → Update calendar or generate summary
5. **Response** → Send result back to Telegram

---

## 📦 **Prerequisites**

### **Required Services**

1. **n8n** (v1.119.1 or higher)
   - Self-hosted or cloud instance
   - [Installation Guide](https://docs.n8n.io/hosting/)

2. **Telegram Bot**
   - Bot Token from [@BotFather](https://t.me/BotFather)
   - Channel/Group Chat ID

3. **Google Calendar API**
   - OAuth2 credentials
   - [Setup Guide](https://developers.google.com/calendar/api/quickstart)

4. **Google Gemini API**
   - API key from [Google AI Studio](https://makersuite.google.com/app/apikey)

5. **HuggingFace API**
   - Access token from [HuggingFace](https://huggingface.co/settings/tokens)

6. **VPN**
   - Connect your device with any VPN to ensure you can access Gemini and run the workflow
     
---

## 🚀 **Installation**

### **Step 1: Import Workflow**

1. Open your n8n instance
2. Click **"Workflows"** → **"Import from File"**
3. Select `Master_Workflow_Final.json`
4. Click **"Import"**

### **Step 2: Configure Credentials**

The workflow requires **4 credentials**. Follow the setup below:

---

## 🔐 **Configuration**

### **1. Telegram Bot Token**

**Get Token:**
```
1. Open Telegram and search for @BotFather
2. Send /newbot
3. Follow instructions to create bot
4. Copy the Bot Token (format: 1234567890:ABCdefGHIjklMNOpqrsTUVwxyz)
```

**Get Chat ID:**
```
1. Add your bot to a channel/group
2. Send a test message in the channel
3. Visit: https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates
4. Find "chat":{"id":-1001234567890} in the response
5. Copy the chat ID (include the minus sign)
```

**Configure in n8n:**
```
Settings → Credentials → Add Credential → Telegram
- Access Token: [Paste Bot Token]
- Save
```

**Update Workflow:**
- Find all Telegram nodes
- Replace `chatId: "-1003487374430"` with your Chat ID

---

### **2. Google Calendar OAuth2**

**Create Google Cloud Project:**

1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Create new project or select existing
3. Enable **Google Calendar API**
   - APIs & Services → Library → Search "Calendar" → Enable

**Create OAuth Credentials:**

1. APIs & Services → Credentials → Create Credentials → OAuth Client ID
2. Application type: **Web application**
3. Authorized redirect URIs: `https://YOUR_N8N_URL/rest/oauth2-credential/callback`
4. Copy **Client ID** and **Client Secret**

**Configure in n8n:**
```
Settings → Credentials → Add Credential → Google Calendar OAuth2
- Client ID: [Paste]
- Client Secret: [Paste]
- Click "Connect to Google"
- Grant permissions
- Save
```

**Update Workflow:**
- Find all Google Calendar nodes
- Replace email `geraldsch.cdg@gmail.com` with your Google email

---

### **3. Google Gemini API**

**Get API Key:**

1. Visit [Google AI Studio](https://makersuite.google.com/app/apikey)
2. Click **"Create API Key"**
3. Copy the key

**Configure in n8n:**
```
Settings → Credentials → Add Credential → Google PaLM API (Gemini)
- API Key: [Paste]
- Save
```

---

### **4. HuggingFace API Token**

**Get Token:**

1. Visit [HuggingFace Settings](https://huggingface.co/settings/tokens)
2. Click **"New Token"**
3. Name: `n8n-workflow`
4. Type: **Read**
5. Copy token

**Update Workflow:**
- Find node **"🤖 Summarize Meeting"**
- Replace Authorization header:
  ```
  Bearer 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
  ```
  with
  ```
  Bearer YOUR_HUGGINGFACE_TOKEN
  ```

---

## 🎮 **Usage**

### **Activate Workflow**

1. Open workflow in n8n
2. Click **"Active"** toggle (top right)
3. Workflow is now running!

### **Basic Commands**

#### **Help & Information**
```
/start    - Welcome message with features
/help     - Display help menu
```

#### **Calendar Management**
```
Schedule team meeting tomorrow at 2 PM
What's on my calendar today?
Show me events for next week
Delete the meeting on November 27
Move the 3 PM meeting to 4 PM
Check my schedule for December 15
```

#### **Meeting Summary**
```
Just paste your meeting notes (>200 characters) directly!
Include keywords like: meeting, attendees, action items, deliverables
```

**Example Meeting Notes:**
```
Project Kickoff Meeting - November 25, 2025

ATTENDEES:
- John (Project Manager)
- Sarah (Developer)
- Mike (Designer)

DISCUSSION:
We discussed the new e-commerce platform redesign.
Timeline is 3 months starting December 1st.

ACTION ITEMS:
- John to finalize requirements by December 5, 2025
- Sarah to set up development environment by December 10, 2025 at 2 PM
- Mike to create wireframes by December 15, 2025

NEXT STEPS:
- Weekly check-ins every Monday at 10 AM starting December 9, 2025
```

The bot will:
1. Generate AI summary
2. Extract 3+ action items
3. Create calendar events automatically
4. Send formatted response

---

## 🧩 **Workflow Components**

### **Triggers**

| Node | Frequency | Purpose |
|------|-----------|---------|
| **Every 3 Seconds** | 3s | Poll Telegram for new messages |
| **Daily Alerts** | 9 AM daily | Send TO-DO list reminder |

### **Main Flows**

#### **1. Calendar Bot Flow**
```
Telegram Poll → Process Updates → Remove Duplicates → Route Commands
    ↓
[/start or /help] → Send Direct Response
    ↓
[Regular Query] → AI Calendar Agent → Google Calendar Tools → Format → Send
```

#### **2. Meeting Summary Flow**
```
Detect Long Message → Processing Alert → HuggingFace Summarize
    ↓
Merge → Format Notes → Extract Actions → Parse Dates
    ↓
Create Calendar Events + Send Summary
```

#### **3. Daily Reminder Flow**
```
Schedule Trigger → Get All Events → Categorize by Urgency
    ↓
Build TO-DO List → Send to Telegram
```

### **Key Nodes Explained**

| Node Name | Function |
|-----------|----------|
| **Process Updates** | Parse Telegram messages, detect type (command/calendar/notes) |
| **Remove Duplicates** | Filter previously seen messages |
| **AI Calendar** | Gemini AI agent with Google Calendar tools |
| **Memory** | Stores conversation context per session |
| **Summarize Meeting** | HuggingFace BART model for text summarization |
| **Extract Actions** | Smart extraction of action items from notes |
| **Parse Dates** | Detect dates & times, create appropriate event types |
| **Process Alerts** | Categorize events by urgency (overdue/urgent/soon/upcoming) |

---

## 🛠️ **Customization**

### **Change Daily Reminder Time**

1. Find node: **"⏰ TRIGGER: Daily Alerts"**
2. Change `triggerAtHour: 9` to your preferred hour (0-23)
3. Save workflow

### **Change Polling Frequency**

1. Find node: **"Every 3 Seconds"**
2. Change `secondsInterval: 3` to desired seconds
3. ⚠️ Don't go below 2 seconds to avoid rate limits

### **Modify Telegram Channel**

1. Search for: `-1003487374430`
2. Replace with your channel ID in all nodes

### **Change Timezone**

1. Workflow Settings → Timezone → Select your timezone
2. Find node **"📅 Parse Dates"**
3. Update:
   ```javascript
   const TIMEZONE = 'Asia/Hong_Kong';
   const TIMEZONE_OFFSET = '+08:00';
   ```

---

## 🐛 **Troubleshooting**

### **Issue: Bot Not Responding**

**Check:**
1. Workflow is **Active** (toggle in top right)
2. Telegram credentials are configured
3. Bot token is correct
4. Bot is added to your channel/group

**Debug:**
- Manually execute "Every 3 Seconds" node
- Check execution logs for errors

---

### **Issue: Calendar Events Not Created**

**Check:**
1. Google Calendar OAuth2 is connected
2. Calendar email matches in all nodes
3. Calendar API is enabled in Google Cloud

**Debug:**
- Test node "AI Calendar" → Check if tools are working
- Verify date format in error logs

---

### **Issue: Meeting Summary Not Working**

**Check:**
1. HuggingFace token is valid
2. Message is >200 characters
3. Contains keywords: meeting, action items, attendees

**Debug:**
- Check node "🤖 Summarize Meeting" → View HTTP response
- Verify token hasn't expired

---

### **Issue: Daily Reminder Not Sent**

**Check:**
1. Trigger time is in correct timezone
2. Workflow is active during trigger time
3. Telegram channel ID is correct

**Debug:**
- Manually execute "⏰ TRIGGER: Daily Alerts" node
- Check if events are being fetched

---

### **Issue: Duplicate Messages**

**Solution:**
1. Deactivate workflow
2. Workflow Settings → Static Data → Clear Static Data
3. Reactivate workflow

---

### **Issue: AI Not Remembering Context**

**Check:**
1. Memory node is connected to AI Calendar
2. Session ID is consistent (`tg_${chatId}`)

**Reset Memory:**
- Send `/start` command to create new session

---

## 📊 **Performance & Limits**

| Component | Limit | Notes |
|-----------|-------|-------|
| Telegram Messages | 30 messages/second | Rate limited by Telegram |
| Calendar Events | No limit | Google Calendar API quota |
| Message Length | 4096 characters | Telegram limit |
| Workflow Executions | Depends on n8n plan | Self-hosted = unlimited |
| HuggingFace API | Rate limited | Free tier: 1000 requests/day |

---

## 📈 **Monitoring**

### **View Executions**

1. n8n → Executions tab (left sidebar)
2. Filter by status (Success/Error/Running)
3. Click execution to see detailed logs

### **Check Statistics**

Monitor in n8n:
- Total executions
- Success rate
- Error rate
- Average execution time

---

## 🔒 **Security Best Practices**

1. **Never commit credentials** to version control
2. Use **environment variables** for sensitive data
3. Restrict **Google Calendar API** scope to minimum required
4. Regularly **rotate API tokens**
5. Use **HTTPS** for n8n instance
6. Enable **2FA** on all accounts

---

## 🆘 **Support**

### **Common Issues**

- [n8n Documentation](https://docs.n8n.io/)
- [n8n Community Forum](https://community.n8n.io/)
- [Telegram Bot API](https://core.telegram.org/bots/api)
- [Google Calendar API](https://developers.google.com/calendar/api/guides/overview)

### **Contact**

For workflow-specific questions, contact the original developer or refer to this documentation.

---

## 📝 **License**

This workflow is provided as-is for educational purposes.

---

## 🙏 **Acknowledgments**

- **n8n** - Workflow automation platform
- **Google Gemini** - AI language model
- **HuggingFace** - Text summarization model
- **Telegram** - Messaging platform
- **Google Calendar** - Calendar service

---

## 📚 **Appendix**

### **A. Workflow Statistics**

- **Total Nodes:** 35
- **Trigger Nodes:** 2
- **AI Nodes:** 2
- **Calendar Nodes:** 7
- **Telegram Nodes:** 6
- **Processing Nodes:** 18

### **B. API Endpoints Used**

```
Telegram Bot API: https://api.telegram.org/bot{token}/getUpdates
HuggingFace: https://router.huggingface.co/hf-inference/models/philschmid/bart-large-cnn-samsum
Google Calendar API: https://www.googleapis.com/calendar/v3/
Google Gemini API: Via n8n integration
```

### **C. Timezone Support**

Currently configured: **Asia/Hong_Kong (UTC+8)**

Supported timezones: [Full list](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

---

**Last Updated:** November 25, 2025  
**Workflow Version:** 1.0  
**n8n Version:** 1.119.1  
**Author:** Gerald Sch / Ral151

---
