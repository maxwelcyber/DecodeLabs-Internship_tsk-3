# 📌 Project Overview
An e‑commerce company was struggling with Excel spreadsheets to manage customer data. They needed a scalable, secure, and persistent cloud database solution.

My mission: Set up a managed database on AWS, create a structured table for employee records, insert test data, and verify everything worked.

## 📐 Infrastructure Specifications
Cloud Service Provider: AWS RDS
Operating System Platform: Aamazon Linux
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

#### The Struggle: I initially didn't configure the Security Group correctly. My EC2 couldn't talk to RDS. I spent time troubleshooting and eventually rebuilt both instances with the correct network rules.

#### Lesson learned: Always double-check Security Group inbound rules. The source should be the EC2 Security Group ID, not your IP address.


### 📋 Step 2: Connecting Through the SSH Tunnel
* Why this was necessary:
RDS was in a private subnet, so I couldn't connect directly from my laptop. I had to go [ Laptop → EC2 → RDS. ]

The command:
```bash
ssh -i Documents/awsuser_key.pem -L 3306:decodelabs.cyvge68k46wi.us-east-1.rds.amazonaws.com:3306 ec2-user@18.213.3.38 -N
```

* ssh -i Documents/awsuser_key.pem: Connect to EC2 using my private key file
* -L 3306:rds-endpoint:3306__Port forwarding: Anything sent to my laptop's port 3306 gets forwarded to RDS's port 3306 through EC2
* ec2-user@18.213.3.38: The EC2 instance's public IP address
* "-N"	No remote commands – just forward the connection and stay open (this is why the terminal hangs/looks blank – it's working)

Without the -N flag, SSH would give me a shell on EC2.

#### The Struggle: I tried installing MySQL directly on EC2 and it failed spectacularly. Amazon Linux uses 'yum' but the package names were different.

#### Lesson learned: I didn't need MySQL on EC2 at all, EC2 is just a bridge. All the SQL work was be done from my local machine through the tunnel. The tunnel just forwards everything.


### 📋 Step 3: Connect with Local SQL Client
Once the tunnel was running, I connected from my Kali laptop.


```bash
mysql -h 127.0.0.1 -u admin -p
```
* Why 127.0.0.1? The tunnel forwards to localhost. So when I connect to 127.0.0.1:3306, it goes through the tunnel to RDS.


### 📋 Step 4: Write SQL to Create the Table
Now the fun part – which was actually building the database structure.

```sql
CREATE DATABASE decodelabs;
USE decodelabs;

CREATE TABLE Interns (
    InternID INT PRIMARY KEY,
    FirstName VARCHAR(50) NOT NULL,
    LastName VARCHAR(50) NOT NULL,
    Email VARCHAR(100) UNIQUE NOT NULL
);
```
#### 🧠 What each constraint does:

* PRIMARY KEY - Each InternID must be unique – no duplicates
* UNIQUE (on Email) - No two interns can have the same email address
* NOT NULL - You can't leave FirstName, LastName, or Email blank

#### Why this matters: In Excel, you can type anything (or nothing). In a database, these rules enforce data quality at the system level. The database won't accept bad data.


### 📋 Step 5: Insert Dummy Records & verify data persistence 
```sql
INSERT INTO Interns (InternID, FirstName, LastName, Email)
VALUES 
    (1, 'John', 'Doe', 'jdoe@decodelabs.com'),
    (2, 'Jane', 'Smith', 'jsmith@decodelabs.com'),
    (3, 'Conan', 'O''Leary', 'coleary@decodelabs.com');

SELECT * FROM Interns;
```
#### OUTPUT:-
```text
+----------+-----------+----------+------------------------+
| InternID | FirstName | LastName | Email                  |
+----------+-----------+----------+------------------------+
|        1 | John      | Doe      | jdoe@decodelabs.com    |
|        2 | Jane      | Smith    | jsmith@decodelabs.com  |
|        3 | Conan     | O'Leary  | coleary@decodelabs.com |
+----------+-----------+----------+------------------------+
3 rows in set (0.221 sec)
```
---

# ##Final Thoughts
This project forced me to understand:

* How cloud networking works (VPC, subnets, security groups)

* How to securely connect to private resources (Bastion Host + SSH tunnel)

* How to design a database schema (DDL with constraints)

* How to populate and verify data (DML with INSERT and SELECT)

## The biggest lesson: Cloud architecture isn't about memorizing commands. It's about understanding the why behind each component. Why a private subnet? Why a Bastion Host? Why an SSH tunnel? Every decision has a security or scalability reason.

