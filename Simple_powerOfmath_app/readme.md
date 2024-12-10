# PowerofMath Web Application with AWS

## Description

In this project, I built a PowerofMath web application using a suite of AWS services to demonstrate scalability, flexibility, and serverless architecture. The application integrates the following AWS services:

- **Amazon DynamoDB**: A NoSQL database that serves as the backend data store for the application, providing low-latency data access and automatic scaling.
- **AWS Lambda**: A serverless compute service that runs backend functions in response to events, allowing for highly scalable and cost-effective application logic without the need for managing servers.
- **Amazon API Gateway**: Used to create RESTful APIs to interact with the Lambda functions. It handles routing requests, authorization, and scaling for API access.
- **AWS IAM (Identity and Access Management)**: Ensures secure and granular access control for the application by managing users, roles, and permissions, safeguarding both the API and Lambda functions.
- **AWS Amplify**: A powerful service for developing and deploying the front-end of the application, enabling an easy connection to AWS services like authentication, storage, and APIs.

## Key Features

- Seamless, serverless architecture for efficient resource management.
- Scalable, cost-effective solution with the ability to handle variable traffic and workloads.
- Fast data retrieval through DynamoDB, ensuring low-latency and high availability.
- Secure API access with robust IAM roles and policies.
- Easy-to-use front-end interface deployed with AWS Amplify, integrating with backend services for a smooth user experience.

This app showcases my ability to design and deploy applications in the cloud, leveraging AWS services to build efficient, scalable, and secure web solutions.

## Architecture Diagram
![awsSchema1](https://github.com/user-attachments/assets/cbee69d2-53db-4abc-8f81-b59550baa97f)

## What do we need?
- A way to create/ host a webpage
- A way to invoke math functionality
- A way to do math
- Somewhere to store/return the math results
- A way to handle permissions

Step 1: Set up AWS Amplify for Frontend
AWS Amplify will help with the frontend setup, including hosting and authentication (if required).

## Create an Amplify Project:

- Open the AWS Amplify Console and click "Create App".
- Choose "Host Web" as the platform.
- Connect your repository (GitHub, GitLab, Bitbucket, or manually).
- so we are going with manually
- so choose without git provider.
- Name the app
- click on choose a file an upload the html file from your Local system (provided the code below copy from it)
- Note that it should be Zip file (make sure tha file below i provided make it into zip first) and then upload the file
