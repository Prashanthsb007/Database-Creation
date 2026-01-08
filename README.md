# Database-Creation

# 📘 LAB: Create Aurora MySQL DB, Connect from EC2, Create Tables

## 🎯 OBJECTIVE

- Create an Amazon Aurora MySQL database
- Connect to it from EC2
- Create database and tables
- Insert and verify data
- Use this DB with Elastic Beanstalk

---

## 🧩 PART 1 — CREATE DATABASE (AURORA MYSQL)

### 🔹 STEP 1 — Open RDS Console

AWS Console → Search **RDS**  
Click **Aurora and RDS**  
Click **Databases**  
Click **Create database**

---

### 🔹 STEP 2 — Choose Database Creation Method

Select:

Easy create

---

### 🔹 STEP 3 — Configuration

Engine type: Aurora (MySQL Compatible)  
DB instance size: Dev/Test

---

### 🔹 STEP 4 — Settings

| Field | Value |
|------|------|
| DB cluster identifier | demo-book-seller |
| Master username | admin |
| Credentials management | Self managed |
| Master password | test1234 |
| Confirm password | test1234 |

Note: Simple password is used only for demo/learning.

---

### 🔹 STEP 5 — Instance Configuration

| Field | Value |
|------|------|
| DB instance class | db.t3.small (or smallest available) |
| Aurora replicas | No |

---

### 🔹 STEP 6 — Connectivity

| Field | Value |
|------|------|
| Public access | Yes |
| VPC | Default VPC |
| Security group | Default (edit later) |

Leave all other options as default.

---

### 🔹 STEP 7 — Create Database

Click **Create database**

Wait 5–10 minutes.

---

### 🔹 STEP 8 — Verify Status

Go to **Databases**

You should see two entries:

- demo-book-seller (Cluster)
- demo-book-seller-instance-1 (Writer instance)

Both must show **Status: Available**

---

## 🧩 PART 2 — FIX NETWORK ACCESS (VERY IMPORTANT)

### 🔹 STEP 9 — Open Security Group

Click **demo-book-seller-instance-1**  
Go to **Connectivity & Security**  
Click the **VPC security group**

---

### 🔹 STEP 10 — Edit Inbound Rules

Add the following rule:

| Type | Protocol | Port | Source |
|------|----------|------|--------|
| MySQL/Aurora | TCP | 3306 | 0.0.0.0/0 |

Note: This is for demo/learning only.

Click **Save rules**  
Wait 30–60 seconds.

---

## 🧩 PART 3 — CONNECT FROM EC2

### 🔹 STEP 11 — Login to EC2

```bash
ssh -i test.pem ubuntu@<EC2_PUBLIC_IP>

## 🧩 PART 3 — CONNECT FROM EC2 & SET UP DATABASE

### 🔹 STEP 12 — Install MySQL Client (If Needed)

On the EC2 instance (Ubuntu):

## 🧩 PART 3 — CONNECT FROM EC2 & SET UP DATABASE

### 🔹 STEP 12 — Install MySQL Client (If Needed)

On the EC2 instance (Ubuntu):

```
sudo apt update -y
sudo apt install mysql-client-core-8.0 -y
```
🔹 STEP 13 — Get RDS Endpoint
Go to AWS Console → RDS → Databases
Click demo-book-seller
Copy the Writer endpoint
example:
```
demo-book-seller.cluster-xxxx.us-east-1.rds.amazonaws.com
```
🔹 STEP 14 — Connect to Database
Run the following command from EC2:
```
mysql -h demo-book-seller.cluster-xxxx.us-east-1.rds.amazonaws.com -P 3306 -u admin -p
```
Enter password:
```
test1234
```

