## Deploy WordPress Docker Container on ECS

- In this tutorial, we will walk through the process of hosting a WordPress site on Amazon ECS. While
some people might choose to set up their WordPress site on Virtual machines, we will be taking a
more advanced approach using Amazon ECS for a scalable, flexible and modern cloud environment.
- By the end of this tutorial, you will understand how to deploy your WordPress site on AWS ECS with
ease.

### STEP 1: Create a VPC
- Our first step is to create our Virtual Private Cloud which is our VPC, which will serve as the dedicated network for our WordPress deployment.
- So, we have to login to AWS Management console and search for VPC.

<img width="1375" height="466" alt="Screenshot 2026-10-03 at 10 48 50 AM" src="https://github.com/user-attachments/assets/c6fe7747-9425-4d6f-869f-69ac8a4747ed" />

- Click on “Create VPC

<img width="1222" height="342" alt="Screenshot 2026-10-03 at 10 50 40 AM" src="https://github.com/user-attachments/assets/101fe670-eaf2-4a04-8dc8-aa31adadc2cf" />

- In “VPC Settings”, we will use “VPC and More”
- On “Name tag Auto-generation”, we will give a name. Let us call it “demo-VPC”
- Then on “IPv4 CIDR block”, we will use the default IP Range, that is “10.0.0.0/16”

<img width="1817" height="681" alt="Screenshot 2026-10-03 at 10 54 07 AM" src="https://github.com/user-attachments/assets/d4766412-cd50-48bc-8697-13b339c736ad" />

- On “Number of Availability Zones”, choose “2”. This is already chosen by default.
- For number of Public and Private subnets, we will choose two each.

<img width="600" height="525" alt="Screenshot 2026-10-03 at 10 56 07 AM" src="https://github.com/user-attachments/assets/d38a438b-7cbe-4746-a9de-d13941f75d68" />

- In this tutorial, we are not going to enable “NAT Gateway” and the “S3 Gateway”. So, both have to be “None”

<img width="636" height="731" alt="Screenshot 2026-10-03 at 10 57 02 AM" src="https://github.com/user-attachments/assets/5909bbd2-4293-417f-b81e-0f26dd096fd7" />

- Click on “Create VPC”

<img width="728" height="735" alt="Screenshot 2026-10-03 at 10 58 28 AM" src="https://github.com/user-attachments/assets/0d4ffeb9-8f58-4f58-ba36-2d32a3c086dd" />

- The VPC has been created. Click on “View VPC

<img width="1616" height="354" alt="Screenshot 2026-10-03 at 11 00 10 AM" src="https://github.com/user-attachments/assets/35274e25-d000-4414-a4e4-43b9ed448d81" />

### STEP 2: Create Security Group

- The next thing to do is to create our security group. Let us navigate to security group
- Click on “Security Groups”

<img width="1608" height="375" alt="Screenshot 2026-10-03 at 11 01 48 AM" src="https://github.com/user-attachments/assets/b01d0fdf-d2c0-43a7-98d2-668d736ab4dc" />

- Click on “Create Security Group” to create a security group that will be dedicated to our WordPress environment. We are going to set this up to allowing HTTP traffic by opening Port 80 for inbound access to enable user to reach our WordPress site over the web.
-  Let is call the security group wp-sg and put same for description. On “VPC”, click on the drop down and select the VPC we created.
-  On the Inbound Rules, we are going to add HTTP. Click on Add Rule
-  Select HTTP and make it accessible from anywhere
-  Click on “Create Security Group”

<img width="1873" height="639" alt="Screenshot 2026-10-03 at 11 05 40 AM" src="https://github.com/user-attachments/assets/96b2dca4-2a57-4322-9187-0a7d634749fd" />

- We have created our own security group.

<img width="1610" height="605" alt="Screenshot 2026-10-03 at 11 07 11 AM" src="https://github.com/user-attachments/assets/44fd22c2-5bd4-40ad-ad85-5f3d64fa8e51" />

