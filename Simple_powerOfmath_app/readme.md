# PowerofMath Web Application with AWS

## Description

In this project, we will built the PowerofMath web application using a suite of AWS services to demonstrate scalability, flexibility, and serverless architecture. The application integrates the following AWS services:

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

##1. Set up AWS Amplify for Frontend##
AWS Amplify will help with the frontend setup, including hosting and authentication (if required).

## Create an Amplify Project:

- Open the AWS Amplify Console and click "Create App".
- Choose "Host Web" as the platform.
- Connect your repository (GitHub, GitLab, Bitbucket, or manually).
- so we are going with manually
- so choose without git provider.
- Name the app
- click on choose a file an upload the html file from your Local system (provided the code below copy from it)
- Note that it should be Zip file and Name the file index.html (make sure tha file below i provided make it into zip first) and then upload the file

##2. Set Up the AWS Lambda Function##
Lambda will handle the logic for adding user data into DynamoDB.

Steps:
- Create a new Lambda function in the AWS console.
- Go to AWS Lambda -> Create Function -> Author from Scratch.
- Define the function name, e.g., simpleLamdafunc (use whatever you want) we use PowerofMath.
- Choose a runtime (e.g., Node.js, Python, etc.). For this example, let's we use python (use the latest version).
- clikc on Create Function
- copy the code below and paste in the lamda function and save it with (ctrl+S)
- After that click Deploy to deploy the code
- After that Click Test and create a new event and name anything you like we use PowerOfmathTest and do private
- in Json formate change key,value to
  {
      'base':2,
      'exponent':3
  }
- After Test the result and look for result it should be 8
```
# import the JSON utility package
import json
# import the Python math library
import math

# import the AWS SDK (for Python the package name is boto3)
import boto3
# import two packages to help us with dates and date formatting
from time import gmtime, strftime

# create a DynamoDB object using the AWS SDK
dynamodb = boto3.resource('dynamodb')
# use the DynamoDB object to select our table
table = dynamodb.Table('PowerOfMathDatabase')
# store the current time in a human readable format in a variable
now = strftime("%a, %d %b %Y %H:%M:%S +0000", gmtime())

# define the handler function that the Lambda service will use an entry point
def lambda_handler(event, context):

# extract the two numbers from the Lambda service's event object
    mathResult = math.pow(int(event['base']), int(event['exponent']))

# write result and time to the DynamoDB table using the object we instantiated and save response in a variable
    response = table.put_item(
        Item={
            'ID': str(mathResult),
            'LatestGreetingTime':now
            })

# return a properly formatted JSON object
    return {
    'statusCode': 200,
    'body': json.dumps('Your result is ' + str(mathResult))
    }
```

##3. Set Up API Gateway to Expose Lambda Function##
API Gateway will allow external HTTP requests to trigger the Lambda function.

- Go to AWS API Gateway -> Create API -> REST API. (click build)
- Create a new API -> API name -> you can give any name, we use PowerOfMathApi.
- Be sure yout on resources and then click create method
- After that select method type should be POST
- Select Lamda fuction and below choose the lamdafunction that we created. (it show as arn: at first followed by your lamdafunc name) be sure the name is correct that you created in lamda
- In the API dashboard select the Enable CORS -> click on post option -> then click save (it Enable the intraction with different domains like other services)
- Click on Deploy API -> select new stage -> write a name of stage (any name) we go with dev.
- copy the Inovke URL -> something like this https://65cm6nx******* paste that url somewhere to retrive it later for the other services
- After that in in resourse -> click post -> click test, to test the its working in json formate write
   {
      'base':2,
      'exponent':3
   }
- Deploy the API to a new stage, e.g., dev, and get the invoke URL.

##2. Set Up the DynamoDB Table
To create database to store our values which gives the result from our function values 

- Go to AWS DynamoDB and create a table called PowerOfMathDatabase.
- Set the Partition Key as ID (String).
- Leave other options as default.
- click on Create table
- After that go to dashboard of dynamodb and in the overviwe tab -> under general information -> additional info -> copy the arn:*** to your notepad or somewhere that you can retrive it later.

- Go to Lamdafunction and go to the Configuration tab
