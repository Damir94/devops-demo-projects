### AWS S3 Static Website Hosting

<img width="933" height="267" alt="Screenshot 2026-09-17 at 4 40 12 PM" src="https://github.com/user-attachments/assets/1588bb55-b68d-43ee-ace4-3ed2ad03f5de" />

## Project Objective
The objective of this project is to host a static website using Amazon S3. It helps in
understanding how to create an S3 bucket, upload website files, enable Static Website Hosting,
configure Bucket Policy, and make the website publicly accessible through the S3 Website
Endpoint.

## Project Description
In this project, a static website was deployed using Amazon S3. A bucket was created in AWS,
and the required HTML files (index.html and error.html) were uploaded. Static Website
Hosting was enabled, and a Bucket Policy was added to allow public access. Finally, the website
was successfully accessed through the S3 Website Endpoint using a web browser.

## Architecture Explanation
The user opens the website using a web browser. The browser sends a request to the S3 Website
Endpoint. Amazon S3 checks the bucket for the requested file. If the file is available, the
index.html page is displayed. If the requested page does not exist, the configured
error.html page is shown instead. This process allows a static website to be hosted without
using a web server.

## Project Workflow

<img width="936" height="266" alt="Screenshot 2026-09-17 at 4 42 24 PM" src="https://github.com/user-attachments/assets/0c0a00e3-fdf0-4fa8-a6d7-5f853c0f3ceb" />

## Workflow Explanation
The project begins by logging into the AWS Management Console and opening the Amazon S3
service. A new S3 bucket is created with a unique bucket name. The HTML files are uploaded to
the bucket, and Static Website Hosting is enabled. A bucket policy is then configured to allow
public access to the website files. Finally, AWS generates a Website Endpoint, which is opened
in a web browser to verify that the website has been successfully hosted.

## Project Implementation
The implementation of the AWS S3 Static Website Hosting project was carried out using the
AWS Management Console. The complete process involved creating an Amazon S3 bucket,
uploading website files, enabling Static Website Hosting, configuring the required permissions,
and verifying the deployed website using the generated Website Endpoint.

## Step 1: Create Amazon S3 Bucket

- The Create Bucket option was selected.

<img width="926" height="417" alt="Screenshot 2026-09-19 at 9 33 59 AM" src="https://github.com/user-attachments/assets/28b18b04-3a13-4acf-b303-2bc337c8f457" />

- The Block Public Access setting was disabled after confirming the warning message.
- Finally, the bucket was created successfully.

<img width="1147" height="482" alt="Screenshot 2026-09-19 at 9 35 02 AM" src="https://github.com/user-attachments/assets/f4febdf4-71f8-4420-9e66-4be09d3eb3b0" />


## Step 2: Enable Static Website Hosting
- Open the S3 bucket.
- Go to the Properties tab.
- Find Static website hosting.
- Click Edit.
- Select Enable.
- Choose:
  - Hosting type: Host a static website
  - Set the index document: index.html
  - Set the error document: error.html
- Save the changes.

<img width="817" height="601" alt="Screenshot 2026-09-19 at 9 42 35 AM" src="https://github.com/user-attachments/assets/248365c1-4a90-4967-9143-991a180affe1" />

## Step 3: Upload Website Files
- Open the S3 bucket.
- Select Upload.
- Add:
  - index.html
  - error.html
- Click Upload.
- The files are now stored as objects inside the S3 bucket.

<img width="1867" height="399" alt="Screenshot 2026-09-19 at 9 48 36 AM" src="https://github.com/user-attachments/assets/72e4c2fd-2f74-4c42-a487-64e74353cf9a" />

## Step 4: Add a Bucket Policy
- For a basic public static website, add a bucket policy that allows public read access to the website objects
- Replace resource name with your actual bucket name.

```bash
{
    "Version":"2012-10-17",
    "Statement":[
      {
        "Sid":"PublicRead",
        "Effect":"Allow",
        "Principal": "*",
        "Action":["s3:GetObject"],
        "Resource":["arn:aws:s3:::examplebucket/*"]
      }
    ]
  }
```

## Step 5: Access Website Using Website Endpoint
- After completing all the configurations, the generated Website Endpoint was copied and opened in a web browser. Amazon S3 successfully served the index.html page, confirming that the website had been hosted correctly.

<img width="1270" height="139" alt="Screenshot 2026-09-19 at 9 59 53 AM" src="https://github.com/user-attachments/assets/9e12ac5f-4f87-4247-b949-54d64ee98e95" />

## Step 6: Final Website Output
- The website was successfully displayed in the browser using the generated S3 Website Endpoint.
- The output confirmed that the HTML files had been uploaded correctly and that Static Website Hosting was working as expected.

<img width="888" height="737" alt="Screenshot 2026-09-19 at 10 01 15 AM" src="https://github.com/user-attachments/assets/1227fad0-786c-4dec-884f-a0ac2eeadbfa" />
