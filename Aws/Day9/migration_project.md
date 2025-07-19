 **MySQL to MySQL database migration using AWS Database Migration Service (DMS)**. This includes:

---
<img width="1076" height="474" alt="image" src="https://github.com/user-attachments/assets/2a5375dd-ec8f-4cee-bead-2b289031c935" />


## ✅ Scenario

**Source DB**: MySQL (running on EC2 or RDS)

## ✅ Sample Table and Data

1. Launch a new EC2 instance (Amazon Linux 2)
2. Connect using SSH
3. Install MySQL client:

sudo dnf install mariadb105

4. Connect to RDS:

   ```bash
   mysql -h < source RDS-endpoint> -u admin -p
   ```


```sql
-- Create DB
CREATE DATABASE companydb;

-- Use DB
USE companydb;

-- Create Table
CREATE TABLE employees (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  department VARCHAR(50),
  salary INT
);

-- Insert 10 Records
INSERT INTO employees VALUES (1, 'Alice', 'IT', 60000);
INSERT INTO employees VALUES (2, 'Bob', 'Finance', 55000);
INSERT INTO employees VALUES (3, 'Charlie', 'HR', 52000);
INSERT INTO employees VALUES (4, 'David', 'IT', 61000);
INSERT INTO employees VALUES (5, 'Eva', 'Sales', 50000);
INSERT INTO employees VALUES (6, 'Frank', 'Finance', 54000);
INSERT INTO employees VALUES (7, 'Grace', 'HR', 53000);
INSERT INTO employees VALUES (8, 'Helen', 'Sales', 51000);
INSERT INTO employees VALUES (9, 'Ian', 'IT', 62000);
INSERT INTO employees VALUES (10, 'Jack', 'Marketing', 48000);
```

---

```

**Target DB**: MySQL (running on RDS or EC2)

Add inbound rule in 2 security groups sourcedb sg and target db sg with default vpc cidr for port 3306


<img width="1261" height="226" alt="image" src="https://github.com/user-attachments/assets/b874d6c0-80d0-4ecc-884f-d88d80305049" />


---

## ✅ Prerequisites

* Source and Target MySQL databases created.
* Tables and data present in source DB.
* A replication user created in source DB with necessary privileges.
* VPC/subnet access between DMS and DBs.
* AWS DMS service role: `dms-vpc-role`

---

## ✅ Step-by-Step Guide

---

### **Step 1: Create a Replication Instance (Controller)**

1. Go to **AWS DMS Console** → **Replication instances** → `Create replication instance`.
2. Provide:

   * **Name**: `mysql-to-mysql-repl`
   * **Instance class**: `dms.t3.micro` (for demo)
   * **VPC**: Choose the same VPC as DBs.
   * **Multi-AZ**: No (for testing/demo)
3. Allocate **subnet group** with access to both source and target DB.
4. Leave default settings and click **Create**.

---

### **Step 2: Create Source and Target Endpoints**

> 🔐 Enable **manual authentication** by entering credentials directly.

#### a) Source Endpoint:

1. Go to **Endpoints** → `Create endpoint`
2. Choose:

   * **Endpoint type**: Source
   * **Endpoint identifier**: `mysql-source`
   * **Source engine**: `MySQL`
   * **Server name**: `<EC2/RDS source endpoint>`
   * **Port**: `3306`
   * **Username**: `admin`
   * **Password**: `your_password`
3. Select **VPC**, **Replication instance**, and **subnet group**.
4. Optional: Test connection.

#### b) Target Endpoint:

1. Same steps as above, but:

   * **Endpoint type**: Target
   * **Endpoint identifier**: `mysql-target`
   * **Server name**: `<RDS endpoint or EC2>`
   * **Username/Password**: MySQL target credentials

✅ Now you have source and target endpoints.

---

### **Step 3: Create a Migration Task**

1. Go to **Database migration tasks** → `Create task`
2. Configure:

   * **Task name**: `mysql-migration-task`
   * **Replication instance**: choose previously created instance
   * **Source/Target endpoints**: select accordingly
   * **Migration type**:

     * `Migrate existing data` (for one-time)

3. **Task settings**:
 Leave as Default

4. Table mappings:

     * Use wizard  and Add new selection rule.
     * Example: Migrate all tables in `companydb` DB:

Selection rules
where schema name is like 'companydb' and Source table name is like '%', include
Transformation rules - 
where schema name is like 'companydb' and Source table name is like '', convert-lowercase

<img width="947" height="686" alt="image" src="https://github.com/user-attachments/assets/a7ad8475-0ba9-4d02-9867-0aa443ae7382" />

Transformation rule.

<img width="981" height="665" alt="image" src="https://github.com/user-attachments/assets/4289ea9b-9ba4-409c-87db-3795c5aadc36" />

Then

disable pre migration assesment

5. Click **Create task and start**.

---

### ✅ Step 4: Monitor Migration

* Go to **Tasks → View** status.
* Monitor logs and progress.
* On success, go to target DB and verify records.

<img width="1591" height="351" alt="image" src="https://github.com/user-attachments/assets/031eb5b1-0670-44a0-97bf-5bc4ca7f07b4" />


---

## ✅ Post Migration

* Validate data in target DB.
* If using ongoing replication, monitor for changes.
* Stop task once completed if one-time.


<img width="1591" height="351" alt="image" src="https://github.com/user-attachments/assets/cda34f89-c5da-47f1-9e11-59eeaf61e95c" />
