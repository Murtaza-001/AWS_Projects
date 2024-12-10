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
- Note that it should be Zip file and Name the file index.html (make sure tha file below i provided make it into zip first) and then upload the file

```
<html><!-- 1 -->
<head> <!-- 2 -->
    <meta charset="UTF-8"> <!-- 3 -->
    <title>To the Power of Math!</title> <!-- 4 -->
    <!-- Styling for the client UI --> <!-- 5 -->
    <style> <!-- 6 -->
    h1 { <!-- 7 -->
        color: #FFFFFF; <!-- 8 -->
        font-family: system-ui; <!-- 9 -->
        margin-left: 20px; <!-- 10 -->
        } <!-- 11 -->
    body { <!-- 12 -->
        background-color: #222629; <!-- 13 -->
        } <!-- 14 -->
    label { <!-- 15 -->
        color: #86C232; <!-- 16 -->
        font-family: system-ui; <!-- 17 -->
        font-size: 20px; <!-- 18 -->
        margin-left: 20px; <!-- 19 -->
        margin-top: 20px; <!-- 20 -->
        } <!-- 21 -->
     button { <!-- 22 -->
        background-color: #86C232; <!-- 23 -->
        border-color: #86C232; <!-- 24 -->
        color: #FFFFFF; <!-- 25 -->
        font-family: system-ui; <!-- 26 -->
        font-size: 20px; <!-- 27 -->
        font-weight: bold; <!-- 28 -->
        margin-left: 30px; <!-- 29 -->
        margin-top: 20px; <!-- 30 -->
        width: 140px; <!-- 31 -->
        } <!-- 32 -->
     input { <!-- 33 -->
        color: #222629; <!-- 34 -->
        font-family: system-ui; <!-- 35 -->
        font-size: 20px; <!-- 36 -->
        margin-left: 10px; <!-- 37 -->
        margin-top: 20px; <!-- 38 -->
        width: 100px; <!-- 39 -->
        } <!-- 40 -->
    </style> <!-- 41 -->
    <script> <!-- 42 -->
        // callAPI function that takes the base and exponent numbers as parameters <!-- 43 -->
        var callAPI = (base, exponent) => { <!-- 44 -->
            // instantiate a headers object <!-- 45 -->
            var myHeaders = new Headers(); <!-- 46 -->
            // add content type header to object <!-- 47 -->
            myHeaders.append("Content-Type", "application/json"); <!-- 48 -->
            // using built in JSON utility package turn object to string and store in a variable <!-- 49 -->
            var raw = JSON.stringify({"base": base, "exponent": exponent}); <!-- 50 -->
            // create a JSON object with parameters for API call and store in a variable <!-- 51 -->
            var requestOptions = { <!-- 52 -->
                method: 'POST', <!-- 53 -->
                headers: myHeaders, <!-- 54 -->
                body: raw, <!-- 55 -->
                redirect: 'follow' <!-- 56 -->
            }; <!-- 57 -->
            // make API call with parameters and use promises to get response <!-- 58 -->
            fetch("https://x6nyho350c.execute-api.ap-south-1.amazonaws.com/dev", requestOptions) <!-- 59 -->
            .then(response => response.text()) <!-- 60 -->
            .then(result => alert(JSON.parse(result).body)) <!-- 61 -->
            .catch(error => console.log('error', error)); <!-- 62 -->
        } <!-- 63 -->
    </script> <!-- 64 -->
</head> <!-- 65 -->
<body> <!-- 66 -->
    <h1>TO THE POWER OF MATH!</h1> <!-- 67 -->
    <form> <!-- 68 -->
        <label>Base number:</label> <!-- 69 -->
        <input type="text" id="base"> <!-- 70 -->
        <label>...to the power of:</label> <!-- 71 -->
        <input type="text" id="exponent"> <!-- 72 -->
        <!-- set button onClick method to call function we defined passing input values as parameters --> <!-- 73 -->
        <button type="button" onclick="callAPI(document.getElementById('base').value,document.getElementById('exponent').value)">CALCULATE</button> <!-- 74 -->
    </form> <!-- 75 -->
</body> <!-- 76 -->
</html> <!-- 77 -->

