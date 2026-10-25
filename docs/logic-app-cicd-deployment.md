# Azure Logic App Deployment Using Existing CI/CD Pipelines

## Objective

Modernize a legacy SQL-based email notification workflow using Azure Logic Apps and an existing Azure DevOps build and release process. This document describes an independently recreated deployment pattern, not production configuration.

## Existing CI/CD Setup

- An existing Azure DevOps build pipeline packages deployment artifacts.
- An existing release pipeline deploys Azure resources.
- Environment-specific deployment configuration is maintained for **Dev**, **PPE**, and **Prod**.

Rather than creating separate pipelines, add a Logic App deployment step to the existing release YAML and reuse the build artifacts.

## Implementation Plan

### 1. Extend the release YAML

Add a deployment task or template invocation that deploys the Logic App using Bicep/ARM and the target environment's parameters. Match the existing repository conventions for artifact paths, stages, and service connections.

### 2. Store environment-specific sender mailbox configuration

Use the relevant parameter/configuration file for each deployment environment:

| Environment | Example sender mailbox |
| --- | --- |
| Dev | dev-sender@example.com |
| PPE | ppe-sender@example.com |
| Prod | prod-sender@example.com |

All addresses above are **fictional placeholders**. Configuration files should not include credentials or confidential information.

### 3. Pass configuration into the deployment

1. Determine the target environment (Dev, PPE, or Prod).
2. Read that environment's configuration file in the release task.
3. Extract the sender mailbox value into a PowerShell variable (or map it into a pipeline variable if needed).
4. Validate that the required value is present.
5. Pass it as a parameter to the Bicep/ARM deployment.
6. Confirm that the deployed Logic App receives the intended configuration.

A PowerShell variable used within a task is **not automatically** an Azure DevOps pipeline variable. If subsequent tasks need the value, publish it explicitly using supported pipeline variable mechanisms.

### 4. Build and release

**Build pipeline:** Validate and package templates, scripts, and supporting deployment files as artifacts.

**Release pipeline:** Consume those artifacts and execute the environment-specific Azure deployment using an appropriately authorized service connection or deployment identity.

### 5. Promote deployments

```text
Build & publish artifacts
         |
         v
Release: Dev deployment -> Test and validate
         |
         v
Release: PPE deployment -> Integration testing and approval
         |
         v
Release: Prod deployment -> Required approval and verification
```

- **Dev:** Deploy first and verify that the Logic App trigger and email workflow function with test data.
- **PPE:** Promote after Dev passes; validate the full integration.
- **Prod:** Promote after PPE passes and required approvals are met.

## Validation and Security

- Confirm the deployment identity can deploy the required Azure resources.
- Confirm the correct environment-specific file and sender mailbox are selected.
- Configure and verify the Logic App identity's required Microsoft Graph `Mail.Send` application permission and necessary consent separately; a sender mailbox parameter alone does not grant permission.
- Protect Logic App trigger URLs, access signatures, and secrets. Do not print or commit them.
- Use fictional examples rather than employer-specific files, subscriptions, resource IDs, or internal endpoints.
- Ensure environment approval gates are respected before production deployment.

## Expected Outcome

The existing build/release setup deploys a consistently configured Logic App into Dev, then PPE, then Prod, reducing manual steps and supporting repeatable, auditable releases.

> This is a high-level reference implementation plan. Exact YAML task names, Bicep parameters, deployment scripts, service connections, and email configuration depend on the target project and must be validated before use.
