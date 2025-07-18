 **MySQL to MySQL database migration using AWS Database Migration Service (DMS)**. This includes:

---

## ✅ Scenario

**Source DB**: MySQL (running on EC2 or RDS)
**Target DB**: MySQL (running on RDS or EC2)

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
     * `Migrate existing data and replicate ongoing changes` (for CDC)
3. **Task settings**:

   * Enable logging.
   * Table mappings:

     * Use wizard or JSON to specify tables.
     * Example: Migrate all tables in `demo` DB:

       ```json
       {
         "rules": [
           {
             "rule-type": "selection",
             "rule-id": "1",
             "rule-name": "1",
             "object-locator": {
               "schema-name": "demo",
               "table-name": "%"
             },
             "rule-action": "include"
           }
         ]
       }
       ```
4. Click **Create task and start**.

---

### ✅ Step 4: Monitor Migration

* Go to **Tasks → View** status.
* Monitor logs and progress.
* On success, go to target DB and verify records.

---

## ✅ Additional Setup (Important)

### ✅ MySQL Source Permissions (Replication User):

Run in source MySQL:

```sql
CREATE USER 'dms_user'@'%' IDENTIFIED BY 'your_password';

GRANT SELECT, RELOAD, SUPER, REPLICATION CLIENT, REPLICATION SLAVE ON *.* TO 'dms_user'@'%';
FLUSH PRIVILEGES;
```

---

## ✅ Sample Table and Data

### Create sample table in source:

```sql
CREATE DATABASE demo;

USE demo;

CREATE TABLE students (
  id INT PRIMARY KEY,
  name VARCHAR(50),
  age INT,
  grade VARCHAR(5)
);

INSERT INTO students (id, name, age, grade) VALUES
(1, 'Alice', 20, 'A'),
(2, 'Bob', 21, 'B'),
(3, 'Charlie', 22, 'A'),
(4, 'David', 20, 'C'),
(5, 'Eva', 23, 'B'),
(6, 'Frank', 21, 'A'),
(7, 'Grace', 22, 'B'),
(8, 'Helen', 20, 'C'),
(9, 'Ian', 21, 'B'),
(10, 'Jane', 23, 'A');
```

---

## ✅ Post Migration

* Validate data in target DB.
* If using ongoing replication, monitor for changes.
* Stop task once completed if one-time.


