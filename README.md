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

# 🛠️ Tools & Services Used
```text
┌────────────────────────────────────────────────────────────────────────────────┐
| Service	        | Purpose
──────────────────────────────────────────────────────────────────────────────────
| AWS RDS	        | Managed MySQL database (stores the actual data)
| AWS EC2	        | Bastion Host (secure bridge between internet and database)
| Security Groups |	Firewalls that control who can talk to what
| SSH Tunnel	    | Encrypted connection from my Kali laptop through EC2 to RDS
| MySQL Client	  | Command-line tool to run SQL commands
└─────────────────────────────────────────────────────────────────────────────────┘
```
---

# 📋 Step-by-Step: What I Actually Did
### Step 1: Set Up the Cloud Infrastructure (AWS)
Task: Provision a database and a secure way to access it.

* Created RDS instance with MySQL	Managed database – AWS handles backups and patching
* Placed RDS in a private subnet – No direct internet access
* Created EC2 instance as Bastion Host – A public entry point that can reach the private database
* Configured Security Groups	Firewall rules: only EC2 can talk to RDS on port 3306
* The Struggle: I initially didn't configure the Security Group correctly. My EC2 couldn't talk to RDS. I spent time troubleshooting and eventually rebuilt both instances with the correct network rules.

** Lesson learned: Always double-check Security Group inbound rules. The source should be the EC2 Security Group ID, not your IP address.