### STEP 3: Create Application Load Balancer
- The next thing is to create a Load Balancer and a Target group to efficiently manage the incoming traffic.
- So, go to the EC2 dash board
- Click on Load Balancers

<img width="834" height="380" alt="Screenshot 2026-10-03 at 11 09 19 AM" src="https://github.com/user-attachments/assets/632c1f62-c4fb-40f0-9eba-b020a7362828" />

- Click on “Create Load Balancer”
- We will use “Application Load Balancer”. Click on “Create”

<img width="391" height="811" alt="Screenshot 2026-10-03 at 11 10 15 AM" src="https://github.com/user-attachments/assets/2b35739d-fba9-4938-882b-b5f69958fe16" />

- Let us call the load balancer “wp-alb” and for the “Scheme”, we will use “Internet-facing”

<img width="1480" height="670" alt="Screenshot 2026-10-03 at 11 11 21 AM" src="https://github.com/user-attachments/assets/450e35dd-fe4c-4d03-a906-e022b1be3582" />

- Scroll down to “Network Mapping”
- Select the VPC we created
- Select the two availability zones and ensure they are on public subnet

<img width="1853" height="753" alt="Screenshot 2026-10-03 at 11 12 41 AM" src="https://github.com/user-attachments/assets/a75f8bb0-b261-438c-85e8-a875970ea1d7" />

- Scroll down to “security Group”
- Select the security group we created, that is “wp-sg”

<img width="1903" height="318" alt="Screenshot 2026-10-03 at 11 14 08 AM" src="https://github.com/user-attachments/assets/470d9b8b-456f-418a-b5b1-285d181d0e5f" />

- Scroll down to “Listeners and Routing” and create a target group

<img width="1828" height="196" alt="Screenshot 2026-10-03 at 11 14 52 AM" src="https://github.com/user-attachments/assets/ebeacfb7-7247-4b0a-8651-6b98090912b3" />

- Click on “Create target group” and a new window will open
- Select “IP Addresses” for “target type” 

<img width="1132" height="558" alt="Screenshot 2026-10-03 at 11 15 50 AM" src="https://github.com/user-attachments/assets/4fda1144-21d6-4f38-a2d4-bc8f66260f6a" />

- Scroll down to “Target Group”
- We will call the target group “wp-TG”

<img width="1266" height="625" alt="Screenshot 2026-10-03 at 11 16 53 AM" src="https://github.com/user-attachments/assets/0835f1a9-9b14-4d90-b573-5db9075eed31" />

- Scroll down to the end
- Click on “Next”
- Complete our IPv4 address, that is “10.0.0.0/16”
- Click on “Create target group”

<img width="1361" height="278" alt="Screenshot 2026-10-03 at 11 18 03 AM" src="https://github.com/user-attachments/assets/21f72a94-e495-4bfa-845c-1f75098a9ca9" />

<img width="1591" height="470" alt="Screenshot 2026-10-03 at 11 19 34 AM" src="https://github.com/user-attachments/assets/45a9b05b-3929-4c79-8fb3-fa492014bf95" />

- Head back to the application Load balancer tab
- Refresh and select the target group we just created

<img width="1812" height="288" alt="Screenshot 2026-10-03 at 11 20 58 AM" src="https://github.com/user-attachments/assets/c1c98ff1-36a3-46e3-bf2b-b38a01a29f79" />

- Scroll down to the end
- Click on “Create Load Balancer”
- The load balancer is being created
- The application load balancer is provisioning, wait for it to be active

<img width="1589" height="730" alt="Screenshot 2026-10-03 at 11 21 36 AM" src="https://github.com/user-attachments/assets/da746688-3cc6-4d23-a0cd-be019f0c7a59" />

- The application load balancer is now “ACTIVE”.

