## How to Build and Push Docker image to AWS ECR using Jenkins pipeline

- In this tutorial we are going to learn Configuring EC2 instance in AWS, Install Java on Ubuntu EC2 instance, Install Jenkins on Ubuntu EC2 instance, Add AWS credentials in Jenkins, Creating Elastic Container Registry (ECR) Repository in AWS, Create an IAM Role in AWS and attach the policy “AmazonEC2ContainerRegistryFullAccess”, how to Build and Push Docker image to AWS ECR using Jenkins pipeline.

### Prerequisites:
- AWS Account with Admin Privileges
- GitHub Account that will be cloned https://github.com/sd031/aws_codebuild_codedeploy_nodeJs_demo

## Step 1: Configuring an Ubuntu EC2 instance in AWS and SSH Connect to the instance

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

<img width="1195" height="665" alt="Screenshot 2026-10-02 at 10 25 05 AM" src="https://github.com/user-attachments/assets/3fc73c45-4fad-4e41-bc71-78c9f02cb54b" />

- Click on “View all instances”

<img width="1517" height="256" alt="Screenshot 2026-10-02 at 10 26 08 AM" src="https://github.com/user-attachments/assets/ae6fb120-2ca8-4a21-b239-f4156db69933" />

PART 2: SSH Connect to the instance

- Select the instance we just create

<img width="1507" height="267" alt="Screenshot 2026-10-02 at 10 27 41 AM" src="https://github.com/user-attachments/assets/51467872-f0a2-49d3-8fc7-21ccf33845be" />

- Click on “Connect”

<img width="1799" height="282" alt="Screenshot 2026-10-02 at 10 28 35 AM" src="https://github.com/user-attachments/assets/2aa9eeee-2242-45ec-8393-30242e7b4b67" />

- Click on “Connect” again

<img width="779" height="201" alt="Screenshot 2026-10-02 at 10 29 26 AM" src="https://github.com/user-attachments/assets/d17a3eb5-81cb-4109-8ed7-4e9451f0afa1" />

- We have now SSH connect to the instance

### Step #2: Install Java on Ubuntu EC2 Instance

- After the successful SSH connection, firstly update the Linux machine. And install java using below commands:
```bash
sudo apt update
```

<img width="1303" height="336" alt="Screenshot 2026-10-02 at 10 30 50 AM" src="https://github.com/user-attachments/assets/8c24a4c6-2a7a-4040-ae9d-f9782cf6fb4c" />

- Now let us install java 21
```bash
sudo apt install fontconfig openjdk-21-jre
java -version
```

<img width="945" height="141" alt="Screenshot 2026-10-02 at 10 39 48 AM" src="https://github.com/user-attachments/assets/ac233574-b809-4c3a-a277-f1122dc3aea6" />

