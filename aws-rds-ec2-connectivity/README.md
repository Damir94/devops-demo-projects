### Amazon RDS (MySQL) Configuration and EC2 Connectivity


<img width="1706" height="922" alt="ChatGPT Image Sep 22, 2026, 10_27_30 AM" src="https://github.com/user-attachments/assets/b83e9c10-9c2d-4672-b662-996c5172101a" />

## Objective
The objective of this project is to deploy an Amazon RDS MySQL database, securely connect it with an Amazon EC2 instance, and perform database operations using SQL commands. This project demonstrates secure cloud database deployment without exposing the database directly to the internet.

## Introduction
Amazon Relational Database Service (Amazon RDS) is a managed database service that simplifies the creation, operation, and maintenance of relational databases in AWS. Instead of manually installing MySQL on a virtual machine, Amazon RDS automatically manages database installation, backups, software updates, monitoring, and storage. In this project, an Amazon EC2 instance was configured as a database client, which securely connected to the MySQL database hosted on Amazon RDS.

## AWS Services Used
- Amazon EC2
- Amazon RDS (MySQL)
- Amazon VPC
- DB Subnet Group
- Security Groups
- MariaDB Client
- SQL

## Implementation Steps

## Step 1: Create a DB Subnet Group
- A DB subnet group was created by selecting two subnets located in different Availability Zones inside the VPC. This enables the RDS instance to operate securely within the VPC.

<img width="1888" height="460" alt="Screenshot 2026-09-22 at 10 32 40 AM" src="https://github.com/user-attachments/assets/05435365-3a9b-407b-af32-e0ca39973d24" />

## Step 2: Create Security Group
A new security group named RDS-SG was created.
- Inbound Rule:
  - MySQL/Aurora
  - Port 3306
  - Source: EC2 Security Group
- This allows only the EC2 instance to communicate with the database.

<img width="1598" height="243" alt="Screenshot 2026-09-22 at 10 37 27 AM" src="https://github.com/user-attachments/assets/514b5935-4840-4643-be66-84ab17663339" />

## Step 3: Create Amazon RDS Instance
- A MySQL database instance named student-db was created using the Amazon RDS service.
- Configuration included:
  - MySQL Engine
  - Single AZ Deployment
  - Free Tier
  - Private Access
  - DB Subnet Group
  - Security Group
  - Master Username and Password

  <img width="1548" height="239" alt="Screenshot 2026-09-22 at 10 51 52 AM" src="https://github.com/user-attachments/assets/57bef8ad-a40a-43f0-bc3b-082f4ad22fbe" />

### Step 4: Launch EC2 Instance
- An Amazon Linux EC2 instance named RDS-EC2 was launched inside the same VPC.
- The MariaDB client package was installed using the package manager.

<img width="1612" height="228" alt="Screenshot 2026-09-22 at 10 55 24 AM" src="https://github.com/user-attachments/assets/d85a08f3-578f-4d4f-8634-ff65ea1a3e65" />

```bash
  sudo dnf install mariadb105
```

<img width="1218" height="334" alt="Screenshot 2026-09-22 at 10 59 51 AM" src="https://github.com/user-attachments/assets/d3c2c895-2c3c-479f-85f2-65274142334b" />

### Step 5: Connect EC2 to Amazon RDS
- Using the RDS endpoint, the EC2 instance established a secure connection to the MySQL server.

```bash
mysql -h <RDS-ENDPOINT> -P 3306 -u admin -p
```
- After authentication, the MariaDB prompt appeared successfully.

## Step 6: Execute SQL Commands
- After connecting to the database, SQL commands were executed.
- Commands performed:
```bash
CREATE DATABASE studentdb;
```
```bash
USE studentdb;
```
```bash
CREATE TABLE students(
id INT PRIMARY KEY,
name VARCHAR(50)
);
```
```bash
INSERT INTO students VALUES(1,'Satya');
```
```bash
SELECT * FROM students;
```
- The output confirmed successful database creation, table creation, data insertion, and retrieval.

<img width="925" height="740" alt="Screenshot 2026-09-22 at 11 15 06 AM" src="https://github.com/user-attachments/assets/379ac01f-1383-4bb6-9059-e9fb167e60bf" />

### Results
- The project was completed successfully.
- The EC2 instance established a secure connection with the Amazon RDS MySQL database.
- Database objects were created successfully, sample records were inserted, and SQL queries returned the expected results.
