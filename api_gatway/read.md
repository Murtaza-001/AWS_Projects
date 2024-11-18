  Building a Serverless Web Application with AWS Amplify, Lambda, API Gateway, DynamoDB, and IAM Roles


![awsSchema1](https://github.com/user-attachments/assets/4f6f05ba-853d-43d1-b9e4-a70d133cc01f)


Overview of the Architecture:
AWS Amplify will serve as the frontend service and handle authentication, API interactions, and hosting.
AWS Lambda will handle backend logic and interact with DynamoDB to read/write data.
API Gateway will expose HTTP endpoints for your frontend to interact with Lambda.
DynamoDB will store the data.
IAM will ensure Lambda has permissions to access DynamoDB.


Step 1: Create a DynamoDB Table
Go to the DynamoDB service in the AWS Management Console.
Click Create table.
Set the Table name (e.g., Items) and define a Primary Key (e.g., id as a String).
Leave the other settings as default, or modify them as needed.
Click Create to create the table.
