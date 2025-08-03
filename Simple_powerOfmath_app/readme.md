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

## ⚙️ What Do We Need?

To build and deploy our **PowerofMath** serverless application, we'll need:

1. ✅ A way to **create/host a webpage**.
2. ✅ A method to **invoke backend math functionality**.
3. ✅ Logic to **perform the math operations**.
4. ✅ A **data store** to save and retrieve math results.
5. ✅ A secure way to **handle permissions and access control**.

---

## 🚀 Step 1: Set Up AWS Amplify for Frontend Hosting

AWS Amplify simplifies static web hosting, integrates with backend services, and offers fast global deployment.

### 🛠️ Instructions

1. **Go to** the [AWS Amplify Console](https://console.aws.amazon.com/amplify/home).
2. Click **"Create App"** and choose **"Host Web App"**.
3. Choose **"Manual deployment"** since we're not using a Git provider (e.g., GitHub).
4. **Name your app** (e.g., `PowerofMathFrontend`).
5. Click **"Choose a file"**, then upload your local HTML code **as a ZIP file**.

> 📦 Make sure the ZIP file contains your `index.html` file at the root level.

### 📂 Example HTML File (`index.html`)

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

## ⚙️ Step 2: Set Up the AWS Lambda Function

AWS Lambda will handle the **backend logic** for computing the power of numbers and storing results in DynamoDB.

---

### 🛠️ Steps to Create the Lambda Function

1. **Navigate to the AWS Lambda Console**  
   Go to **AWS Console → Lambda → Create Function**.

2. **Create the function from scratch:**
   - Select **"Author from scratch"**
   - Set your function name (e.g., `PowerofMath`)
   - Choose a **runtime**:  
     > For this project, we use **Python** (choose the latest available version)

3. **Click "Create Function"**

4. After creation:
   - Scroll to the **Function code** section
   - **Replace the default code** with the following:
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

## 🌐 Step 3: Set Up API Gateway to Expose Lambda Function

API Gateway will allow external HTTP requests to trigger the Lambda function.

---

### 🛠️ Steps:

1. Go to **AWS API Gateway → Create API → REST API** (click **Build**).
2. Create a new API and give it a name — you can choose any name. In our case, we use **PowerOfMathApi**.
3. In the **Resources** section, click **Create Method**.
4. Choose the method type as **POST**.
5. Select **Lambda Function**, and from the dropdown, choose the Lambda function you created earlier.  
   (It will show as an ARN followed by your Lambda function name — make sure it’s correct.)
6. In the API dashboard, select **Enable CORS**:
   - Click on the **POST** option.
   - Then click **Save**.  
   (This enables interaction with different domains or services.)
7. Click **Deploy API**:
   - Select **"New Stage"**
   - Enter a stage name (any name works; we use **dev**).
8. Copy the **Invoke URL** — it should look something like:  
   `https://65cm6nx*******`  
   Save this URL somewhere for use in other services.
9. Go to the **Resources** tab:
   - Click **POST**
   - Click **Test**, and in the JSON input field, enter:  
     `{ 'base': 2, 'exponent': 3 }`

10. Deploy the API to a stage (e.g., `dev`) and keep the **Invoke URL** handy for the next steps.

---

> 📌 This completes your API setup, allowing your Lambda function to be triggered via HTTP POST requests.


## 🗄️ Step 4: Set Up the DynamoDB Table

We'll create a DynamoDB table to store the results returned from our Lambda function.

---

### 🛠️ Steps:

1. Go to **AWS DynamoDB** and click **Create Table**.
2. Name the table **PowerOfMathDatabase**.
3. Set the **Partition Key** as `ID` (Type: `String`).
4. Leave all other options as default.
5. Click **Create Table**.

---

### 📋 After Creation:

1. Go to the **DynamoDB dashboard**.
2. Under the **Overview** tab, scroll to **General Information** → **Additional Info**.
3. Copy the **ARN** (starts with `arn:aws:dynamodb:...`) and save it in your notepad or somewhere safe.  
   You’ll need this ARN in later steps to configure permissions or link services.

---

> 📌 Your DynamoDB table is now ready to store computation results!

## 🔐 Step 5: Set Up IAM Roles

We'll configure IAM permissions so that the Lambda function can interact securely with the DynamoDB table.

---

### 🛠️ Steps:

1. Go to your **Lambda function** in the AWS Console.
2. Navigate to the **Configuration** tab.
3. Click on **Permissions** → under **Execution Role**, click the attached role name  
   (e.g., `powerofmathfunc-role`).  
   This will open the IAM console.

4. In the IAM role page:
   - Click **Add permissions**
   - Select **Create inline policy**
   - Choose the **JSON** tab

5. Paste the following policy into the JSON editor:

   > ⚠️ Replace `"YOUR-TABLE-ARN"` with the actual ARN of your DynamoDB table you copied earlier.

   ```json
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
 

## 🔄 Step 6: Update the Lambda Function

Now that everything is connected, we’ll update the Lambda function to store the results in DynamoDB.

---

### 🛠️ Steps:

1. Go to your **Lambda function** in the AWS Console.
2. **Replace the existing code** with the new version provided below.
3. Click **Deploy** to apply the new code changes.
4. After deployment, click **Test**.
5. Once tested:
   - Go to **AWS DynamoDB**
   - Click on **Explore Table Items**
   - You should see a new item in the `PowerOfMathDatabase` table containing the result and timestamp.

✅ At this point, DynamoDB is successfully integrated and capturing the results from your Lambda function.

---

### 🧠 Lambda Function Code (Updated)

```python
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

# define the handler function that the Lambda service will use as an entry point
def lambda_handler(event, context):
    # extract the two numbers from the Lambda service's event object
    mathResult = math.pow(int(event['base']), int(event['exponent']))

    # write result and time to the DynamoDB table using the object we instantiated
    response = table.put_item(
        Item={
            'ID': str(mathResult),
            'LatestGreetingTime': now
        })

    # return a properly formatted JSON object
    return {
        'statusCode': 200,
        'body': json.dumps('Your result is ' + str(mathResult))
    }

  ```

## 🌐 Step 7: Update the Amplify Frontend

Now that our backend services are ready, we’ll update the frontend hosted on AWS Amplify to interact with the new API Gateway endpoint.

---

### 🛠️ Steps:

1. **Delete the Existing App:**
   - Go to the **Amplify Console**
   - Navigate to **App Settings → General Settings**
   - Click **Delete App**

2. **Recreate the App (Follow the Amplify Setup in Step 1):**
   - Choose **"Host Web App"**
   - Select **Manual Deployment**
   - Name your app
   - Upload a new ZIP file (see below)

3. **Update and Prepare the HTML:**
   - Copy the **updated code** (provided below)
   - Replace `"YOUR API GATEWAY ENDPOINT"` in the JavaScript `fetch()` function with your saved API Gateway invoke URL
   - Save the file as `index.html`
   - Zip the file before uploading to Amplify

4. **Upload the ZIP File to Amplify:**
   - Use the file upload dialog in Amplify setup
   - After deployment, open your **Amplify domain URL**
   - Test the app to ensure functionality — results should appear via a popup on the web page

---

### 🧾 HTML Code (Updated UI with API Integration)

```html
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
        var callAPI = (base, exponent) => {
            var myHeaders = new Headers();
            myHeaders.append("Content-Type", "application/json");

            var raw = JSON.stringify({ "base": base, "exponent": exponent });

            var requestOptions = {
                method: 'POST',
                headers: myHeaders,
                body: raw,
                redirect: 'follow'
            };

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
        <button type="button" onclick="callAPI(document.getElementById('base').value, document.getElementById('exponent').value)">CALCULATE</button>
    </form>
</body>
</html>

```

## 🛑 Final Step: Shut Down AWS Resources

Once you've finished testing and demonstrating the application, **make sure to shut down all the AWS resources** you created during this project.

> ⚠️ **Reminder:** AWS services can incur charges even when idle.  
If you leave them running, you may be billed unexpectedly.

---

### ✅ Services to Shut Down:

- Delete the **Amplify app**
- Remove the **API Gateway**
- Delete the **Lambda function**
- Delete the **DynamoDB table**
- Remove or detach the **IAM roles/policies**

---

## ✅ Conclusion

Here’s what we accomplished in this project:

- ✅ **A way to create/host a webpage** → using **AWS Amplify**
- ✅ **A way to invoke math functionality** → using **API Gateway**
- ✅ **A way to perform math operations** → using **AWS Lambda**
- ✅ **A way to store and retrieve results** → using **DynamoDB**
- ✅ **A way to manage secure permissions** → using **IAM roles and policies**

---

> 🎉 You’ve successfully built and deployed a fully serverless, scalable, and interactive web application using AWS!

