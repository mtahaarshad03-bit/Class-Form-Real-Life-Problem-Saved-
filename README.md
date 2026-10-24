# BS AI - UI/UX Problem Submission Portal & Automation Workflow

An automated submission and tracking system built for the **BS AI Course** under the supervision of **Mr. Dahir Zaib**. Developed by **Muhammad Taha**, this portal collects student UI/UX problem submissions via a frontend form, processes them asynchronously using **n8n**, stores structured multi-row data in **Google Sheets**, applies automated visual grouping using **Google Apps Script**, and sends instant dual notification emails via **Gmail**.

---

## 📌 Project Overview

* **Course:** BS Artificial Intelligence (BS AI)
* **Developer:** Muhammad Taha
* **Instructor:** Mr. Dahir Zaib
* **Purpose:** Collect 3 distinct real-world UI/UX problem statements and their impact frequencies from 56 students.
* **Tech Stack:**
  * **Frontend:** HTML5, Tailwind CSS
  * **Automation Engine:** n8n Workflow Automation
  * **Database / Output:** Google Sheets
  * **Email Delivery:** Gmail Nodes (n8n API Integration)
  * **Formatting Script:** Google Apps Script (JavaScript)

---

## 👨‍💻 Author & Credits

* **Developer:** Muhammad Taha
* **Course Supervisor:** Mr. Dahir Zaib
* **Program:** BS Artificial Intelligence (BS AI)

---

## ⚙️ System Architecture & Workflow

```text
                           ┌──► Gmail Node (Student Email Confirmation)
                           │
[ HTML/Tailwind Form ] ────┼──► Gmail Node (Instructor Notification - Mr. Dahir Zaib)
  (Webhook Trigger)        │
                           └──► [ Split Out Node ] ──► [ Google Sheets (Append Row) ]
                                                            │
                                                            ▼
                                                   [ Apps Script Trigger ]
                                                  (Auto-Merge & Group Color)
```

---

## 🚀 Key Features

1. **Structured Data Splitting:** Converts a single student form submission containing an array of 3 problems into 3 distinct rows in Google Sheets using n8n's `Split Out` node.
2. **Dual-Channel Gmail Notifications:** Sends non-repeating confirmation emails to both the submitting student and the course instructor.
3. **Automated Visual Grouping (Apps Script):**
   * Merges `Name`, `Roll No`, and `Email` cells vertically for every student's 3-row submission.
   * Vertically centers student information across their 3 rows.
   * Applies alternating pastel group background colors to distinguish submissions cleanly.

---

## 🛠️ Step-by-Step Setup Guide

### 1. Webhook Payload Structure
The frontend form sends the following JSON payload to the n8n Webhook node:

```json
{
  "student": {
    "fullName": "Student Name",
    "rollNumber": "BSAI-F23-XXX",
    "email": "student@gmail.com"
  },
  "problems": [
    {
      "number": 1,
      "problemStatement": "Description of UI/UX issue 1",
      "impactFrequency": "Daily"
    },
    {
      "number": 2,
      "problemStatement": "Description of UI/UX issue 2",
      "impactFrequency": "Weekly"
    },
    {
      "number": 3,
      "problemStatement": "Description of UI/UX issue 3",
      "impactFrequency": "Monthly"
    }
  ]
}
```

---

### 2. n8n Node Configuration

* **Webhook Node:** Accepts `POST` requests from the frontend HTML form.
* **Gmail Nodes:** Connected directly to the Webhook node (before splitting) to ensure only 1 email is sent per submission:
  * **To Student:** `{{ $('Webhook').item.json.body.student.email }}`
  * **To Instructor:** `dahir.zaib@...`
* **Split Out Node:**
  * **Field to Split Out:** `body.problems`
  * **Include:** `All Other Fields`
* **Google Sheets Node (Append Row):**
  * **Name:** `{{ $('Webhook').item.json.body.student.fullName }}`
  * **Roll No.:** `{{ $('Webhook').item.json.body.student.rollNumber }}`
  * **Email:** `{{ $('Webhook').item.json.body.student.email }}`
  * **Problem:** `{{ $json.problemStatement }}`
  * **Effect:** `{{ $json.impactFrequency }}`

---

### 3. Google Apps Script Integration
To enable auto-merging and group color-coding on the target Google Sheet:

1. In your Google Sheet, navigate to **Extensions ➔ Apps Script**.
2. Replace all existing code with the following script:

```javascript
function formatAndMergeStudentRows() {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  var lastRow = sheet.getLastRow();
  
  if (lastRow < 2) return;
  
  var colors = [
    "#FEF3C7", // Light Amber
    "#E0F2FE", // Light Blue
    "#F3E8FF", // Light Purple
    "#E0E7FF", // Light Indigo
    "#DCFCE7"  // Light Green
  ];
  
  var colorIndex = 0;
  
  for (var r = 2; r <= lastRow; r += 3) {
    var numRows = Math.min(3, lastRow - r + 1);
    
    for (var col = 1; col <= 3; col++) {
      var range = sheet.getRange(r, col, numRows, 1);
      if (numRows > 1) {
        range.merge();
      }
      range.setVerticalAlignment("middle");
      range.setHorizontalAlignment("center");
    }
    
    var studentBlock = sheet.getRange(r, 1, numRows, 5);
    studentBlock.setBackground(colors[colorIndex % colors.length]);
    studentBlock.setBorder(true, true, true, true, false, false, "#CBD5E1", SpreadsheetApp.BorderStyle.SOLID);
    
    colorIndex++;
  }
}
```

3. **Set up an automated trigger:**
   * Click **Triggers (⏰ Clock Icon)** on the left sidebar.
   * Click **+ Add Trigger**.
   * Select `formatAndMergeStudentRows`.
   * Set **Event Source** to `Time-driven` ➔ `Minutes timer` ➔ `Every minute` (or `Every 5 minutes`).

---

## 🧪 Testing the Portal

Use any of the following sample payloads in Postman or via your form to test end-to-end execution:

```json
{
  "student": {
    "fullName": "Zain Ali",
    "rollNumber": "BSAI-F23-089",
    "email": "zain.bsai23@gmail.com"
  },
  "problems": [
    {
      "number": 1,
      "problemStatement": "Campus Wi-Fi disconnects automatically every 15 minutes during lab classes.",
      "impactFrequency": "Daily"
    },
    {
      "number": 2,
      "problemStatement": "Library portal does not show availability status of AI reference books.",
      "impactFrequency": "Weekly"
    },
    {
      "number": 3,
      "problemStatement": "Student portal grade history tab takes too long to load on low-speed internet.",
      "impactFrequency": "Monthly"
    }
  ]
}
```

---

## 👥 Authors & Supervision

* **Developer:** Muhammad Taha
* **Course Supervisor:** Mr. Dahir Zaib
* **Program:** BS Artificial Intelligence
