  Building a Serverless Web Application with AWS Amplify, Lambda, API Gateway, DynamoDB, and IAM Roles


![awsSchema1](https://github.com/user-attachments/assets/4f6f05ba-853d-43d1-b9e4-a70d133cc01f)


Overview of the Architecture:
AWS Amplify will serve as the frontend service and handle authentication, API interactions, and hosting.
AWS Lambda will handle backend logic and interact with DynamoDB to read/write data.
API Gateway will expose HTTP endpoints for your frontend to interact with Lambda.
DynamoDB will store the data.
IAM will ensure Lambda has permissions to access DynamoDB.


1. Set Up AWS Amplify to Host the Frontend
AWS Amplify simplifies the process of deploying and hosting your frontend.

Steps:
1. Go to the AWS Amplify Console.
2. Click Get Started under the "Host web app" section.
3. Choose GitHub or another repository provider if you have your frontend in a version control system. If you don't have a repository, you can deploy directly from 
   the console by uploading your frontend files later.
4. After selecting your repository, connect your frontend app and configure the build settings (Amplify will automatically detect the build configuration for most common frameworks).
5. Click Save and Deploy to start the deployment process.


2. Create a Lambda Function
Now, let’s create a Lambda function that will process requests from API Gateway.

Steps:
1. Go to the AWS Lambda Console.
2. Click Create function.
3. Choose Author from Scratch.
4. Provide a Function name (e.g., CreateItemFunction).
5. Set Runtime to Node.js or Python (depending on your preference for writing Lambda functions).
   Click Create function.

   ```
   const AWS = require('aws-sdk');
   const dynamoDB = new AWS.DynamoDB.DocumentClient();

   exports.handler = async (event) => {
   const { httpMethod, path, body } = event;

    if (httpMethod === 'POST' && path === '/createItem') {
        const item = JSON.parse(body);
        const params = {
            TableName: 'YourDynamoDBTable',
            Item: item,
        };
        
        try {
            await dynamoDB.put(params).promise();
            return {
                statusCode: 200,
                body: JSON.stringify({ message: 'Item created successfully!' }),
            };
        } catch (error) {
            return {
                statusCode: 500,
                body: JSON.stringify({ message: 'Failed to create item', error }),
            };
        }
    }

    return {
        statusCode: 400,
        body: JSON.stringify({ message: 'Invalid request' }),
    };
   };



3. Create an API Gateway
Next, create an API Gateway to expose the Lambda function as a RESTful API.

Steps:
1. Go to the Amazon API Gateway Console.
2. Click Create API.
3. Select REST API and click Build.
4. In the API creation screen, choose New API, and provide an API name (e.g., CreateItemAPI).
5. Click Create API.

Create a Resource and Method:
1. Under Resources, click Actions and select Create Resource.
2. Name the resource /createItem and click Create Resource.
3. With the /createItem resource selected, click Actions and select Create Method.
4. Choose POST and click the checkmark.
5. In the Integration type, select Lambda Function and check Use Lambda Proxy integration.
6. In the Lambda Function field, type the name of your Lambda function (CreateItemFunction), and click Save. You’ll be prompted to give API Gateway permission to 
   invoke your Lambda function, click OK.
