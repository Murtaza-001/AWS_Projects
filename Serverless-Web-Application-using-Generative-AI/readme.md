# Serverless Web Application using Generative AI
In this Project you will use AWS Amplify to build a serverless web application powered by Generative AI using Amazon Bedrock and the Claude 3 Sonnet foundation model. Users can enter a list of ingredients, and the application will generate delicious recipes based on the input ingredients. The application includes an HTML-based user interface for ingredient submission and a backend web app to request AI-generated recipes.

## What you will accomplish
- Configure AWS Amplify to host your frontend application with continuous deployment built in
- Configure Amplify Auth and enable Amazon Bedrock foundation model Access
- Build an app backend for handling requests for your web application
- Use Amplify Data to call the serverless backend
- Connect the app to the backend

  ## Application Architecture
  ![serveless_ai_app-removebg-preview](https://github.com/user-attachments/assets/da4cdc2c-81fd-4519-b286-db547ad59f14)


## Tasks
 - Host a Static Website: Configure AWS Amplify to host your frontend application with continuous deployment built in
 - Manage Users: Configure Amplify Auth and enable Amazon Bedrock foundation model Access.
 -  Build a Serverless Backend: Build an app backend for handling requests for your web application
 - Call the API from the Static Website: Use Amplify Data to call the serverless backend
 - Build the frontend: Connect the app to the backend
 - Clean up Resources: Clean up the resources used in this tutorial

 ## Task 1: Host a Static Website
   you will start by creating a new React application and pushing it to a GitHub repository. You will then connect the repository to AWS Amplify web hosting and 
   deploy it to a globally available content delivery network (CDN) hosted on an amplifyapp.com domain. 

**Implementation**

 **Steps 1: create a React App**
  - In a new terminal or command line window of your local system, run the following command to use Vite to create a React application:
  - Make sure the node is installed on your system.
    
   ```
    npm create vite@latest ai-recipe-generator -- --template react-ts -y
    cd ai-recipe-generator
    npm install
    npm run dev
   ```
  - Write the commands one by one for ease, or use && character after each command in single line.
  - In the terminal window, select and open the Local link to view the Vite + React application.
     
  **step 2: Initilize the Github repo**
  - create a GitHub repository and commit your code to the repository. You will need a GitHub account to complete this step, if you do not have an      
     account, creaet one.
  - Start a new repository in your github
  - For Repository name, enter ai-recipe-generator, and choose the Public radio button.
  - Then select, Create a new repository
  - Open a new terminal window, navigate to your projects root folder (ai-recipe-generator), and run the following commands to initialize a git and push of the 
     application to the new GitHub repo:
     ```
    git init
    git add .
    git commit -m "first commit"
    git remote add origin git@github.com:<your-username>/ai-recipe-generator.git
    git branch -M main
    git push -u origin main
     ```
  - this will push your code to your github repo

  **step 3: installing amplify packages**
  - Open a new terminal window, navigate to your app's root folder (ai-recipe-generator), and run the following command:
  - `npm create amplify@latest -y`
  -  Running the previous command will scaffold a lightweight Amplify project in the app’s directory.
  -  In your terminal window, run the following commands to push the changes to GitHub:
  ```
    git add .
    git commit -m 'installing amplify'
    git push origin main
  ```
   **step 4: connect the GitHub repository**
   - Sign in to the AWS Management console in a new browser window, and open the AWS Amplify console at https://console.aws.amazon.com/amplify/apps.
   - Choose Create new app.
   - On the Start building with Amplify page, for Deploy your app, select GitHub, and select Next.
   -  When prompted, authenticate with GitHub. You will be automatically redirected back to the Amplify console. Choose the repository and main branch you created 
       earlier. Then select Next.
   - Leave the default build settings, and select Next.
   -  Review the inputs selected, and choose Save and deploy.
   - AWS Amplify will now build your source code and deploy your app at https://...amplifyapp.com, and on every git push your deployment instance will update. It 
     may take up to 5 minutes to deploy your app.
 

   
 ## Task 2: Manage Users
   In this task, you will configure Amplify Auth and you will enable Amazon Bedrock foundation model access.

 **step 1: setup Amplify Auth**
   The app uses email as the default login mechanism. When the users sign up, they receive a verification email. In this step, you will customize the verification 
   email that is sent to users.

   - On your local machine, navigate to the ai-generated-recipes/amplify/auth/resource.ts file and update it with the following code. Then, save the file.
   ```
   import { defineAuth } from "@aws-amplify/backend";

    export const auth = defineAuth({
      loginWith: {
        email: {
          verificationEmailStyle: "CODE",
          verificationEmailSubject: "Welcome to the AI-Powered Recipe Generator!",
          verificationEmailBody: (createCode) =>
            `Use this code to confirm your account: ${createCode()}`,
        },
      },
    });
   ```
   **step 2: Amazon Bedrock Model Access**
   Amazon Bedrock enables users to request access to a variety of Generative AI models. In this project, you will need access to Claude 3 Sonnet from Anthropic.

   -  Sign in to the AWS Management console in a new browser window, and open the AWS Amazon Bedrock console at  https://console.aws.amazon.com/bedrock/. 
      Verify that you are in the N. Virginia us-east-1 region, and choose Get started.
   - In the Foundation models section, choose the Claude model.
   - Scroll down to the Claude models section, and choose the Claude 3 Sonnet tab, and select Request model access.
   - In the Base models section, for Claude 3 Sonnet, choose Available to request, and select Request model access.
   - On the Edit model access page, choose Next.
   - On the Review and Submit page, choose Submit.

   - You have configured Amplify for authentication and customized the verification email, and enabled access to Amazon Bedrock and Claude 3 Sonnet.

  ## Task 3: Build a Serverless Backend
  In this task, you will use AWS Amplify and AWS Lambda to build a serverless function.

  In this task, you will configure a serverless function using AWS Amplify and AWS Lambda. This function takes an input parameter i.e. ingredients to generate a 
  prompt. It then sends this prompt to Amazon Bedrock via an HTTP POST request to the Claude 3 Sonnet model. The body of the request includes the prompt string 
  within a messages array.

  **step 1: Create a Lamda function**
    - On your local machine, navigate to the ai-recipe-generator/amplify/data folder, and create a file named bedrock.js.
    - Then, update the file with the following code:
    - The following code defines a request function that constructs the HTTP request to invoke the Claude 3 Sonnet foundation model in Amazon Bedrock. The 
        response function parses the response and returns the generated recipe.
       
    ```
    export function request(ctx) {
    const { ingredients = [] } = ctx.args;
  
    // Construct the prompt with the provided ingredients
    const prompt = `Suggest a recipe idea using these ingredients: ${ingredients.join(", ")}.`;
  
    // Return the request configuration
    return {
      resourcePath: `/model/anthropic.claude-3-sonnet-20240229-v1:0/invoke`,
      method: "POST",
      params: {
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({
          anthropic_version: "bedrock-2023-05-31",
          max_tokens: 1000,
          messages: [
            {
              role: "user",
              content: [
                {
                  type: "text",
                  text: `\n\nHuman: ${prompt}\n\nAssistant:`,
                },
              ],
            },
          ],
        }),
      },
     };
    }
  
    export function response(ctx) {
      // Parse the response body
      const parsedBody = JSON.parse(ctx.result.body);
      // Extract the text content from the response
      const res = {
        body: parsedBody.content[0].text,
      };
      // Return the response
      return res;
    }
    ```

**step 2: Add Amazone Bedrock**
  - Update the amplify/backend.ts file with the following code. Then, save the file.
  - The code adds an HTTP data source for Amazon Bedrock to your API and grant it permissions to invoke the Claude model.
  
    ```
    import { defineBackend } from "@aws-amplify/backend";
    import { data } from "./data/resource";
    import { PolicyStatement } from "aws-cdk-lib/aws-iam";
    import { auth } from "./auth/resource";
    
    const backend = defineBackend({
      auth,
      data,
    });
    
    const bedrockDataSource = backend.data.resources.graphqlApi.addHttpDataSource(
      "bedrockDS",
      "https://bedrock-runtime.us-east-1.amazonaws.com",
      {
        authorizationConfig: {
          signingRegion: "us-east-1",
          signingServiceName: "bedrock",
        },
      }
    );
    
    bedrockDataSource.grantPrincipal.addToPrincipalPolicy(
      new PolicyStatement({
        resources: [
          "arn:aws:bedrock:us-east-1::foundation-model/anthropic.claude-3-sonnet-20240229-v1:0",
        ],
        actions: ["bedrock:InvokeModel"],
        
      })
    );
    ```
   
    
