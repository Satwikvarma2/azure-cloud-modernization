# Azure Cloud Modernization — Learning Notes

These notes capture basic concepts used when modernizing legacy SQL and PowerShell workflows with Azure services.

## Bicep file

A **Bicep file** (`.bicep`) is an Infrastructure as Code (IaC) file used to **define and deploy Azure resources**. Instead of manually creating resources in the Azure portal, we describe the resources and their settings in Bicep and deploy them through Azure Resource Manager.

**Example:**
```bicep
param location string = resourceGroup().location

resource storageAccount 'Microsoft.Storage/storageAccounts@2023-05-01' = {
  name: 'stmodernizationdemo001'
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
}
```
This example defines an Azure Storage Account. The resource is created or updated only when the Bicep template is deployed, for example through a CI/CD pipeline.

## Object

An **object** is a collection of **key-value pairs**. Each key identifies a property, and each value holds its data. JSON objects are commonly used to exchange data between Azure services.

**Example JSON object:**
```json
{
  "recipient": "demo@example.com",
  "subject": "Pipeline completed",
  "message": "The data pipeline finished successfully."
}
```

Here, `recipient`, `subject`, and `message` are keys. Their associated strings are values.

## How Azure Synapse sends an object to a Logic App

1. A Synapse pipeline runs a **Web Activity**.
2. The Web Activity makes an HTTP request (typically `POST`) to a Logic App's **HTTP request trigger URL**.
3. The request **body contains a JSON object** with the information the Logic App needs.
4. The Logic App's request trigger accepts the JSON and its actions use the provided properties to process the request, such as sending an email.

For example, the Synapse Web Activity might send the JSON object shown above as its request body, with `Content-Type: application/json`. The Logic App's trigger schema must match the actual request body.

> **Security note:** Use sample data in this repository. Never commit real trigger URLs containing access signatures, credentials, production addresses, or employer-specific configurations.
