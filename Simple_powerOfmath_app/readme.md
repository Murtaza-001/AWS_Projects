# 🧮 PowerofMath Web Application with AWS

Welcome to the **PowerofMath** web application — a lightweight, scalable, and fully serverless app built with modern AWS services. This project demonstrates how to architect a cloud-native solution using key AWS tools for compute, storage, security, and deployment.

---

## 📘 Project Description

This project showcases the implementation of a simple web application that calculates the power of a number. The backend logic, database storage, and front-end deployment are handled entirely through AWS services, offering scalability, cost-efficiency, and ease of maintenance.

---

## 🧱 Integrated AWS Services

- **Amazon DynamoDB**  
  A fast, scalable NoSQL database used for storing input/output logs and computation history.

- **AWS Lambda**  
  Executes backend functions on demand without provisioning or managing servers.

- **Amazon API Gateway**  
  Facilitates RESTful communication between the frontend and Lambda functions with built-in security and scaling.

- **AWS IAM (Identity and Access Management)**  
  Manages secure access to resources via fine-grained permissions and role-based access control.

- **AWS Amplify**  
  Simplifies front-end hosting and integrates smoothly with AWS backend services, enabling CI/CD and fast global content delivery.

---

## ✨ Key Features

- ✅ Fully **serverless architecture** with zero server management.
- 🚀 **Auto-scalable** and **cost-effective** infrastructure.
- ⚡ **Low-latency** data access with Amazon DynamoDB.
- 🔐 **Secure API endpoints** using IAM policies and roles.
- 🌐 **Modern frontend deployment** with AWS Amplify.
- 📊 Cloud-native app that highlights **efficiency, resilience, and modular design**.

---

## 🧠 What You’ll Learn

- How to integrate AWS Lambda and API Gateway for serverless computing.
- Using DynamoDB for fast and scalable NoSQL data storage.
- Implementing IAM roles and policies for secure resource access.
- Hosting and deploying modern web apps with AWS Amplify.
- End-to-end development using AWS services with no server overhead.

---

