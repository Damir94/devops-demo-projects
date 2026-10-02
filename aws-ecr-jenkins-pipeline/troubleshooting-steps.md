# Troubleshooting Jenkins → Docker → AWS ECR Pipeline

## Introduction

In this project, I built a Jenkins CI/CD pipeline that:

1. Connects Jenkins to AWS ECR
2. Clones source code from GitHub
3. Builds a Docker image
4. Tags the Docker image
5. Pushes the image to Amazon ECR

The overall workflow is:

```text
GitHub
   |
   v
Jenkins
   |
   +----> AWS ECR Login
   |
   +----> Clone Git Repository
   |
   +----> Docker Build
   |
   +----> Docker Tag
   |
   +----> Docker Push
   |
   v
AWS ECR
```

During implementation, several issues occurred. This document explains how each problem was identified, troubleshot, and resolved.

---

# 1. Jenkins Pipeline Stuck at "Waiting for next available executor"

## Problem

When the pipeline was started, Jenkins displayed:

```text
Still waiting to schedule task
Waiting for next available executor
```

The pipeline did not start executing.

## Investigation

The Jenkins built-in node showed a disk-space warning.

The Jenkins node information showed approximately:

```text
Free Disk Space: 13.73 GiB
Free Swap Space: 0 B
Free Temp Space: 449.61 MiB
```

The server itself had enough disk space:

```bash
df -h
```

Example:

```text
Filesystem       Size  Used Avail Use% Mounted on
/dev/root         19G  4.6G   14G  25% /
```

However, `/tmp` was mounted as a relatively small `tmpfs`:

```bash
df -h /tmp
```

Output:

```text
Filesystem      Size  Used Avail Use% Mounted on
tmpfs           455M  4.8M  450M   2% /tmp
```

Jenkins' configured temporary-space threshold was higher than the available `/tmp` filesystem.

## Root Cause

Jenkins' disk/temp-space monitoring threshold was configured at approximately:

```text
1 GiB / 2 GiB
```

while the `/tmp` filesystem was only approximately:

```text
455 MiB
```

Therefore, Jenkins considered the node unsuitable for running builds.

## Solution

The Jenkins disk-space monitoring thresholds were lowered to a value appropriate for the server, approximately:

```text
100 MiB
```

After changing the threshold, Jenkins became available for pipeline execution.

## Verification

The pipeline eventually started successfully:

```text
Running on Jenkins in /var/lib/jenkins/workspace/ECR-pipeline
```

## Lesson Learned

Jenkins disk-space monitoring considers individual filesystems such as `/tmp`, not only the total disk space available on `/`.

When Jenkins reports that an executor is unavailable, check:

```bash
df -h
df -h /tmp
```

and review the Jenkins node's disk-space monitoring thresholds.

---

# 2. Jenkins Returned HTTP 403 After Configuration Changes

## Problem

After changing the Jenkins node configuration, accessing Jenkins locally returned:

```text
HTTP/1.1 403 Forbidden
X-Jenkins: 2.580.1
X-You-Are-Authenticated-As: anonymous
X-Required-Permission: hudson.model.Hudson.Read
```

## Investigation

Jenkins was still running.

The HTTP response showed:

```text
X-Jenkins: 2.580.1
```

which confirmed that the Jenkins server was responding.

The important part was:

```text
X-You-Are-Authenticated-As: anonymous
```

The local `curl` request was simply not authenticated to Jenkins.

## Root Cause

The 403 response was an authentication/authorization issue with the local HTTP request, not a Jenkins process failure.

## Solution

Jenkins was allowed to continue running, and access through the authenticated Jenkins browser session was restored.

## Lesson Learned

A Jenkins HTTP 403 does not necessarily mean Jenkins is down.

Useful checks include:

```bash
sudo systemctl status jenkins
```

and:

```bash
curl -I http://localhost:8080
```

A response from Jetty/Jenkins confirms that the service itself is running.

---

# 3. AWS ECR Login Failed with "SignatureDoesNotMatch"

## Problem

The Jenkins pipeline reached the AWS ECR login stage but failed:

```text
aws: [ERROR]: An error occurred (InvalidSignatureException)
when calling the GetAuthorizationToken operation:

The request signature we calculated does not match the signature you provided.
```

The pipeline also displayed:

```text
error: cannot perform an interactive login from a non TTY device
```

## Initial Pipeline Command

The pipeline used:

```bash
aws ecr get-login-password --region us-east-1 |
docker login --username AWS --password-stdin \
923918898373.dkr.ecr.us-east-1.amazonaws.com
```

## Investigation

First, we checked whether AWS environment variables were configured for Jenkins:

```bash
sudo -u jenkins env | grep '^AWS_'
```

No AWS environment variables were present.

Next, we checked the AWS credential source:

```bash
sudo -u jenkins aws configure list
```

The output showed:

```text
NAME       : VALUE                    : TYPE
profile    : <not set>                : None
access_key : ****************BE5Z     : shared-credentials-file
secret_key : ****************OZfg     : shared-credentials-file
region     : us-east-1                : config-file
```

