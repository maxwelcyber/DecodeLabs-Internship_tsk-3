# 📌 Project Overview
An e‑commerce company was struggling with Excel spreadsheets to manage customer data. They needed a scalable, secure, and persistent cloud database solution.

My mission: Set up a managed database on AWS, create a structured table for employee records, insert test data, and verify everything worked.

---

## 🧠 What I Built (The Simple Version)
I built a secure cloud database that can replace an Excel spreadsheet. Instead of storing customer info in a file that can crash or get lost, I stored it in a professional database that:

* Lives in the cloud (accessible anywhere)

* Is backed up automatically

* Has rules to prevent bad data (like duplicate emails)

* Is protected from hackers

---

# 🏗️ Architecture Diagram (Visual Thinkers)
```text
┌─────────────────────────────────────────────────────────┐
│                                                         │
│   [My Kali PC]                                          │
│         │                                               │
│         │ 1. SSH into EC2 (18.213.3.38)                 │
│         │ 2. Tunnel forwards port 3306                  │
│         ▼                                               │
│   [EC2 Bastion Host]  ← Public Subnet                   │
│         │                                               │
│         │ Private network (only EC2 can reach RDS)      │
│         ▼                                               │
│   [RDS Database]      ← Private Subnet                  │
│         │                                               │
│         │ MySQL engine, port 3306                       │
│         ▼                                               │
│   [Interns Table]  ← Created with SQL                   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```
---