<img width="1600" height="289" alt="Screenshot 2026-10-03 at 11 24 39 AM" src="https://github.com/user-attachments/assets/c51ddf9c-ede0-4141-8e20-d1095d328c71" />

## STEP 4: Create ECS Task Definition
- The next step is to create the ECS Task Definition. Go to AWS Management Console and search for “ECS”. Task definition is the blue print of your container.

<img width="1251" height="395" alt="Screenshot 2026-10-03 at 11 25 10 AM" src="https://github.com/user-attachments/assets/d25be36f-4698-477f-b990-39b2e49ce770" />

- Click on “Elastic Container Service”
- Click on “Task Definition”
- Click on the drop down on “Create new task definition” and select “Create new task definition”

<img width="1555" height="312" alt="Screenshot 2026-10-03 at 11 26 12 AM" src="https://github.com/user-attachments/assets/457ac5c6-603a-4eab-86ab-baa15d95cf75" />

- Give the Task Definition a name, I will call it wordpress-task-def. In “Infrastructure Requirements”, choose “AWS Fargate”

<img width="1064" height="421" alt="Screenshot 2026-10-03 at 11 27 16 AM" src="https://github.com/user-attachments/assets/cd856755-7704-4d3e-aab1-aa21e89b549c" />

- Scroll down to Task Role
- Click on “IAM Console” and a new window will pop up

<img width="1296" height="312" alt="Screenshot 2026-10-03 at 11 30 21 AM" src="https://github.com/user-attachments/assets/55a2cf2c-b5fd-4b96-a6c1-abd667b02067" />

- On “Trusted Entity Type”, select “AWS Service” and on “Use Case”, search for “Elastic Container Service”. The select “Elastic Container Service -Task”.
- Click on “Next”

<img width="1107" height="759" alt="Screenshot 2026-10-03 at 11 31 37 AM" src="https://github.com/user-attachments/assets/5d078343-e233-4bc0-a7be-b9b513f9a2e6" />

- The permission we will be giving to this is “AmazonECSTaskExecutionRolePolicy”, search for “AmazonECSTask”

<img width="1558" height="645" alt="Screenshot 2026-10-03 at 11 32 55 AM" src="https://github.com/user-attachments/assets/db79c060-90b3-4031-b5d3-c372aee7d6bd" />

- Select the policy and click on “Next”
- Give the role a name. I will call it “ECSTaskExecutionRole”
- Click on “Create Role”

<img width="1216" height="467" alt="Screenshot 2026-10-03 at 11 33 46 AM" src="https://github.com/user-attachments/assets/8ab9edd7-669e-4140-acf7-7a234b013a1f" />

- The role has been created. I will go back and continue with my Task definition tab

<img width="1555" height="521" alt="Screenshot 2026-10-03 at 11 34 37 AM" src="https://github.com/user-attachments/assets/b9dd3bdb-025f-44d4-9efc-a4fa7621d98b" />

- Click on the drop down and select the Task Role we just created

<img width="1050" height="177" alt="Screenshot 2026-10-03 at 11 35 29 AM" src="https://github.com/user-attachments/assets/fa1feee4-a84e-4310-bdef-100470c14fa0" />

- Scroll down to “Container-1”
- On the container name, enter “wordpress” and on the “Image URI”, also enter wordpress since we are using the default WordPress image.

<img width="1505" height="339" alt="Screenshot 2026-10-03 at 11 37 01 AM" src="https://github.com/user-attachments/assets/6d20f6ab-5142-4e35-b84a-0c359220d2ca" />

- Scroll down to the end
- Click on “Create”

<img width="1550" height="567" alt="Screenshot 2026-10-03 at 11 38 01 AM" src="https://github.com/user-attachments/assets/5799e171-b3d5-410a-9a3b-10e6c65545af" />

- Click on “View task Definition”
- We have created the Task Definition. Let is now create the ECS Cluster.

<img width="1541" height="354" alt="Screenshot 2026-10-03 at 11 39 06 AM" src="https://github.com/user-attachments/assets/f9ff9b20-3bb3-4cc7-a93c-628e08abf02d" />

### STEP 4: Create ECS Cluster

- Click on “Clusters”
- Click on “Create Cluster”

<img width="1540" height="432" alt="Screenshot 2026-10-03 at 11 40 01 AM" src="https://github.com/user-attachments/assets/60f697da-f0c7-4993-ab8f-e9b750c04c99" />

- Give the cluster a name, I will call it “wordpress-cluster”
- On “Infrastructure – Optional”, we will use “AWS Fargate”. Scroll down to the end
- Click on “Create”

<img width="1528" height="602" alt="Screenshot 2026-10-03 at 11 41 10 AM" src="https://github.com/user-attachments/assets/b66ea160-46be-439b-8e34-61f8cf05f04b" />

- The cluster is being created

<img width="1566" height="444" alt="Screenshot 2026-10-03 at 11 51 11 AM" src="https://github.com/user-attachments/assets/9897fc3b-de32-4f10-9520-3594ecc63373" />

### STEP 6: Create ECS Service

- The cluster has been created, click on the cluster name
- Click on “Create”

<img width="1522" height="400" alt="Screenshot 2026-10-03 at 11 52 01 AM" src="https://github.com/user-attachments/assets/1f6af133-05c7-4b6a-b207-ef207f3c9c16" />

- Click on the drop down and select the Task definition we created
- Let is give the service a name, I will call it “wordpress-service”

<img width="1201" height="509" alt="Screenshot 2026-10-03 at 11 53 27 AM" src="https://github.com/user-attachments/assets/12f80d2e-5fe6-477a-a8bc-8a7424a99f8b" />

- Scroll down to “Networking” and click on it
- Select the VPC we created
- Click on the drop down and select the security group we created for this project

<img width="1174" height="777" alt="Screenshot 2026-10-03 at 11 54 51 AM" src="https://github.com/user-attachments/assets/b23df9b5-d590-4327-976e-19fdc4864352" />

- Scroll down to “Load Balancer”
- Check the box on “Use Load Balancing”
- Select “Use an existing load balancer”
- Click on the drop down and select the Application Load Balancer we created

<img width="1081" height="824" alt="Screenshot 2026-10-03 at 11 56 29 AM" src="https://github.com/user-attachments/assets/a56d1c8a-8a46-4d2b-9a23-34cc818bc23c" />

- Scroll down to “Listener”
- On “Listener”, select “Use an existing listener”, then click on the drop down and select “HTTP:80”.
- And on “Target Group”, select “Use an existing Target Group”, then click on the drop down and select the target group we created.

<img width="1141" height="778" alt="Screenshot 2026-10-03 at 11 57 55 AM" src="https://github.com/user-attachments/assets/e659449e-83c7-493f-98ef-b09eea2b4ee6" />

- Scroll down to the end
- Click on “Create”
- The service is being created

<img width="1513" height="646" alt="Screenshot 2026-10-03 at 11 58 47 AM" src="https://github.com/user-attachments/assets/8309a0b3-b3ba-4054-b311-8f95627e8e62" />

- Our service has been created. Click on the service name
- Click on “Configuration and Networking”
- Copy the DNS name and paste on your web browser

<img width="1500" height="398" alt="Screenshot 2026-10-03 at 12 00 46 PM" src="https://github.com/user-attachments/assets/9808b0e1-0d9b-45e0-8655-d5fdde8cd5cd" />

- We have been able to successfully deploy our WordPress site using ECS. We started by creating our own VPC, Security Group, our load balancer, our target group. We also created our Task definition in ECS, our cluster and our service.

<img width="1893" height="1034" alt="Screenshot 2026-10-03 at 1 44 20 PM" src="https://github.com/user-attachments/assets/2dde2655-f415-4715-a88e-ab09c5c9f55b" />
