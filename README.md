# Azure Cloud Modernization

Modernizing legacy SQL and PowerShell workflows with Azure Logic Apps, Synapse Analytics, Microsoft Entra ID, Microsoft Graph, Bicep and CI/CD.

## Secure email notification architecture

![Azure Synapse to Microsoft Entra ID to Logic App to Microsoft Graph architecture](assets/secure-email-notification-flow.svg)

**How it works**

1. **Azure Synapse** obtains an OAuth 2.0 access token from **Microsoft Entra ID** using a configured identity.
2. The Synapse **Web Activity** sends an HTTPS POST request with the bearer token to **Azure Logic Apps**.
3. The Logic App validates the incoming request, runs its workflow and uses separately authorized credentials/identity to call **Microsoft Graph API**.
4. Microsoft Graph sends the email from the configured sender mailbox.

Development, PPE and production use separate application registrations in this reference scenario.

> **Note:** The diagram is a conceptual representation, not a claim that one token works for both the Logic App endpoint and Microsoft Graph. Each API requires an appropriately issued token and granted permissions.

## Documentation

- [Azure fundamentals: Bicep and JSON objects](docs/azure-fundamentals.md)
- [Logic App deployment with existing build and release pipelines](docs/logic-app-cicd-deployment.md)

## Security

Examples here are independently recreated and sanitized. Do not commit internal endpoints, secrets, access signatures, employer source code or organization-specific settings.
