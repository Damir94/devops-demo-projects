## Set Up MySQL RDS and DB Access for WordPress

- This is a continuation of the tutorial “Deploy WordPress Docker Container on ECS”
- In this tutorial, you will learn how to set up a dedicated security group to control access, configure a DB subnet group to enhance network performance, and securely connect your WordPress app on ECS by updating the task definition with required environment variables.
- By the end, you will have a fully functional, secure backend supporting your WordPress deployment!

<img width="657" height="306" alt="Screenshot 2026-10-03 at 4 56 09 PM" src="https://github.com/user-attachments/assets/92b1ef11-b4ee-40e3-8023-dc66e5203d0d" />

- we will create and configure a MySQL database in Amazon RDS tailored for WordPress, restricting access solely to our ECS and ALB services.
- Before we start creating a new security group for the RDS database. Let is quickly review this security group we previously setup called “wp-sg”.
- Go to the security group
