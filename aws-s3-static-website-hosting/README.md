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
- The Block Public Access setting was disabled after confirming the warning message.
- Finally, the bucket was created successfully.

## Step 2: Create Website Files
- The website files were created using Visual Studio Code. Two HTML files were prepared:
  ● index.html – Displays the homepage of the website.
  ● error.html – Displays an error message if the requested page is unavailable.
- These files contained the required HTML content for demonstrating static website hosting.

