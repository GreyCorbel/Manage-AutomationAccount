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
`text
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
` 

## Action Inputs
* **environmentName** (required) - Defines the environment for which you want to perform synchronization (default is "Common").
* **projectDir** (required) - Defines the path where the predefined directory structure is located in the project repository.
* **subscription** (required) - Defines the subscription in which Azure Automation resides.
* **azureSubscription** (required) - Defines the service connection / credentials.
* **resourceGroup** (required) - Defines the resource group.
* **automationAccount** (required) - The name of the Azure Automation account.
* **storageAccount** - Used for automatically uploading private PowerShell modules to Azure Storage.
* **storageAccountContainer** - A specific container in which the powershell module should be saved.
* **fullSync** - Defines whether items that are not in the predefined directory structure should be deleted from Azure Automation.
* **reportMissingImplementation** - Returns a list of items that do not have an implementation.
* **helperHybridWorkerModuleManagement** - Whether to turn on helper: HybridWorkerModuleManagement.
* **verbose** - A switch for detailed logging.
* **cloudEnvironment** - Select the target Azure cloud environment (e.g., AzureCloud, AzureUSGovernment, AzureChinaCloud). Default is AzureCloud.
