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

```
ssh -i test.pem ubuntu@<EC2_PUBLIC_IP>
```
## 🧩 PART 3 — CONNECT FROM EC2 & SET UP DATABASE

### 🔹 STEP 12 — Install MySQL Client (If Needed)

On the EC2 instance (Ubuntu):

## 🧩 PART 3 — CONNECT FROM EC2 & SET UP DATABASE

### 🔹 STEP 12 — Install MySQL Client (If Needed)

On the EC2 instance (Ubuntu):

```
sudo apt update -y
sudo apt install mysql-client -y
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
Successful connection shows:
```
mysql>
```
🧩 PART 4 — CREATE DATABASE & TABLES
🔹 STEP 15 — Create Database
```
CREATE DATABASE ebdb;
SHOW DATABASES;
```
🔹 STEP 16 — Use Database
```
USE ebdb;
```
🔹 STEP 17 — Create Table
```
CREATE TABLE IF NOT EXISTS books (
  book_id INT auto_increment,
  title VARCHAR(250) NOT NULL,
  author VARCHAR(50),
  price FLOAT,
  qty INT,
  PRIMARY KEY (book_id)
) ENGINE=InnoDB;

```

Verify table creation:
```
SHOW TABLES;
```
🔹 STEP 18 — Insert Sample Data
```
INSERT INTO books VALUES (1100, 'Chamber of Secrets', 'Rowling', 11.11, 4);
INSERT INTO books VALUES (1103, 'Philosophers Stone', 'Rowling', 10.90, 8);
INSERT INTO books VALUES (1105, 'War and Peace', 'Tolstoy', 22.22, 2);
INSERT INTO books VALUES (1107, 'Romeo and Juliet', 'Shakespear', 33.33, 5);
INSERT INTO books VALUES (1109, 'Othallo', 'Shakespear', 13.99, 7);
INSERT INTO books VALUES (1111, 'Death on the Nile', 'Agatha', 44.40, 15);
INSERT INTO books VALUES (1113, 'ABC Murders', 'Agatha', 39.40, 11);
INSERT INTO books VALUES (1115, 'Anna Kareneena', 'Tolstoy', 55.55, 23);
```
🔹 STEP 19 — Verify Data
```
SELECT * FROM books;
```
🔹 STEP 20 — Exit MySQL
```
exit;
```
## 🧩 PART 5 — CONNECT ELASTIC BEANSTALK TO RDS

In this section, we configure **Elastic Beanstalk environment variables** so that the application can connect to the Aurora MySQL database.

---

### 🔹 STEP 21 — Open Elastic Beanstalk Environment

1. Go to **AWS Console**
2. Search for **Elastic Beanstalk**
3. Click **Applications**
4. Select your application  
   Example: book-seller-demo

5. Click **Environments**
6. Click your environment  
Example: Book-seller-demo-env

---

### 🔹 STEP 22 — Open Configuration

1. Inside the environment page, click **Configuration**
2. Locate the **Software** configuration section
3. Click **Edit**

---

### 🔹 STEP 23 — Add Environment Properties

Scroll down to **Environment properties**

Add the following **Key / Value** pairs.

⚠️ **Source must be set to `Plain text` for all entries**

| Source | Key | Value |
|------|-----|------|
| Plain text | RDS_USERNAME | admin |
| Plain text | RDS_PASSWORD | test1234 |
| Plain text | RDS_DB_NAME | ebdb |
| Plain text | RDS_PORT | 3306 |
| Plain text | RDS_HOSTNAME | demo-book-seller.cluster-xxxx.us-east-1.rds.amazonaws.com |

📌 Notes:
- Use the **Writer endpoint** only
- Variable names are **case-sensitive**
- Do **not** add quotes

---

### 🔹 STEP 24 — Apply Configuration Changes

1. Click **Apply**
2. Elastic Beanstalk will start updating the environment
3. Wait **1–3 minutes**

Environment status will change:

