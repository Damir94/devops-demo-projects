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


- The application load balancer is now “ACTIVE”.


## STEP 4: Create ECS Task Definition
- The next step is to create the ECS Task Definition. Go to AWS Management Console and search for “ECS”. Task definition is the blue print of your container.
<img width="1589" height="730" alt="Screenshot 2026-10-03 at 11 21 36 AM" src="https://github.com/user-attachments/assets/da746688-3cc6-4d23-a0cd-be019f0c7a59" />
