# KOKALE Lead Capture System - Complete Workflow

## 📋 System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    KOKALE Website                           │
│              (Frontend - index.html)                         │
│         - Lead Form Modal                                   │
│         - Form Validation                                   │
└────────────────────┬────────────────────────────────────────┘
                     │ (Form Submission)
                     ▼
┌─────────────────────────────────────────────────────────────┐
│          Node.js Backend Server (server.js)                 │
│         - Receive form data                                 │
│         - Validate input                                    │
│         - Save to temporary storage                         │
│         - Trigger Python script                             │
└────────────────────┬────────────────────────────────────────┘
                     │ (API Call)
                     ▼
┌─────────────────────────────────────────────────────────────┐
│        Python Script (google_integration.py)                │
│  - Authenticate with Google API                             │
│  - Append data to Google Sheets                             │
│  - Send confirmation email via Gmail                        │
│  - Handle errors & retries                                 │
└────────────────────┬────────────────────────────────────────┘
                     │
        ┌────────────┼────────────┐
        ▼            ▼            ▼
   ┌─────────┐ ┌──────────┐ ┌──────────┐
   │ Google  │ │  Gmail   │ │  Google  │
   │ Drive   │ │  Emails  │ │  Drive   │
   └─────────┘ └──────────┘ │(Backup)  │
                             └──────────┘
```

---

## 🔄 Complete Workflow Steps

### **Step 1: User Submits Form**
- User fills KOKALE website form
- Frontend validates data
- Form POSTs to Node.js server
- Data: `{ name, email, phone, intent, requirement }`

### **Step 2: Node.js Receives & Validates**
- Server receives POST request
- Validates all fields
- Checks email format
- Stores in local buffer
- Returns success/error response

### **Step 3: Node.js Triggers Python Script**
- Node.js calls Python script via child_process
- Passes form data as JSON
- Waits for response

### **Step 4: Python Connects to Google**
- Authenticates with Google API using credentials
- Appends row to Google Sheets
- Sends confirmation email to user
- Sends notification to sales team
- Logs activity

### **Step 5: Data Stored in Google Sheets**
```
Columns: Date | Name | Email | Phone | Intent | Requirement | Status
Row 1:  2026-08-15 | Raj Kumar | raj@company.com | +91-98928-10945 | AWS migration | Need cloud assessment | New
Row 2:  2026-08-15 | Priya Singh | priya@tech.com | +91-99876-54321 | Autodesk | CAD software inquiry | New
```

### **Step 6: Confirmation Emails Sent**
- **To Customer**: "Thank you for your enquiry. We'll contact you soon."
- **To Sales Team**: "New lead received: [Name] - [Intent]"

### **Step 7: Error Handling**
- If Google API fails → retry 3 times
- If email fails → log & continue
- Send error notification to admin email

---

## 🗂️ File Structure

```
kokale-backend/
├── server.js                    # Node.js main server
├── google_integration.py         # Python Google API integration
├── requirements.txt              # Python dependencies
├── config.json                   # Configuration file
├── credentials.json              # Google API credentials (GITIGNORE)
├── .env                          # Environment variables (GITIGNORE)
├── .gitignore                    # Git ignore file
├── leads.json                    # Temporary lead storage (backup)
├── logs/
│   └── leads.log                # Activity logs
├── README.md                     # Setup instructions
└── package.json                  # Node.js dependencies
```

---

## 🔐 Data Flow Security

1. **Frontend → Node.js**: HTTPS only, CORS protected
2. **Node.js → Python**: Local execution, no external calls
3. **Python → Google**: OAuth 2.0 authentication
4. **Google API**: Service account with limited permissions
5. **Email**: Gmail API with app-specific password

---

## 📊 Google Sheets Structure

```
Sheet Name: "Kokale_Leads"

Columns:
A: Timestamp          (2026-08-15 10:30:45)
B: Name               (Customer Name)
C: Email              (customer@email.com)
D: Phone              (+91 98928-10945)
E: Intent             (AWS migration)
F: Requirement        (Cloud assessment needed)
G: Status             (New/Contacted/Converted)
H: Follow_Up_Date     (Empty - for sales team)
I: Notes              (Empty - for sales team)
J: Source             (Website)
```

---

## ⚙️ Tech Stack

| Component | Technology | Purpose |
|-----------|-----------|---------|
| Frontend | HTML/CSS/JS | User interface |
| Backend API | Node.js + Express | Handle requests |
| Google Integration | Python 3 | Google API calls |
| Database | Google Sheets | Lead storage |
| Email | Gmail API | Notifications |
| Hosting | GitHub + Render/Heroku | Free hosting |
| Authentication | OAuth 2.0 | Google API auth |

---

## 🚀 Deployment Flow

```
1. Create Google Service Account
   ↓
2. Download credentials.json
   ↓
3. Create Google Sheet
   ↓
4. Set up Node.js server locally
   ↓
5. Test locally
   ↓
6. Deploy to Render/Heroku
   ↓
7. Update frontend to point to live server
   ↓
8. Test form submission
   ↓
9. Monitor logs & emails
```

---

## 📱 API Endpoints

### **POST /api/leads**
- **Request**: Form data (name, email, phone, intent, requirement)
- **Response**: `{ success: true, message: "Lead saved" }`
- **Trigger**: Python script to save to Google Sheets

### **GET /api/health**
- **Response**: Server status check
- **Purpose**: Verify server is running

---

## 🔑 Environment Variables Needed

```
# Google API
GOOGLE_CREDENTIALS_PATH=./credentials.json
GOOGLE_SHEET_ID=your-spreadsheet-id
GOOGLE_SHEET_NAME=Kokale_Leads

# Gmail
GMAIL_SENDER=kokale1977@gmail.com
SALES_EMAIL=sales@kokale.com

# Node.js
PORT=3000
NODE_ENV=production
```

---

## ⚡ Performance Metrics

- Form submission: < 2 seconds
- Google Sheets update: < 3 seconds
- Email delivery: < 5 seconds
- Total request time: ~ 5-8 seconds

---

## 🛡️ Error Handling

| Error | Handling |
|-------|----------|
| Invalid email | Return 400 error |
| Google API timeout | Retry 3x, then queue |
| Gmail service down | Log & send via backup |
| Network issue | Queue & retry later |
| Duplicate submission | Check email + timestamp |

---

## ✅ Testing Checklist

- [ ] Form validates on frontend
- [ ] Node.js receives POST request
- [ ] Data saves to Google Sheets
- [ ] Confirmation email sent
- [ ] Sales notification email sent
- [ ] Error handling works
- [ ] Logs are created
- [ ] Rate limiting works
- [ ] CORS is configured
- [ ] Environment variables loaded

---

## 🎯 Next: CODE IMPLEMENTATION

Ready for the actual code? I'll provide:
1. **Node.js server code** (server.js)
2. **Python integration code** (google_integration.py)
3. **Updated frontend code** (index.html)
4. **Configuration files** (.env, config.json)
5. **Setup instructions** (README.md)
6. **Deployment guide**

---

**Approve this workflow, and I'll create all the code files!**
