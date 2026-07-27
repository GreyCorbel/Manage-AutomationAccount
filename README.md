# Manage-AutomationAccount GitHub Action

The Manage-AutomationAccount action provides a seamless experience for Azure Automation Account resources, automatically updating individual items in the automation account based on your local project with a predefined structure. 

**Supported items include:**
* Runbooks
* Variables
* Configurations
* Schedules
* Modules
* JobSchedules

Integrate this action into your GitHub workflow and let it take care of everything for you! 

## How does it work?

The logic of this action is based on a predefined directory structure where you store individual items you want to synchronize with Azure Automation. The directory structure is divided into **Definitions** and **Source**. 

* **Definitions:** Specify the definition and bindings of individual objects (JSON files).
* **Source:** Store common objects and environments (like Prod, UAT, or Common) to be synchronized.

### Directory tree example:
```text
-Definitions
    -Runbooks
        -test.json
    -JobSchedules
        -test.json
    -Schedules
        -Minutes-15.json
-Source
    -Common
    -Prod
        -Runbooks
            -test.ps1
        -JobSchedules
            -Default-Parameters.json
```

### Authentication with Azure
Unlike Azure DevOps, which uses Service Connections natively, GitHub Actions requires Azure Service Principal credentials to authenticate. You need to create an Azure Service Principal with Contributor rights to your subscription and store its details in GitHub Secrets or Variables `Settings -> Secrets and variables -> Actions`.

You will need to configure the following secrets/variables:
```
AZURE_CLIENT_ID        | Secret
AZURE_CLIENT_SECRET    | Secret
AZURE_TENANT_ID        | Secret
AZURE_SUBSCRIPTION_ID  | Variable
```
## Action Inputs
* **scope** (required) - What we are deploying. Options: 'Runbooks', 'Variables', 'Configurations', 'Schedules', 'Modules', 'JobSchedules', 'Webhooks'.
* **environmentName** (required) - Defines the environment for which you want to perform synchronization (default is "Common").
* **projectDir** (required) - Defines the path where the predefined directory structure is located in the project repository.
* **subscription** (required) - Defines the subscription in which Azure Automation resides.
* **azureClientId** (required) - Client ID (App ID) of the service principal with contributor rights for defined subscription.
* **azureClientSecret** (required) - Client secret of the service principal.
* **azureTenantId** (required) - Azure AD tenant ID of the service principal.
* **azureSubscriptionId** (required) - Subscription ID where the automation account is located.
* **resourceGroup** (required) - Defines the resource group.
* **automationAccount** (required) - The name of the Azure Automation account.
* **storageAccount** - Used for automatically uploading private PowerShell modules to Azure Storage.
* **storageAccountContainer** - A specific container in which the powershell module should be saved.
* **fullSync** - Defines whether items that are not in the predefined directory structure should be deleted from Azure Automation.
* **reportMissingImplementation** - Returns a list of items that do not have an implementation.
* **helperHybridWorkerModuleManagement** - Whether to turn on helper: HybridWorkerModuleManagement.
* **verbose** - A switch for detailed logging.
* **cloudEnvironment** - Select the target Azure cloud environment (e.g., AzureCloud, AzureUSGovernment, AzureChinaCloud). Default is AzureCloud.
## Example Usage
Here is an example of how to use this action in your GitHub workflow. Make sure to pass the credentials using GitHub Secrets `${{ secrets.SECRET_NAME }}`.
```
name: Deploy Automation Account
on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: windows-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Deploy Runbooks to Azure Automation
        uses: Greycorbel/Manage-AutomationAccount@v1 # Replace with your actual repository name and tag/branch
        with:
          scope: 'Runbooks, Variables, Configurations, Schedules, Modules, JobSchedules'
          environmentName: 'Prod'
          projectDir: './Automation/Default'
          subscription: 'My-Azure-Subscription-Name'
          azureClientId: ${{ secrets.AZURE_CLIENT_ID }}
          azureClientSecret: ${{ secrets.AZURE_CLIENT_SECRET }}
          azureTenantId: ${{ secrets.AZURE_TENANT_ID }}
          azureSubscriptionId: ${{ vars.AZURE_SUBSCRIPTION_ID }}

          cloudEnvironment: 'AzureCloud'

          resourceGroup: 'my-rg-automation'
          automationAccount: 'my-automation-acc'

          storageAccount: 'my-storage-acc'
          storageAccountContainer: 'my-storage-acc-container'

          fullSync: 'true'
```
