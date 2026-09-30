## How to Build and Push Docker image to AWS ECR using Jenkins pipeline

- In this tutorial we are going to learn Configuring EC2 instance in AWS, Install Java on Ubuntu EC2 instance, Install Jenkins on Ubuntu EC2 instance, Add AWS credentials in Jenkins, Creating Elastic Container Registry (ECR) Repository in AWS, Create an IAM Role in AWS and attach the policy “AmazonEC2ContainerRegistryFullAccess”, how to Build and Push Docker image to AWS ECR using Jenkins pipeline.

### Prerequisites:
- AWS Account with Admin Privileges
- GitHub Account that will be cloned https://github.com/sd031/aws_codebuild_codedeploy_nodeJs_demo

## Step #1: Configuring an Ubuntu EC2 instance in AWS and SSH Connect to the instance

PART 1: Create the ubuntu EC2 instance
- Go to the AWS dashboard and then to the EC2 services.

<img width="1637" height="741" alt="Screenshot 2026-09-30 at 12 27 10 PM" src="https://github.com/user-attachments/assets/7ad6d476-3e0c-4651-a3b3-eefa45dc4169" />

- Click on “Launch Instance” and we will call the instance “Jenkins-Server”

<img width="720" height="174" alt="1_sJz-mns9WJ3WSxL6sBi_6A" src="https://github.com/user-attachments/assets/fe5c9bc1-83a4-4a1f-a6f0-85f5fabfd1b4" />

- On Application and OS Image, select “Ubuntu”

<img width="720" height="481" alt="1_b1YSboGk5sN6kheYzOvPUg" src="https://github.com/user-attachments/assets/681f75cd-df48-4c60-afbd-c1ca5a8745de" />

<img width="720" height="162" alt="1_SPjXYAULPtBK8WOQtffmUA" src="https://github.com/user-attachments/assets/25c51d3b-5bb2-4e13-9e0b-3fb5a4c3b8ac" />

<img width="720" height="128" alt="1_uLWgqjZNGb4N0FOfkoxKUg" src="https://github.com/user-attachments/assets/b8a2856a-413b-4613-a1d4-e74f48b7a4ae" />

Click on “Create key Pair”

<img width="1276" height="216" alt="Screenshot 2026-09-30 at 12 30 02 PM" src="https://github.com/user-attachments/assets/072daa02-be73-4a60-9a0c-9fbc0ce5408d" />

<img width="720" height="431" alt="1_wMJql_dDb46vXCZQnn0yTQ" src="https://github.com/user-attachments/assets/8509525e-adc0-460f-a487-ac9681d6dab5" />

<img width="720" height="223" alt="1_cheZydtNLejKThzS3gZtkw" src="https://github.com/user-attachments/assets/0bfb741c-1c31-47f2-aa35-4350a76ec0fa" />

- Click on “Launch Instance”

<img width="720" height="402" alt="1_m3VPMFB0tTsZywdgz8t0Kw" src="https://github.com/user-attachments/assets/8e098c55-976d-4335-ab57-f5d1c306eab7" />

- Click on “View all instances”

<img width="720" height="118" alt="1_Ms9rKpiRuOPjMmGFYWvXHA" src="https://github.com/user-attachments/assets/0ff89b81-19b4-43c4-89f9-01a21fd452e3" />
