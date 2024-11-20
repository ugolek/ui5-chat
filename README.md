# Creating a Chat Control with UI5 (OpenUI5) Using TypeScript

This tutorial will guide you through setting up a development environment to create a chat control with UI5 (OpenUI5) using TypeScript. It assumes you start without any pre-installed development tools or software.

---

## Prerequisites

Before proceeding, ensure you have:

- **Internet connection**
- **Basic understanding of the command-line interface (CLI)**
- **Node.js installed**
- **Git installed** (if you plan to clone repositories)
- **Basic knowledge of SAP UI5 and approuter**

First, download the repository with the basic structure and configurations:

Repository URL: [https://github.tools.sap/I751749/ui5-chat.git](https://github.tools.sap/I751749/ui5-chat.git)

---

## Step 1: Install UI5 CLI and Dependencies

UI5 CLI is a command-line tool for UI5 development. To install it globally, run the following command:

```bash
npm install @ui5/cli --save-dev
```

Verify the installation by checking the version:

```bash
ui5 --version
```

Next, install the necessary dependencies. Run the following commands in the root and the `approuter` folder:

```bash
npm install
```

---

## Step 2: Add an Instance of AgentBuilder in Cloud Foundry

1. **Log in to your Cloud Foundry Space**  
   Navigate to your workspace (e.g., `default`).

2. **Access the Service and Marketplace**  
   Open the **Service and Marketplace** section and locate **"Project Agent Builder"**.

3. **Create an Instance**  
   - Select **"Project Agent Builder"**.  
   - Click **Create**.  
   - Choose the **default instance plan** and provide an instance name.

---

## Step 3: Build and Package the Application

1. **Build the Application**  
   In the project root, run:

   ```bash
   npm run build
   ```

2. **Package the Application**  
   Create an `.mtar` package using the following command:

   ```bash
   mbt build -t gen --mtar mta.tar
   ```
   (perhaps you need mbt install first)
---

## Step 4: Deploy to Cloud Foundry

1. **Log in to Cloud Foundry**  
   Use the following command, replacing the URL with your tenant server address:

   ```bash
   cf login -a https://api.cf.eu12.hana.ondemand.com --sso
   ```

   Enter the Temporary Authentication Code in the console to complete the login.

2. **Deploy the Application**  
   Run the deployment command:

   ```bash
   cf bg-deploy gen/mta.tar --no-confirm
   ```

   During deployment:
   - An `xsuaa` instance will be created and bound automatically based on the `my-xsuaa` resource in `mta.yaml`.
   - A `destination` service will also be created and bound automatically, as described in `mta.yaml` (e.g., `my-destination-service`).

---

## Step 5: Configure Destination Service

At this point, deployment will not work yet because the `destination` configuration is missing. Follow these steps to complete the setup:

1. **Create a Service Key for AgentBuilder**  
   Go to the **Project Agent Builder** instance (we created it at step 2) and create a **Service Key**. Copy the generated key.

2. **Add a Destination**  
   Navigate to the **subaccount level** in the BTP Cockpit:  
   - Go to **Connectivity > Destinations**.  
   - Add a new destination with the name `AgentBuilder` and populate the fields as follows:

     - **Name**: `AgentBuilder`
     - **Type**: `HTTP`
     - **URL**: `https://some-adress.stage.kyma.ondemand.com/api/v1`
     - **Authentication**: `OAuth2ClientCredentials`
     - **Token Service URL**: `https://some-adress.authentication.euXX.hana.ondemand.com/oauth/token`
     - **Client ID**: *(from your Agent Builder service key)*
     - **Client Secret**: *(from your Agent Builder service key)*

   - Ensure all the required fields are correctly populated based on the details from the Agent Builder service key.

   After completing the setup, click the **"Check Connection"** button.

   - If the configuration is correct, you should see the following message:  
     **"Connection to 'AgentBuilder' established. Response returned: '401: Unauthorized'"**

   This indicates that the connection is successfully established but requires proper authentication credentials to access the API.

3. **Verify the OData Metadata**  
   Test the setup by accessing the metadata URL:

   ```bash
   https://<tenant-and-app-name>.cfapps.euXX.hana.ondemand.com/odata/unified-ai-service/$metadata
   ```

   If successful, the metadata will be displayed.

---

## Step 6: Create Subscriptions and Test the Application

Don't forget that we don't have any Agents or Chats at our tenant account. You need to add some for using ui from this repository.

1. **Enable the Classic UI**  
   Open the **BTP Cockpit** at the subaccount level, navigate to **Instances and Subscriptions**, and click **Create**.  
   - Find **Project Agent Builder** and add it as a subscription.  
   - This will provide access to the classic UI where you can create agents, tools, and chats.

2. **Access the Chat UI**  
   This repository includes a UI for a single chat messaging app. Open the chat UI by accessing the app URL and adding the specific `AgentID` and `chatId` to the path:

   Example:

   ```bash
   https://<your-full-domain-and-app-address>/ui/#/chat/9ad88182-3f5b-4755-acdf-a17bd3762b10/1d67ba26-c0ab-45b0-bf0b-eb5927cd2354
   ```

---

At this stage, your instance of AgentBuilder should be ready for use with your custom UI.
