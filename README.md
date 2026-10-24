# BS AI UI/UX Problem Submission Portal 🚀

An automated system designed for collecting, processing, and recording UI/UX problem submissions from BS AI students. Built with an HTML/Tailwind CSS frontend, n8n webhook automation, Google Sheets integration, and automated Gmail notifications.

---

## 👨‍💻 Author & Developer
* **Developer:** Muhammad Taha
* **Instructor / Reviewer:** Mr. Dahir Zaib
* **Course:** BS Artificial Intelligence (BS AI)

---

## 🏗 System Architecture

The workflow automates student submission through a seamlessly connected pipeline:

```text
[ HTML / Tailwind Frontend ]
            │
            ▼ (JSON Payload)
     [ n8n Webhook ]
            │
            ├───────────────► [ Gmail Node 1 ] ──► Confirmation to Student
            │
            ├───────────────► [ Gmail Node 2 ] ──► Notification to Instructor
            │
            ▼
    [ Split Out Node ]
            │
            ▼
 [ Google Sheets Node ] ──► Appends 3 distinct rows per submission
