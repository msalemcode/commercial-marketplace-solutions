# In this article
- [What is Delegated Managed Identity Resource ID?](#what-is-delegated-managed-identity-resource-id)
- [Why Do We Need It?](#why-do-we-need-it)
- [How It Works](#how-it-works)
- [How to Apply delegatedManagedIdentityResourceId](#how-to-apply-delegatedmanagedidentityresourceid)
- [Common Errors and Troubleshooting](#common-errors-and-troubleshooting)
    
## What is Delegated Managed Identity Resource ID?

In Azure, the `delegatedManagedIdentityResourceId` property is used to properly assign roles to managed identities across different tenants. This is particularly useful when dealing with managed applications published in the Azure Marketplace, where the publisher and the customer exist in separate tenants.

## Why Do We Need It?

When a customer deploys a managed application from the marketplace, the publisher is responsible for managing resources within the managed resource group (MRG). However, any role assignments performed as part of the deployment template occur in the publisher's tenant. This creates a challenge when the managed identity is created in the customer's tenant, as role assignment will fail if it tries to locate the identity in the wrong tenant.

The `delegatedManagedIdentityResourceId` property resolves this issue by explicitly specifying where the managed identity exists, ensuring that the role assignment process can correctly locate and assign permissions.

## How It Works

### Managed Identity Creation

When deploying a managed application, the managed identity is created in the customer’s tenant.

### Role Assignment

Role assignment deployment occurs under the publisher's tenant, role assignments naturally look for identities within that tenant.

### Using delegatedManagedIdentityResourceId

By specifying the correct resource ID:

- **For System Assigned Identities**: Use the resource ID of the resource that holds the identity (e.g., Function App or Logic App).
- **For User Assigned Identities**: Use the resource ID of the identity itself.

## How to Apply delegatedManagedIdentityResourceId

To set up role assignment correctly, add the `delegatedManagedIdentityResourceId` property in the role assignment section of your Azure Resource Manager (ARM) template. Example:

```json
{
  "type": "Microsoft.Authorization/roleAssignments",
  "apiVersion": "2022-04-01",
  "properties": {
    "roleDefinitionId": "<role-definition-id>",
    "principalId": "<principal-id>",
    "delegatedManagedIdentityResourceId": "<resource-id-of-identity>"
  }
}
```
## Common Errors and Troubleshooting

### Role Assignment Failure Due to Missing Identity

- Ensure that the correct resource ID is provided in `delegatedManagedIdentityResourceId`.
- Verify that the managed identity exists in the customer's tenant.

### Deny Assignment Preventing Access

- The deny assignment prevents customers from accessing the MRG.
- Ensure the publisher's identity managing the MRG is correctly referenced in the customer's tenant.

### Misconfigured Deployment Context

- AMA deployments with published managed apps and publisher access enabled occur in the publisher's tenant.
- Ensure `delegatedManagedIdentityResourceId` is properly set to reference the customer’s tenant identity.

### Next steps
[Azure Managed Application with managed identity](https://learn.microsoft.com/en-us/azure/azure-resource-manager/managed-applications/publish-managed-identity)