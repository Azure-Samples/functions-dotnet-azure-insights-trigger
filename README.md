---
services: app-service, functions
platforms: dotnet
author: syntaxc4
---

# C# Azure Function for Reacting to Azure Insights Events

## How to run the sample

To run this sample you will need the following:

* An Internet Connection
* An Azure Subscription (Works with the [Free Trial](https://azure.microsoft.com/en-us/free/)!)

## Deploy this sample to Azure

1. Click on the Deploy to Azure button above
1. Login to your Azure Subscription
1. Fill out a few details in the Portal
1. Kick off your shoes, put your feet up, lean back and enjoy as this sample deploys itself.

## Walk-through

This walkthrough will outline how to re-create the deployed version of the application.

### Prerequisites

1. [Node.js & npm](https://nodejs.org/en/download/)
1. [Visual Studio Code](https://code.visualstudio.com)
    * [Azure Functions Tooling for VS Code](https://marketplace.visualstudio.com/items?itemName=ahmelsayed.vscode-azurefunctions)
1. Azure Functions CLI (from a command prompt)

   ```npm install -g azurefunctions```

1. Azure Subscription or [Azure Free Trial](https://azure.microsoft.com/en-us/free)

### Writing your first Azure Function on localhost

#### Preparing the Azure Functions CLI

Run the command below, then sign in via the login screen popup.

   ```func login```

#### Creating a new Azure Function Locally

1. Create a working directory for the function
1. Initialize the working direction by running

    ```func init```
1. Scaffold a new Azure Function by running

    ```func new```
1. Follow the Yeoman prompts

    * List all the templates
    * Select **GenericWebHook-CSharp**
    * Enter a name for the Function

1. Launch Visual Studio Code for Editing

You can launch Visual Studio Code from the command line by simply typing `code .` (including the '.' will launch VS Code with the current directory loaded in the workspace).

Under the directory which is named the same as your function, you will see three files:

* [function.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/function.json)
* [run.csx](https://azure.microsoft.com/en-us/documentation/articles/functions-reference-csharp/)
* sample.dat - sample request data

We will be leveraging the `function.json` & `run.csx` files to build out the rest of the solution and `sample.dat` to store some development data.

#### Building the Azure Insights Receiver

Copy the sample information below into your clipboard, this will be used to test the function while it is being developed. Adding this file to sample.dat in your local development environment is encouraged, but not required.

> For this sample we will use the sample payload available from the [Azure Insights documentation](https://azure.microsoft.com/en-us/documentation/articles/insights-autoscale-to-webhook-email/#autoscale-notification-webhook-payload-schema)

```
{
   "version": "1.0",
   "status": "Activated",
   "operation": "Scale In",
   "context": {
      "timestamp": "2016-03-11T07:31:04.5834118Z",
      "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
      "name": "myautoscaleSetting",
      "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
      "subscriptionId": "s1",
      "resourceGroupName": "rg1",
      "resourceName": "MyCSRole",
      "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
      "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
      "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
      "oldCapacity": "3",
      "newCapacity": "2"
   },
   "properties": {
      "key1": "value1",
      "key2": "value2"
   }
}
```

#### Commit your Code and Push to github

You've now created your first Azure Function, you are ready to commit the code and push it to GitHub.

1. Run the following commands from the command line:

   ```git add -A; git commit -m "initial commit"```

1. Add the GitHub remote

   ```git remote add origin <github-repository-url>```

1. Push the code to GitHub

   ```git push origin master```

### Building the Infrastructure

#### Create the Azure Function

1. From the command line run the following command:

   ```func new functionApp myFuncApp [--subscription <subscriptionId>]```

1. Navigate to the [Azure Functions Portal](https://functions.azure.com/signin), and sign into your account
1. Find and *Click* on **App Services** from the left-hand menu
1. Click on **myFuncApp** in the App Services listing blade
1. Click on the **Function app settings** link at the bottom of the Function App blade
1. Click on **Configure continous integration**
1. Follow these steps on [setting up continuous integration] (https://azure.microsoft.com/en-us/documentation/articles/app-service-continous-deployment) under *Enable continuous deployment*

#### Create an App Service Web App

1. Navigate to the [Azure Portal](https://portal.azure.com) and sign into your account
1. Click **+ New**
1. Select **Web + Mobile**
1. Select **Web App**
1. Enter a **App name** and **Resource Group**
1. Create or Select an **App Service Plan** (Note: Must select a Standard or Premium Pricing tier)
1. Click **Select**
1. Click **OK**
1. Click **Create**

#### Configure auto-scale

1. Once your Web App is created, **select** Scale out (App Service Plan) from the left hand of the Web App blade
1. Select *CPU Percentage* from the **Scale by** field
1. Set the Minimum and Maximum **Instances** threshold
1. Set the Minimum and Maximum **CPU Target** threshold
1. Paste the *Function Url* from your Azure Function into the **Webhook field**
1. Click **Save**

### Load Test your Application to Trigger the Auto-Scale

1. From the left hand navigation in the App Service blade, select **Performance test** from under the *Development Tools* menu
1. Click on **+ New** from within the Performance test blade
1. Fill out the **name** and select the appropriate **region** to send load from the closest region to your web app
1. Adjust the **User load** and **duration**
1. Click **Run test** (This will do front-door testing of your Web Application)