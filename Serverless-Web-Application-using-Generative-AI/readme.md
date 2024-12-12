# Serverless Web Application using Generative AI
In this Project you will use AWS Amplify to build a serverless web application powered by Generative AI using Amazon Bedrock and the Claude 3 Sonnet foundation model. Users can enter a list of ingredients, and the application will generate delicious recipes based on the input ingredients. The application includes an HTML-based user interface for ingredient submission and a backend web app to request AI-generated recipes.

## What you will accomplish
- Configure AWS Amplify to host your frontend application with continuous deployment built in
- Configure Amplify Auth and enable Amazon Bedrock foundation model Access
- Build an app backend for handling requests for your web application
- Use Amplify Data to call the serverless backend
- Connect the app to the backend

  ## Application Architecture
  ![ai_aws_app](https://github.com/user-attachments/assets/2c629fb6-b958-4223-8d8a-947b64772238)



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
   
 - You have defined a Lambda function using Amplify, and added Amazon Bedrock as an HTTP data source.

## Task 4: Deploy the Backend API
In this task, you will use AWS Amplify to configure a custom data query to use Amazon Bedrock as data source.

In this task, you will configure a custom query that will reference the data source and the resolver you defined the previous model to produce a recipe based on a list of ingredients. This query will use a custom type to structure the response from Amazon Bedrock. 

**step 1: setup amplify data**
- On your local machine, navigate to the ai-recipe-generator/amplify/data/resource.ts file, and update it with the following code. Then, save the file.
- The following code defines the askBedrock query that takes an array of strings called ingredients and returns a BedrockResponse. The .handler(a.handler.custom({ 
  entry: "./bedrock.js", dataSource: "bedrockDS" })) line sets up a custom handler for this query, defined in bedrock.js, using bedrockDS as its data source.

```
  import { type ClientSchema, a, defineData } from "@aws-amplify/backend";
  
  const schema = a.schema({
    BedrockResponse: a.customType({
      body: a.string(),
      error: a.string(),
    }),
  
    askBedrock: a
      .query()
      .arguments({ ingredients: a.string().array() })
      .returns(a.ref("BedrockResponse"))
      .authorization((allow) => [allow.authenticated()])
      .handler(
        a.handler.custom({ entry: "./bedrock.js", dataSource: "bedrockDS" })
      ),
  });
  
  export type Schema = ClientSchema<typeof schema>;
  
  export const data = defineData({
    schema,
    authorizationModes: {
      defaultAuthorizationMode: "apiKey",
      apiKeyAuthorizationMode: {
        expiresInDays: 30,
      },
    },
  });

```
- Open a new terminal window, navigate to your apps project folder (ai-recipe-generator), and run the following command to deploy cloud resources into an isolated 
  development space so you can iterate fast.
- `npx ampx sandbox`
- Once the cloud sandbox has been fully deployed, your terminal will display a confirmation message and the amplify_outputs.json file will be generated and added 
  to your project.

- You have configured a GraphQL API to define a custom query to connect to Amazon Bedrock and generate recipes based on a list of ingredients.


##  Task 5: Build the Frontend
In this task, you will create an app frontend and connect it to the cloud backend you have already built.

In this task, you will update the website you created in module one to use the Amplify UI component library to scaffold out an entire user authentication flow, allowing users to sign up, sign in, and reset their password and invoke the GraphQL API to use the customer query for generating recipe based on a list of ingredients.

**step 1: install amplify libraries**
 - Open a new terminal window, navigate to your projects root folder (ai-recipe-generator), and run the following command to install the libraries.
 - `npm install aws-amplify @aws-amplify/ui-react`

 **step 2: style app ui**
 -  On your local machine, navigate to the ai-recipe-generator/src/index.css file, and update it with the following code to center the App UI. Then, save the file
```
    :root {
      font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
      line-height: 1.5;
      font-weight: 400;
    
      color: rgba(255, 255, 255, 0.87);
    
      font-synthesis: none;
      text-rendering: optimizeLegibility;
      -webkit-font-smoothing: antialiased;
      -moz-osx-font-smoothing: grayscale;
    
      max-width: 1280px;
      margin: 0 auto;
      padding: 2rem;
    
    }
    
    .card {
      padding: 2em;
    }
    
    .read-the-docs {
      color: #888;
    }
    
    .box:nth-child(3n + 1) {
      grid-column: 1;
    }
    .box:nth-child(3n + 2) {
      grid-column: 2;
    }
    .box:nth-child(3n + 3) {
      grid-column: 3;
    }
```
 - Update the src/App.css file with the following code to style the ingredients form. Then, save the file.
  ```
    .app-container {
    
      margin: 0 auto;
      padding: 20px;
      text-align: center;
    }
    
    .header-container {
      padding-bottom: 2.5rem;
      margin:  auto;
      text-align: center;
    
      align-items: center;
      max-width: 48rem;
      
      
    }
    
    .main-header {
      font-size: 2.25rem;
      font-weight: bold;
      color: #1a202c;
    }
    
    .main-header .highlight {
      color: #2563eb;
    }
    
    @media (min-width: 640px) {
      .main-header {
        font-size: 3.75rem;
      }
    }
    
    .description {
    
      font-weight: 500;
      font-size: 1.125rem;
      max-width: 65ch;
      color: #1a202c;
    }
    
    .form-container {
      margin-bottom: 20px;
    }
    
    .search-container {
      display: flex;
      flex-direction: column;
      gap: 10px;
      align-items: center;
    }
    
    .wide-input {
      width: 100%;
      padding: 10px;
      font-size: 16px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }
    
    .search-button {
      width: 100%; /* Make the button full width */
      max-width: 300px; /* Set a maximum width for the button */
      padding: 10px;
      font-size: 16px;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    
    .search-button:hover {
      background-color: #0056b3;
    }
    
    .result-container {
      margin-top: 20px;
      transition: height 0.3s ease-out;
      overflow: hidden;
    }
    
    .loader-container {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 10px;
    }
    
    .result {
      background-color: #f8f9fa;
      border: 1px solid #e9ecef;
      border-radius: 4px;
      padding: 15px;
      white-space: pre-wrap;
      word-wrap: break-word;
      color: black;
      font-weight: bold;
      text-align: left; /* Align text to the left */
    }
  ```

**step 3: Implement UI**
 - On your local machine, navigate to the ai-recipe-generator/src/main.tsx file, and update it with the following code. Then, save the file.
 - The code will use the Amplify Authenticator component to scaffold out an entire user authentication flow allowing users to sign up, sign in, reset their 
   password, and confirm sign-in for multifactor authentication (MFA).
```
  import React from "react";
  import ReactDOM from "react-dom/client";
  import App from "./App.jsx";
  import "./index.css";
  import { Authenticator } from "@aws-amplify/ui-react";
  
  ReactDOM.createRoot(document.getElementById("root")!).render(
    <React.StrictMode>
      <Authenticator>
        <App />
      </Authenticator>
    </React.StrictMode>
  );
```
 - Open the ai-recipe-generator/src/App.tsx file, and update it with the following code. Then, save the file.
 - The code starts by configuring the Amplify library with the client configuration file (amplify_outputs.json). It then generates a data client using the 
   generateClient() function. The app presents a form to users for submitting a list of ingredients. Once submitted, it will use the data client to pass the list 
   to the askBedrock query and retrieve the generated recipe then display it to the user.
```
    import { FormEvent, useState } from "react";
    import { Loader, Placeholder } from "@aws-amplify/ui-react";
    import "./App.css";
    import { Amplify } from "aws-amplify";
    import { Schema } from "../amplify/data/resource";
    import { generateClient } from "aws-amplify/data";
    import outputs from "../amplify_outputs.json";
    
    
    import "@aws-amplify/ui-react/styles.css";
    
    Amplify.configure(outputs);
    
    const amplifyClient = generateClient<Schema>({
      authMode: "userPool",
    });
    
    function App() {
      const [result, setResult] = useState<string>("");
      const [loading, setLoading] = useState(false);
    
      const onSubmit = async (event: FormEvent<HTMLFormElement>) => {
        event.preventDefault();
        setLoading(true);
    
        try {
          const formData = new FormData(event.currentTarget);
          
          const { data, errors } = await amplifyClient.queries.askBedrock({
            ingredients: [formData.get("ingredients")?.toString() || ""],
          });
    
          if (!errors) {
            setResult(data?.body || "No data returned");
          } else {
            console.log(errors);
          }
    
      
        } catch (e) {
          alert(`An error occurred: ${e}`);
        } finally {
          setLoading(false);
        }
      };
    
      return (
        <div className="app-container">
          <div className="header-container">
            <h1 className="main-header">
              Meet Your Personal
              <br />
              <span className="highlight">Recipe AI</span>
            </h1>
            <p className="description">
              Simply type a few ingredients using the format ingredient1,
              ingredient2, etc., and Recipe AI will generate an all-new recipe on
              demand...
            </p>
          </div>
          <form onSubmit={onSubmit} className="form-container">
            <div className="search-container">
              <input
                type="text"
                className="wide-input"
                id="ingredients"
                name="ingredients"
                placeholder="Ingredient1, Ingredient2, Ingredient3,...etc"
              />
              <button type="submit" className="search-button">
                Generate
              </button>
            </div>
          </form>
          <div className="result-container">
            {loading ? (
              <div className="loader-container">
                <p>Loading...</p>
                <Loader size="large" />
                <Placeholder size="large" />
                <Placeholder size="large" />
                <Placeholder size="large" />
              </div>
            ) : (
              result && <p className="result">{result}</p>
            )}
          </div>
        </div>
      );
    }
    
    export default App;
```

- Open a new terminal window, navigate to your projects root directory (ai-recipe-generator), and run the following command to launch the app:
- `npm run dev`
- Select the Local host link to open the Vite + React application.
- Choose the Create Account tab, and use the authentication flow to create a new user by entering your email address and a password. Then, choose Create Account.
- You will get a verification code sent to your email. Enter the verification code to log in to the app.
- When signed in, you can start inputting ingredients and generating recipes.
- In the open terminal window, run the following command to push the changes to GitHub:
- Sign in to the AWS Management console in a new browser window, and open the AWS Amplify console at https://console.aws.amazon.com/amplify/apps.
- AWS Amplify automatically builds your source code and deployed your app at https://...amplifyapp.com, and on every git push your deployment instance will 
  update. Select the Visit deployed URL button to see your web app up and running live.
  

- You have now connected your app to the Amplify backend and built a frontend to generate a recipe based on a list of ingredients submitted by the user.


## Task 6: Clean up Resources
  - delete all the resources that you created after testing your web app
  - In the Amplify console, in the left-hand navigation for the ai-recipe-generator app, choose App settings, and select General settings.
  - In the General settings section, choose Delete app.
