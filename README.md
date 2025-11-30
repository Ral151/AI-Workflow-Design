# 🤖 Intelligent Calendar Assistant with AI-Powered Meeting Summary (CCAI9024 ASSIGNMENT)

An automated workflow that integrates **Telegram Bot**, **Google Calendar**, and **AI** to provide intelligent calendar management and meeting summarization.

---

## 📋 **Table of Contents**

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Workflow Components](#workflow-components)
- [Customization](#customization)
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
"Schedule team meeting on 12th of December at 2 PM - 3PM"
"What's my schedule tomorrow?"
"Delete the meeting on November 27."
"Move the 3 PM meeting to 4 PM"
```

### **2. 📝 Meeting Summary Agent**
- Paste meeting notes (>300 characters) into Telegram
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
- Scheduled delivery (default: 9 AM daily)

---

### **Data Flow**

1. **Telegram Polling** (every 3 seconds) → Fetch new messages
2. **Message Processing** → Detect type (command/calendar/meeting notes)
3. **AI Processing** → Route to appropriate handler
4. **Action Execution** → Update calendar or generate summary
5. **Response** → Send result back to Telegram

---

## 📦 **Prerequisites**

### **Required Services**

1. **N8N** (1.121.3 or latest)
   - Download the N8N using either npx or npm.  
   - [Installation Guide](https://docs.n8n.io/hosting/installation/npm/)
    
2. **Telegram App**
   - Download the Telegram for [Windows](https://desktop.telegram.org/) or [Mac](https://macos.telegram.org/)
   - Requires creating a new channel exclusively for the bot. 

2. **Telegram Bot**
   - Documentation of how to get a Telegram bot token from this [official website](https://core.telegram.org/bots/tutorial#introduction)
   - Bot Token from [@BotFather](https://t.me/BotFather)
   - Channel Chat ID

4. **Google Calendar API**
   - OAuth2 credentials
   - [Setup Guide](https://developers.google.com/calendar/api/quickstart)

5. **OpenAI API**
   - API key from [Open AI Platform](https://platform.openai.com/api-keys)

6. **VPN**
   - Connect your device with any VPN to ensure you can access OpenAI and run the workflow.

7. **ConvertAPI**
   - Token from [ConvertAPI](https://www.convertapi.com/a/authentication)
   - Free tier: 250 conversions/month


     
---

## 🚀 **Installation**

### **Step 1: Install n8n**
# Via npm (recommended)
npm install -g n8n

# Start n8n
n8n start

# Access UI
http://localhost:5678

### **Step 2: Import Workflow**

1. Open your n8n instance
2. Click **"Create Workflows"** → **"Three dots in upper right corner"** → **"Import from File"**
3. Select `workflow.json`
4. Click **"Import"**

### **Step 3: Configure Credentials**

The workflow requires **4 credentials**. Follow the setup below:

---

## 🔐 **Configuration**

### **1. Telegram Bot Token**

**Get Token:**
```
1. Open Telegram and search for @BotFather
2. Send /start
3. Send /newbot
4. Follow instructions to create a bot
5. Copy the Bot Token (format: 1234567890:ABCdefGHIjklMNOpqrsTUVwxyz)
```

**Get Chat ID:**
```
1. Add your bot to a channel
2. Send a test message in the channel
3. Visit: https://api.telegram.org/bot<YOUR_BOT_TOKEN>/getUpdates
4. Find following format like "sender_chat":{"id":-1001234567890} in the response
5. Copy your chat ID (include the minus sign)
```

**Configure in n8n:**
```
Dashboard -> Credentials -> Create Credentials -> Search for Telegram API
- Access Token: [Paste Bot Token]
- Save
```

**Update Workflow:**
- Find Telegram node that has chatID Value -1003487374430
- Replace `chatId: "-1003487374430"` with your Chat ID. 

---

### **2. Google Calendar OAuth2**

**Create Google Cloud Project:**

1. Go to [Google Cloud Console](https://console.cloud.google.com)
2. Create a new project or select an existing one
3. Select the project you want to use
4. Enable **Google Calendar API**
   - APIs & Services → Enable APIs and Services -> Library -> Search for Google Calendar API -> Enable 

**Create OAuth2 Credentials:**

1. APIs & Services → Credentials → Create Credentials → OAuth Client ID
2. Configure consent screen (if first time):
   - User Type: External
   - App name: "Meeting Assistant."
   - Support email: [Your email]
   - Save
4. Application type: **Web application**
5. Name: "n8n Calendar Integration"
6. Authorized redirect URIs: 
   `http://localhost:5678/rest/oauth2-credential/callback`
   (Replace localhost:5678 with your n8n URL if different)
7. Click Create
8. Configure OAuth clients (if first time):
   Create OAuth Client -> 
10. Copy **Client ID** and **Client Secret**


**Register Your Account**
1. Audience -> Scroll down to Test users -> Add users -> Add your gmail account -> Save


**Configure in n8n:**
```
Dashboard -> Credentials -> Create Credentials -> Google Calendar OAuth2
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

### **3. OpenAI API**

**Get API Key:**

1. Visit: [OpenAI Platform](https://platform.openai.com/api-keys)
2. Click "Create new secret key"
3. Name: "n8n-meeting-assistant"
4. Copy key: sk-proj-xxxxxxxxxxxxxxxxxxxxx
5. Store securely (shown only once!)

**Configure in n8n:**
```
Find node "📄 ConvertAPI - Extract Text" and replace:
// Line 14 in the code:
const CONVERTAPI_SECRET = 'YOUR_CONVERTAPI_SECRET';

// Replace with:
const CONVERTAPI_SECRET = 'abc123def456...';
```

---

### **3. OpenAI API**
**Get API Token:**

1. Sign up: https://www.convertapi.com/a/signup
2. Verify email
3. Go to: https://www.convertapi.com/a/auth
4. Create a new token or simply copy your secret token: abc123def456... 

---

## 🎮 **Usage**

### **Change The Date Settings**
In AI Calendar node, under the System Message section:
`Today's date is 29 November, 2025.\n\nYou're an AI Calendar Assistant with Google Calendar access.\n\nUse tools to create, delete, update, check, and get events.\n\nBe friendly, concise. Use emojis: ✅❌🗑️✏️\n\nConfirm all actions. Do not use any LaTeX at all.`

Change the Today's date to the current date. Otherwise, you need to be very specific and cannot use word like tomorrow in your input. 


### **Activate Workflow**

**If First Time chat in the channel**
1. Open workflow in n8n
2. Click **"Active"** toggle (top right)
3. Workflow is now running!
4. You can start to text message in your Telegram channel
5. If you want to take a look at the result of the daily reminder without waiting for the designated time, click execute workflow directly beside the "⏰ TRIGGER: Daily Alerts" node.

**If not, First time chat in the channel**
1. Open workflow in n8n
2. Remove a node called Remove Duplicates
3. Create a new Remove Duplicates again with the same settings:
   ```
   Operation: Remove Items Processed in Previous Executions
   Keep Items Where: Value is New
   Value to Dedupe on: {{ $json.messageText }}
   ```
5. You can start to text message in your Telegram channel
6. Click **"Active"** toggle (top right)
7. Workflow is now running!
8. Continue your conversation

### **Basic Commands**

#### **Help & Information**
```
/start    - Welcome message with features
/help     - Display help menu
```

#### **Calendar Management**
```
Each Input is separated by gap:
Schedule a math test on 9th December at 9am-10:30am.

Update my math test on 9th December to 3rd December.

My math test on 3rd December got cancelled.

What's my schedule for the next 3 weeks.

Delete all my events for the next 3 weeks

etc
```

#### **Meeting Summary**
```
Just paste your meeting notes (>300 characters) or simply upload your PDF or DOCX file directly:

Important things: 
Include keywords in a particular section, like: ACTION ITEMS, TASK ASSIGNMENTS, TO DO LIST, DELIVERABLES, NEXT STEPS, FOLLOW UP ITEMS. Follow up with several action items under the section to ensure the workflow can extract the action items and parse them to the calendar system. Try to specifically mention the date of your action items, either with or without time. Otherwise, the calendar will not consider it as an action item and won't input it in your calendar system. 
```

**Example Meeting Notes Input:**
```
PRODUCT LAUNCH PLANNING MEETING
Date: November 24, 2025
Attendees: Sarah Chen (Product Manager), Mike Rodriguez (Engineering Lead), Lisa Wang (Marketing Director), James Taylor (QA Manager), Emma Davis (Design Lead)

AGENDA:
1. Q1 2026 Product Launch Timeline
2. Resource Allocation
3. Marketing Campaign Strategy
4. Quality Assurance Protocol

DISCUSSION NOTES:

Sarah opened the meeting by emphasizing the critical importance of our Q1 2026 product launch. The new AI-powered analytics dashboard must be ready for public release by March 15, 2026. We're currently 60% through development, but several key milestones remain.

Mike presented the technical roadmap. The backend API integration is currently blocked due to third-party vendor delays. He stressed that we need to finalize the database schema by December 10, 2025, to stay on track. The team also needs to complete security penetration testing by January 20, 2026, before we can proceed to beta testing.

Lisa discussed the marketing strategy. She proposed a soft launch campaign starting February 1, 2026, targeting our existing enterprise clients first. The marketing assets including video demos, case studies, and landing pages must be completed by January 15, 2026. She also mentioned we need approval from legal for all marketing materials by January 25, 2026.

James raised concerns about the QA timeline. He emphasized that the team needs at least 3 weeks for comprehensive testing. The QA test plan must be approved by December 20, 2025. He also requested a dedicated staging environment by December 5, 2025, to begin preliminary testing.

Emma presented the new UI mockups which received positive feedback. However, she noted that final design assets need to be handed off to engineering by December 15, 2025. She also mentioned conducting user testing sessions with 10 beta customers in mid-January.

DECISIONS MADE:
- Launch date confirmed: March 15, 2026
- Weekly sync meetings every Monday at 10 AM starting December 2, 2025
- Budget approved for additional QA resources
- Marketing campaign budget increased by 20%

ACTION ITEMS:
- Mike to finalize database schema by December 10, 2025
- James to create and submit QA test plan by December 20, 2025
- James to request staging environment setup by December 5, 2025
- Emma to deliver final design assets by December 15, 2025
- Lisa to complete all marketing materials by January 15, 2026
- Lisa to obtain legal approval for marketing content by January 25, 2026
- Mike to complete security penetration testing by January 20, 2026
- Lisa to launch soft marketing campaign by February 1, 2026
- Team to conduct user testing sessions on January 15, 2026 at 2 PM
- Weekly sync meeting every Monday at 10 AM starting December 2, 2025
- Final launch review meeting on March 10, 2026 at 9 AM
- Post-launch retrospective meeting on March 20, 2026 at 3 PM

RISKS & CONCERNS:
- Third-party API vendor delays could impact timeline
- Limited QA resources for comprehensive testing
- Tight deadline for marketing material creation
- Dependency on legal approval process

```

The bot will:
1. Generate an AI summary
2. Extract all of the action items
3. Create calendar events automatically
4. Send a formatted response

---

## 🧩 **Workflow Components**

### **Triggers**

| Node | Frequency | Purpose |
|------|-----------|---------|
| **Every 3 Seconds** | 3s | Poll Telegram for new messages |
| **Daily Alerts** | 9 AM daily | Send TO-DO list reminder for 365 days (You can change this in the calendar node) |

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
Detect Long Message → Processing Alert → AI Summarize
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
| **AI Calendar** | OpenAI AI agent with Google Calendar tools |
| **Memory** | Stores conversation context per session |
| **Summarize Meeting** | GPT-4o-mini model for text summarization |
| **Extract Actions** | Smart extraction of action items from notes |
| **Parse Dates** | Detect dates & times, create appropriate event types |
| **Process Alerts** | Categorize events by urgency (overdue/urgent/soon/upcoming) |

---

## 🛠️ **Customization**

### **Change Daily Reminder Time**

1. Find node: **"⏰ TRIGGER: Daily Alerts"**
2. Change `triggerAtHour: 9` to your preferred hour (0-23)
3. Save workflow

### **Change Total Number of Days on the TO DO LIST You Want to Display**
1. Find node: **"📆 Get Upcoming Events"** after the **"⏰ TRIGGER: Daily Alerts"** node
2. Change `Before: {{$now.plus({day: 365})}}` to `Before: {{$now.plus({day: YOUR PREFFERED AMOUNT OF DAYS})}}`  

### **Change Polling Frequency**

1. Find node: **"Every 3 Seconds"**
2. Change `secondsInterval: 3` to the desired seconds
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
4. Bot is added to your channel

**Debug:**
- Manually execute "Every 3 Seconds" node
- Check execution logs for errors

---

### **Issue: You want to send the previous input, but it cannot be run**

**Solution:**
1. Deactivate workflow
2. Find the **"Remove Duplicates"** node and delete it
3. Create a new **"Remove Duplicates"** with the same settings as the previously **"Remove Duplicates"** deleted node:
   ```
   Operation: Remove Items Processed in Previous Executions
   Keep Items Where: Value is New
   Value to Dedupe on: {{ $json.messageText }}
   ```
5. Reactivate workflow

---

## 📊 **Performance & Limits**

| Component | Limit | Notes |
|-----------|-------|-------|
| Calendar Events | No limit | Google Calendar API quota |
| Message Length | 4096 characters | Telegram limit |
| ConvertAPI | 250 Conversion for first-time user and 25/month onwards | ConvertAPI Maximum Conversion |


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

### **Contact**

For workflow-specific questions, contact the original developer or refer to this documentation.

---

## 📝 **Model License**
This workflow is provided as-is for educational and personal use.
```
System Name: Intelligent Calendar Assistant with AI-Powered Meeting Summarization
Version: 2.0
Release Date: November 28, 2025
Developed By: Gerald Benedict Setiawan, Liu Qingyuan Midhat Mowla Siddique 
Course: CCAI9024 - AI Workflow Design Assignment
Institution: The University of Hong Kong
Platform: n8n Workflow Automation

AI Model Used:
1. OpenAI GPT-4o-mini

    Provider: OpenAI
    Version: Latest 
    Purpose: Meeting notes summarization and as an AI Calendar
    Task Type: Text generation, summarization, daily conversation regarding calendar management.
    Context Window: 128K tokens
    Temperature: 0.3 (low for consistency)
    Max Tokens: 1500
    
    Capabilities:
      - Natural language understanding
      - Text summarization
      - Structured output generation
      - Markdown formatting
    
    Limitations:
      - May occasionally miss nuanced context
      - Cannot access real-time information
      - Prone to hallucination if not constrained
      - Limited to text-based input

Credentials Used:
1. OpenAI API:
  Terms of Service: https://openai.com/policies/terms-of-use
  Usage Policy: https://openai.com/policies/usage-policies
  Privacy Policy: https://openai.com/policies/privacy-policy
  Note: Usage subject to OpenAI's pricing and rate limits

2. Google Cloud OAuth2:
  Terms of Service: https://cloud.google.com/terms
  Privacy Policy: https://policies.google.com/privacy
  Note: Subject to Google Cloud Platform terms and quotas

3. Telegram Bot API:
  Terms of Service: https://telegram.org/tos
  Bot API Terms: https://core.telegram.org/bots/faq#what-do-i-need-to-know-about-the-bot-api
  Privacy Policy: https://telegram.org/privacy
  Note: Free to use; subject to rate limits

4. ConvertAPI:
  Terms of Service: https://www.convertapi.com/terms
  Privacy Policy: https://www.convertapi.com/privacy-policy
  Note: Free tier available; paid usage subject to pricing
```

---

## 📝 **MIT License**
```
Copyright (c) 2025 Gerald Benedict Setiawan, Midhat Mowla Siddique, Liu Qingyuan

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

**Last Updated:** November 29, 2025  
**n8n Version:** 1.121.3  
**Author:** Gerald Benedict Setiawan
