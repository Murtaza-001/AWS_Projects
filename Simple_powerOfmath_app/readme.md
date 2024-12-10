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
1. <html>
2. <head>
3.     <meta charset="UTF-8">
4.     <title>To the Power of Math!</title>
5.     <!-- Styling for the client UI -->
6.     <style>
7.     h1 {
8.         color: #FFFFFF;
9.         font-family: system-ui;
10.         margin-left: 20px;
11.         }
12.     body {
13.         background-color: #222629;
14.         }
15.     label {
16.         color: #86C232;
17.         font-family: system-ui;
18.         font-size: 20px;
19.         margin-left: 20px;
20.         margin-top: 20px;
21.         }
22.     button {
23.         background-color: #86C232;
24.         border-color: #86C232;
25.         color: #FFFFFF;
26.         font-family: system-ui;
27.         font-size: 20px;
28.         font-weight: bold;
29.         margin-left: 30px;
30.         margin-top: 20px;
31.         width: 140px;
32.         }
33.     input {
34.         color: #222629;
35.         font-family: system-ui;
36.         font-size: 20px;
37.         margin-left: 10px;
38.         margin-top: 20px;
39.         width: 100px;
40.         }
41. </style>
42. <script>
43.     // callAPI function that takes the base and exponent numbers as parameters
44.     var callAPI = (base, exponent) => {
45.         // instantiate a headers object
46.         var myHeaders = new Headers();
47.         // add content type header to object
48.         myHeaders.append("Content-Type", "application/json");
49.         // using built in JSON utility package turn object to string and store in a variable
50.         var raw = JSON.stringify({"base": base, "exponent": exponent});
51.         // create a JSON object with parameters for API call and store in a variable
52.         var requestOptions = {
53.             method: 'POST',
54.             headers: myHeaders,
55.             body: raw,
56.             redirect: 'follow'
57.         };
58.         // make API call with parameters and use promises to get response
59.         fetch("https://x6nyho350c.execute-api.ap-south-1.amazonaws.com/dev", requestOptions)
60.         .then(response => response.text())
61.         .then(result => alert(JSON.parse(result).body))
62.         .catch(error => console.log('error', error));
63.     }
64. </script>
65. </head>
66. <body>
67.     <h1>TO THE POWER OF MATH!</h1>
68.     <form>
69.         <label>Base number:</label>
70.         <input type="text" id="base">
71.         <label>...to the power of:</label>
72.         <input type="text" id="exponent">
73.         <!-- set button onClick method to call function we defined passing input values as parameters -->
74.         <button type="button" onclick="callAPI(document.getElementById('base').value,document.getElementById('exponent').value)">CALCULATE</button>
75.     </form>
76. </body>
77. </html>