## Architecture Diagram
![awsSchema1](https://github.com/user-attachments/assets/cbee69d2-53db-4abc-8f81-b59550baa97f)

## What do we need?
- A way to create/ host a webpage
- A way to invoke math functionality
- A way to do math
- Somewhere to store/return the math results
- A way to handle permissions

## 1. Set up AWS Amplify for Frontend##
AWS Amplify will help with the frontend setup, including hosting and authentication (if required).

## Create an Amplify Project: (better to do this step at the Last)

- Open the AWS Amplify Console and click "Create App".
- Choose "Host Web" as the platform.
- Connect your repository (GitHub, GitLab, Bitbucket, or manually).
- so we are going with manually
- so choose without git provider.
- Name the app
- click on choose a file an upload the html file from your Local system (provided the code below copy from it)
- Note that it should be Zip file and Name the file index.html (make sure tha file below i provided make it into zip first) and then upload the file
- After all done check for Domain url in the amplify and click on domain to check the html website that we uploaded.
- We will update the code later in the following steps to fully laverage the power of all the services used in this project..

  ```
  <!DOCTYPE html>
  <html>
  <head>
      <meta charset="UTF-8">
      <title>To the Power of Math!</title>
  </head>
  
  <body>
      To the Power of Math!
  </body>
  </html>
  ```

## 2. Set Up the AWS Lambda Function##
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
 `` {
      "base":2,
      "exponent":3
    }
  ``
- After Test the result and look for result it should be "8" you play with different values for result.
```
# import the JSON utility package
import json
# import the Python math library
import math

# define the handler function that the Lambda service will use an entry point
def lambda_handler(event, context):

# extract the two numbers from the Lambda service's event object
    mathResult = math.pow(int(event['base']), int(event['exponent']))

    # return a properly formatted JSON object
    return {
    'statusCode': 200,
    'body': json.dumps('Your result is ' + str(mathResult))
    }
```

## 3. Set Up API Gateway to Expose Lambda Function##
API Gateway will allow external HTTP requests to trigger the Lambda function.

- Go to AWS API Gateway -> Create API -> REST API. (click build)
- Create a new API -> API name -> you can give any name, we use PowerOfMathApi.
- Be sure yout on resources and then click create method
- After that select method type should be POST
- Select Lamda fuction and below choose the lamdafunction that we created. (it show as arn: at first followed by your lamdafunc name) be sure the name is correct that you created in lamda
- In the API dashboard select the Enable CORS -> click on post option -> then click save (it Enable the intraction with different domains like other services)
- Click on Deploy API -> select new stage -> write a name of stage (any name) we go with dev.
- copy the Inovke URL -> something like this -https://65cm6nx******* paste that url somewhere to retrive it later for the other services
- After that in in resourse -> click post -> click test, to test the its working in json formate write
   {
      'base':2,
      'exponent':3
   }
- Deploy the API to a new stage, e.g., dev, and get the invoke URL.

## 4. Set Up the DynamoDB Table
To create database to store our values which gives the result from our function values 

- Go to AWS DynamoDB and create a table called PowerOfMathDatabase.
- Set the Partition Key as ID (String).
- Leave other options as default.
- click on Create table
- After that go to dashboard of dynamodb and in the overviwe tab -> under general information -> additional info -> copy the arn:*** to your notepad or somewhere that you can retrive it later.

## 5. Set up IAM Roles

- Go to Lamdafunction and go to the Configuration tab
- go to Permissions -> go to execution role -> click the powerofmathfunc role
-  it will take to Iam console.
-  click add permission -> click the inline policy -> click on json and paste the code given below
  ```
   {
"Version": "2012-10-17",
"Statement": [
    {
        "Sid": "VisualEditor0",
        "Effect": "Allow",
        "Action": [
            "dynamodb:PutItem",
            "dynamodb:DeleteItem",
            "dynamodb:GetItem",
            "dynamodb:Scan",
            "dynamodb:Query",
            "dynamodb:UpdateItem"
        ],
        "Resource": "YOUR-TABLE-ARN"
    }
    ]
}
```
- Copy your arn name in "Resource:" of your Dynamodb table in console you find or where you saved at as we saved earlier.
- It will give the specific permission of dynamodb to the lamda funcion to performe the given actions
- After that click reviwe policy -> name the policy we name it PowerofMathpolicy -> click create policy

## 6. update the Lamda funcion

- Go to lamda function and update the code with the new one which is given below, copy and paste it into your function
- After that click on deploy for deployment of code
- Then test it.
- Then check the Dynamodb table -> go to console of dynamodb -> click on Explore table items -> then you see a table created with result.
- All set for dynamodb

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

## 6. update the Amplyfy
- We have to update the code as we mention in the first step.
- To update we have to delete the existing app
- In the amplify console go to app settings -> General settings -> Delete the app
- Create new app as follow the first steps of the amplify in the above of this doc
- Make sure to zip your file
- Copy the updated code from below and make it into zip file
- Before Uploading the code to amplify make sure to copy your "YOUR API GATEWAY ENDPOIN" that you saved in file or somewhere
- In the Script tag in code given below -> check for 'fetch()' variable and in that 'your api gateway' paste your apigateway_endpoint
- After all done go to the amplify domain url and test it out to see the results, in pop-up script in the web page
  
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>To the Power of Math!</title>
    <!-- Styling for the client UI -->
    <style>
    h1 {
        color: #FFFFFF;
        font-family: system-ui;
		margin-left: 20px;
        }
	body {
        background-color: #222629;
        }
    label {
        color: #86C232;
        font-family: system-ui;
        font-size: 20px;
        margin-left: 20px;
		margin-top: 20px;
        }
     button {
        background-color: #86C232;
		border-color: #86C232;
		color: #FFFFFF;
        font-family: system-ui;
        font-size: 20px;
		font-weight: bold;
        margin-left: 30px;
		margin-top: 20px;
		width: 140px;
        }
	 input {
        color: #222629;
        font-family: system-ui;
        font-size: 20px;
        margin-left: 10px;
		margin-top: 20px;
		width: 100px;
        }
    </style>
    <script>
        // callAPI function that takes the base and exponent numbers as parameters
        var callAPI = (base,exponent)=>{
            // instantiate a headers object
            var myHeaders = new Headers();
            // add content type header to object
            myHeaders.append("Content-Type", "application/json");
            // using built in JSON utility package turn object to string and store in a variable
            var raw = JSON.stringify({"base":base,"exponent":exponent});
            // create a JSON object with parameters for API call and store in a variable
            var requestOptions = {
                method: 'POST',
                headers: myHeaders,
                body: raw,
                redirect: 'follow'
            };
            // make API call with parameters and use promises to get response
            fetch("YOUR API GATEWAY ENDPOINT", requestOptions)
            .then(response => response.text())
            .then(result => alert(JSON.parse(result).body))
            .catch(error => console.log('error', error));
        }
    </script>
</head>
<body>
    <h1>TO THE POWER OF MATH!</h1>
	<form>
        <label>Base number:</label>
        <input type="text" id="base">
        <label>...to the power of:</label>
        <input type="text" id="exponent">
        <!-- set button onClick method to call function we defined passing input values as parameters -->
        <button type="button" onclick="callAPI(document.getElementById('base').value,document.getElementById('exponent').value)">CALCULATE</button>
    </form>
</body>
</html>
```

### After completing all tasks, be sure to shut down your resources.
### Shut down All your AWS services which you created it along this project, It will cost you if left open even if you not use.


# Conclusion
 ## so what we done
- A way to create/ host a webpage ( with amplify)
- A way to invoke math functionality (with API_GATEWAY)
- A way to do math ( with Lamda function )
- Somewhere to store/return the math results ( with DynamoDB )
- A way to handle permissions ( With IAM User )