This showed that the Jenkins user was using credentials from a shared credentials file.

## Root Cause

Jenkins was using AWS credentials stored under:

```text
/var/lib/jenkins/.aws/credentials
```

Those credentials were invalid or mismatched, causing:

```text
SignatureDoesNotMatch
```

The Docker error:

```text
cannot perform an interactive login from a non TTY device
```

was a secondary error because the AWS command failed to provide a valid password to Docker.

## Solution

The EC2 instance already had an IAM role:

```text
EC2-ECR-proj-Role
```

Instead of using static AWS access keys, Jenkins was configured to use the EC2 instance IAM role.

The old Jenkins credentials file was backed up:

```bash
sudo mv /var/lib/jenkins/.aws/credentials \
/var/lib/jenkins/.aws/credentials.backup
```

Then AWS authentication was tested:

```bash
sudo -u jenkins aws sts get-caller-identity
```

The command successfully authenticated using the EC2 IAM role.

## ECR Verification

The following command was then tested:

```bash
sudo -u jenkins aws ecr get-login-password --region us-east-1 |
docker login --username AWS --password-stdin \
923918898373.dkr.ecr.us-east-1.amazonaws.com
```

Result:

```text
Login Succeeded
```

## Lesson Learned

For EC2-based Jenkins servers, using an IAM role is preferable to storing long-lived AWS access keys on the server.

The credential flow became:

```text
Jenkins
   |
   v
EC2 Instance
   |
   v
EC2-ECR-proj-Role
   |
   v
AWS ECR
```

---

# 4. Docker Build Appeared to Hang While Pulling node:14

## Problem

The Jenkins pipeline reached:

```text
[Pipeline] { (Building image)
```

and started:

```text
docker build -t ecrrepo:v1 .
```

The Dockerfile started with:

```dockerfile
FROM node:14
```

Jenkins displayed:

```text
14: Pulling from library/node
...
Pulling fs layer
...
```

The build remained at this stage for several minutes.

## Investigation

We checked whether the image was already available:

```bash
docker images | grep node
```

No `node:14` image was present.

We then manually pulled the image:

```bash
docker pull node:14
```

The pull completed successfully:

```text
3d2201bd995c: Pull complete
0d27a8e86132: Pull complete
1de76e268b10: Pull complete
...
2ff1d7c41c74: Pull complete

Status: Downloaded newer image for node:14
```

## Root Cause

The Jenkins build was performing the first download of the `node:14` base image.

The image consisted of multiple layers and had not previously been cached on the server.

## Solution

The image was manually pulled:

```bash
docker pull node:14
```

This cached the image locally.

A subsequent Jenkins build could then use the cached base image.

## Lesson Learned

When a Docker build appears stuck at:

```text
Pulling from library/...
```

test the base image independently:

```bash
docker pull <image>:<tag>
```

This helps determine whether the problem is Jenkins or Docker/image retrieval.

---

# 5. Jenkins Kept Restarting During Docker Build

## Problem

After Docker successfully started building the image, Jenkins displayed:

```text
Step 1/7 : FROM node:14
 ---> a158d3b9b4e3

Step 2/7 : WORKDIR /usr/src/app
 ---> Running in 9e7bde7cb534
```

Then the build showed:

```text
Resuming build at Fri Oct 02 16:52:23 UTC 2026 after Jenkins restart
Ready to run at Fri Oct 02 16:52:23 UTC 2026
```

This happened repeatedly.

## Investigation

We checked the Linux kernel logs:

```bash
sudo journalctl -k --since "30 minutes ago" --no-pager |
grep -Ei "oom|killed process|out of memory"
```

The logs revealed:

```text
dockerd invoked oom-killer
```

and:

```text
Out of memory: Killed process 35944 (java)
```

The process that was killed was:

```text
java
```

Jenkins runs on Java, so the kernel was killing the Jenkins process.

## Root Cause

The EC2 instance did not have enough available memory to run Jenkins and Docker builds simultaneously.

The Linux Out-Of-Memory (OOM) killer terminated Jenkins:

```text
Out of memory: Killed process ... (java)
```

This caused Jenkins to restart and attempt to resume the pipeline.

## Solution

Swap space was added to the server.

A 2 GB swap file was created:

```bash
sudo fallocate -l 2G /swapfile
```

Permissions were configured:

```bash
sudo chmod 600 /swapfile
```

The swap filesystem was created:

```bash
sudo mkswap /swapfile
```

Swap was enabled:

```bash
sudo swapon /swapfile
```

The configuration was verified:

```bash
free -h
```

The swap was also made persistent:

```bash
echo '/swapfile none swap sw 0 0' | \
sudo tee -a /etc/fstab
```

Finally, Jenkins was restarted:

```bash
sudo systemctl restart jenkins
```

## Result

After adding swap, Jenkins was able to continue the Docker build without being killed by the Linux OOM killer.

## Lesson Learned

When Jenkins unexpectedly restarts during a Docker build, don't assume Jenkins itself is crashing.