### Step 3: Install Jenkins on Ubuntu EC2 Instance 
- Add Jenkins official repository & key
```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

<img width="1088" height="370" alt="Screenshot 2026-10-02 at 10 40 42 AM" src="https://github.com/user-attachments/assets/0f76e59f-27c9-4711-89b4-9aeb1a43a571" />

- Install Jenkins
```bash
sudo apt update
sudo apt install jenkins
```

<img width="891" height="342" alt="Screenshot 2026-10-02 at 10 41 58 AM" src="https://github.com/user-attachments/assets/a2f1f88b-005b-423e-8957-6a6800bdd67c" />

### Step 4: Enable and start Jenkins on Ubuntu EC2 Instance

- Start & enable Jenkins
```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
sudo systemctl status jenkins
```

<img width="1492" height="405" alt="Screenshot 2026-10-02 at 10 42 42 AM" src="https://github.com/user-attachments/assets/2499ed19-c5ab-4dbc-bbd9-021fda73f1ee" />

- Jenkins has been installed and it is active and running

### Step 5: Install git on Ubuntu EC2 Instance

- We need to Install git using below command
```bash
sudo apt install git
```

### Step 6: Access Jenkins on Browser

PART 1: Enable Port 8080 on the EC2 instance
- Select the instance
- Click on the “Security” tab
- Right-click on the “Security Group” and choose “Open in new tab”
- Click on “Edit Inbound Rules”
- Click on “Add Rule”
- Add port 8080
- Click on “Save rules”

<img width="1581" height="337" alt="Screenshot 2026-10-02 at 10 51 03 AM" src="https://github.com/user-attachments/assets/eb69d090-f6fe-4c02-a35b-5029974771c4" />

- Port 8080 has been enabled

PART 2: Access Jenkins on browser
```bash
http://<Instance_ip>:8080
```
- After that on the browser, you should see the Jenkins interface that asks for the administrator password.

<img width="995" height="485" alt="Screenshot 2026-10-02 at 10 52 23 AM" src="https://github.com/user-attachments/assets/87ebcfe5-24e6-4e11-b9f8-6eb9cc25fbcc" />

- Now cat the following Jenkins file to retrieve the Administrator password and paste it to the Jenkins dashboard. Use the command:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

<img width="995" height="73" alt="Screenshot 2026-10-02 at 10 53 19 AM" src="https://github.com/user-attachments/assets/01b35333-9e22-47e8-8f11-8b3a38b80f68" />

- Copy the password:
- Paste it on the Jenkins browser

<img width="884" height="166" alt="Screenshot 2026-10-02 at 10 54 40 AM" src="https://github.com/user-attachments/assets/9fe22735-5b72-49c4-98cd-539deeb8df6b" />

- Click on “Continue”

<img width="941" height="458" alt="Screenshot 2026-10-02 at 10 55 16 AM" src="https://github.com/user-attachments/assets/f566a80e-92aa-4e0d-a5ff-47f70bc61bcd" />

- Click on “Install suggested plugins”

<img width="992" height="520" alt="Screenshot 2026-10-02 at 10 55 57 AM" src="https://github.com/user-attachments/assets/78205786-42e6-4396-a49e-ae5bfa777651" />

- Enter your user name, password and email address
- Click on “Save and Continue”

<img width="911" height="375" alt="Screenshot 2026-10-02 at 10 57 40 AM" src="https://github.com/user-attachments/assets/ba637d60-9597-4fa6-bde7-3cab8a819e3d" />

- Click on “Save and Finish”

<img width="995" height="321" alt="Screenshot 2026-10-02 at 10 58 13 AM" src="https://github.com/user-attachments/assets/a5ef3a1c-d773-403d-be75-0ea63771c5fe" />

- Click on “Start using Jenkins”. After the configuration is completed, you should see the Jenkins dashboard.

<img width="892" height="611" alt="Screenshot 2026-10-02 at 10 58 45 AM" src="https://github.com/user-attachments/assets/23d3c5aa-bf5a-4dd2-b40a-0a156e2b834e" />

### Step 7: Add AWS credentials in Jenkins

- We may also set up AWS credentials in Jenkins so that it facilitates the Docker push to the ECR repository. Go to Jenkins Dashboard and click on “Manage Jenkins”

<img width="1879" height="507" alt="Screenshot 2026-10-02 at 10 59 50 AM" src="https://github.com/user-attachments/assets/d0b337f4-44e6-4d22-8e30-758bfdbe2091" />

- Click on “System”
- Click on “Global credentials”
- Click on “Add Credentials”

<img width="640" height="683" alt="Screenshot 2026-10-02 at 11 01 47 AM" src="https://github.com/user-attachments/assets/e8c49f70-f433-4325-a6dd-171eea838904" />

- Under “Kind”, select “Username with Password”. On username, put your “AWS username”. For password, use your “AWS password” and for ID, use put your “AWS Account ID”
- Click on “Save”

### Step 8: Install Docker on Ubuntu EC2 Instance

- Now here we need to Install Docker
```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl restart docker
sudo chmod 777 /var/run/docker.sock
```

<img width="1166" height="361" alt="Screenshot 2026-10-02 at 11 08 26 AM" src="https://github.com/user-attachments/assets/4949e150-2908-49e2-a99c-a9b49f208c07" />

- After Installing Docker we need to give some permission
```bash
sudo usermod -aG docker $USER
```

### Step 9: Installing plugins in Jenkins

- Head back to Jenkins dashboard
- Click on “manage Jenkins”
- Click on “Plugins
- Click on “Available Plugin”
- Search for the following plugins: Docker, Docker Pipeline and Amazon ECR
- Click on “Install”

<img width="1540" height="605" alt="Screenshot 2026-10-02 at 11 10 51 AM" src="https://github.com/user-attachments/assets/8ae668d9-a7c5-4f0a-b43e-cbc7c72cfa0f" />

### Step 10: Creating ECR Repository in AWS

- Let us create AWS ECR repository to push this image. Go to AWS Management console and search for “ECR”

<img width="1365" height="436" alt="Screenshot 2026-10-02 at 11 11 48 AM" src="https://github.com/user-attachments/assets/c6e85b71-b7f2-42c3-9651-631fc04fb32d" />

- Click on “Elastic Container Registry” under “Services”
- Click on “Create Repository” and we will call the repository “ecrrepo”
- Click on “Create”

<img width="1781" height="673" alt="Screenshot 2026-10-02 at 11 13 25 AM" src="https://github.com/user-attachments/assets/5a8ad2d9-2a15-4d55-882a-ffc63ae1ffcd" />

- The repository has been created

<img width="1556" height="328" alt="Screenshot 2026-10-02 at 11 14 12 AM" src="https://github.com/user-attachments/assets/c05cafdc-7e31-41ea-8fcc-6a465cc3fa8a" />

## Step 11: Create an IAM Role for ECR and add permission

- We will create an IAM role and attached the policy “AmazonEC2ContainerRegistryFullAccess” to the created IAM role. Here in this step, we need to create IAM role with below permission. I will call the repository “EC2-ECR-Proj -Role”
- Search for IAM on the AWS Management console
- Click on “IAM”

<img width="1043" height="696" alt="Screenshot 2026-10-02 at 11 15 21 AM" src="https://github.com/user-attachments/assets/1d3ca3fa-96cd-4cc9-9851-738a8bc5fbf4" />

- Click on “Roles”
- Click on “Create Role”

<img width="1197" height="757" alt="Screenshot 2026-10-02 at 11 16 50 AM" src="https://github.com/user-attachments/assets/880dfefb-6b62-4bf1-8ad5-61b1bbd29aed" />

- Click on “Next”
- Search for “AmazonEC2ContainerRegistryFullAccess” and select it

<img width="1534" height="672" alt="Screenshot 2026-10-02 at 11 17 45 AM" src="https://github.com/user-attachments/assets/8682c431-df61-4b1b-98b4-242d3d323b27" />

- Click on “next” and give the role the name “EC2-ECR-proj-Role”

<img width="1551" height="546" alt="Screenshot 2026-10-02 at 11 18 32 AM" src="https://github.com/user-attachments/assets/46c9e323-d89f-4acb-8ade-09068b09ff85" />

- Click on “Create role”

<img width="1529" height="211" alt="Screenshot 2026-10-02 at 11 19 11 AM" src="https://github.com/user-attachments/assets/1724b70f-0d23-4336-be55-d4011869186c" />

### Step 12: Install AWS CLI on Ubuntu EC2 Instance

- You can go to the official site of AWS and Install
```bash
sudo apt install curl unzip
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo unzip awscliv2.zip
sudo ./aws/install
```

- Check the aws cli version using the command:
```bash
aws - version
```

<img width="944" height="116" alt="Screenshot 2026-10-02 at 11 24 09 AM" src="https://github.com/user-attachments/assets/03ec4096-d6ad-4de3-a166-0b00c7a3ae65" />

### Step 13: Create an Access and Secret Key

- To create Access and Secret keys, click on your account at the top right-hand side
- Click on “Security Credentials”
- Click on “Create Access Key”
- Click on “Next”
- Click on “Create Access Key”
- Click on “Download .csv file”, This will save the .csv file containing your Access Key and Secret Key
- Then run the command:
```bash
sudo -su jenkins
```

<img width="469" height="43" alt="Screenshot 2026-10-02 at 11 26 33 AM" src="https://github.com/user-attachments/assets/be17e73b-71ff-4f15-b699-a7dfaee6c532" />

- Followed by the command:
```bash
aws configure
```
- Enter the Access Key ID and press Enter

### Step 14: Push Docker image to AWS ECR using Jenkins pipeline

- So let us create Jenkins pipeline. Go to the Jenkins Dashboard
- Click on “new Item”
- I will give the pipeline the name “ECR-pipeline”
- select Pipeline and click “OK”

<img width="899" height="861" alt="Screenshot 2026-10-02 at 11 31 24 AM" src="https://github.com/user-attachments/assets/61fafbef-4eb5-40ed-ba90-0cacb77252c9" />

- Click on “Pipeline”
- paste this code
- Modify the parts highlighted in red to match your credentials and the ECR repository name. In my case my repository is called “ecrrepo”, my “Repository URI” can be obtained as shown below.

```bash
pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="510786428272"
        AWS_DEFAULT_REGION="us-east-1"
        IMAGE_REPO_NAME="ecrrepo"
        IMAGE_TAG="v1"
        REPOSITORY_URI = "510786428272.dkr.ecr.us-east-1.amazonaws.com/ecrrepo"
    }
   
    stages {
        
         stage('Logging into AWS ECR') {
            steps {
                script {
                sh """aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"""
                }
                 
            }
        }
        
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/sd031/aws_codebuild_codedeploy_nodeJs_demo.git']]])     
            }
        }
  
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }<img width="1897" height="595" alt="Screenshot 2026-10-02 at 1 02 49 PM" src="https://github.com/user-attachments/assets/663833da-408e-4d48-aa75-8b4b0fdde5a2" />
```

- Click on “Save”
- Now, click on “Build Now”

<img width="1190" height="823" alt="Screenshot 2026-10-02 at 1 01 37 PM" src="https://github.com/user-attachments/assets/ed6dd0d3-da60-483f-9e31-459d5e1dcb67" />

- The build is successful. Now let us Check ECR Repo our image push or not

<img width="1548" height="431" alt="Screenshot 2026-10-02 at 1 04 06 PM" src="https://github.com/user-attachments/assets/bac0b4fd-6d2e-4080-97e4-85c6f0c4e936" />

- You can see the image has been push to the ECR

### Conclusion:
- In this article we have covered Configuring EC2 instance in AWS, Install Java on Ubuntu EC2 instance, Install Jenkins on Ubuntu EC2 instance, Add AWS credentials in Jenkins, Creating ECR Repository in AWS, Create an IAM Role and add the permission “AmazonEC2ContainerRegistryFullAccess” to the role, then build and Push Docker image to AWS ECR using Jenkins pipeline.