Check for Linux OOM events:

```bash
sudo journalctl -k | grep -Ei "oom|out of memory|killed process"
```

For Jenkins + Docker workloads, sufficient RAM is important. Swap can provide additional safety, but increasing the EC2 instance's RAM is a better long-term solution for heavier builds.

---

# 6. Docker Build Warning About Legacy Builder

## Problem

During the Docker build, Docker displayed:

```text
DEPRECATED: The legacy builder is deprecated and will be removed
in a future release.

Install the buildx component to build images with BuildKit.
```

## Root Cause

The Docker installation was using the legacy Docker builder rather than BuildKit/buildx.

## Impact

This was a warning rather than a build failure.

The Docker image could still be built successfully.

## Solution

No immediate change was required to complete this project.

For future projects, Docker BuildKit/buildx should be enabled and used.

For example:

```bash
docker buildx version
```

and:

```bash
docker buildx build .
```

## Lesson Learned

Not every message marked `WARNING` or `DEPRECATED` is the cause of a failed build.

Always distinguish between:

```text
WARNING
```

and:

```text
ERROR
```

before changing the configuration.

---

# 7. Docker Credentials Warning

## Problem

After successfully logging into ECR, Docker displayed:

```text
WARNING! Your credentials are stored unencrypted in
'/var/lib/jenkins/.docker/config.json'.
```

## Root Cause

Docker stored the registry authentication information in the Jenkins user's Docker configuration.

## Impact

This did not prevent the pipeline from working.

The ECR login was successful:

```text
Login Succeeded
```

## Future Improvement

For a production Jenkins environment, a Docker credential helper or Jenkins-managed credentials can be used to avoid storing credentials in an unencrypted Docker configuration file.

For this project, the warning did not prevent the ECR push.

---

# Final Working Pipeline

After troubleshooting, the Jenkins pipeline successfully followed this workflow:

```text
                         GitHub
                           |
                           v
                     +-----------+
                     |  Jenkins  |
                     +-----------+
                           |
                           v
                  AWS ECR Authentication
                           |
                           v
                  EC2 IAM Role
              EC2-ECR-proj-Role
                           |
                           v
                    Clone Repository
                           |
                           v
                    Docker Build
                           |
                           v
                     ecrrepo:v1
                           |
                           v
                    Docker Tag
                           |
                           v
                       AWS ECR
                           |
                           v
              ecrrepo:v1 pushed
```

# Troubleshooting Summary

| Problem                        | Root Cause                                         | Solution                                                           |
| ------------------------------ | -------------------------------------------------- | ------------------------------------------------------------------ |
| Jenkins waiting for executor   | `/tmp` below Jenkins threshold                     | Lowered disk/temp monitoring threshold                             |
| Jenkins HTTP 403               | Local request was unauthenticated                  | Verified Jenkins service was running and used authenticated access |
| `SignatureDoesNotMatch`        | Invalid/stale AWS credentials                      | Removed stale Jenkins credentials and used EC2 IAM role            |
| Docker `non TTY` error         | AWS command failed before Docker received password | Fixed AWS authentication                                           |
| Docker build slow at `node:14` | Base image was not cached                          | Pulled `node:14` manually                                          |
| Jenkins repeatedly restarted   | Linux OOM killer killed Jenkins Java process       | Added 2 GB swap                                                    |
| Legacy builder warning         | Docker using deprecated builder                    | Not a blocker; BuildKit/buildx is future improvement               |
| Docker credentials warning     | Credentials stored in Docker config                | Accepted for lab; credential helper recommended for production     |

# Key Troubleshooting Commands

These commands were particularly useful during troubleshooting:

```bash
# Check disk space
df -h

# Check /tmp
df -h /tmp

# Check memory
free -h

# Check Jenkins status
sudo systemctl status jenkins

# Check Jenkins logs
sudo journalctl -u jenkins --no-pager

# Check kernel/OOM events
sudo journalctl -k | grep -Ei "oom|out of memory|killed process"

# Check AWS credentials used by Jenkins
sudo -u jenkins aws configure list

# Test AWS authentication
sudo -u jenkins aws sts get-caller-identity

# Test ECR authentication
sudo -u jenkins aws ecr get-login-password --region us-east-1 | \
docker login --username AWS --password-stdin \
923918898373.dkr.ecr.us-east-1.amazonaws.com

# Pull Docker base image manually
docker pull node:14

# Check Docker images
docker images

# Check Docker disk usage
docker system df
```

# Conclusion

This troubleshooting process demonstrated that a Jenkins CI/CD pipeline can fail for reasons outside the Jenkinsfile itself.

The final issues were related to:

* Jenkins resource monitoring
* AWS credential management
* Docker image availability
* EC2 memory limitations
* Linux OOM behavior

The most important troubleshooting lesson was to **identify the actual layer where the failure occurs before changing the pipeline**.

The final working setup uses an EC2 IAM role for AWS authentication, Docker for image creation, Jenkins for automation, and Amazon ECR for container image storage.
