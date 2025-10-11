# Infrastructure as Code (IaC)

This directory contains the Infrastructure as Code (IaC) templates for deploying the Octopets application to Azure Container Apps.

## Structure

- `main.bicep`: Main deployment module that creates the resource group and orchestrates resource deployment
- `resources.bicep`: Shared resources including Container Apps Environment, Log Analytics, Container Registry, Key Vault, and Application Insights
- `../backend/manifests/containerApp.tmpl.yaml`: Container App configuration template for the backend API
- `../frontend/manifests/containerApp.tmpl.yaml`: Container App configuration template for the frontend

## Container App Resources Configuration

### Backend API (octopetsapi)

Based on production incident resolution, the backend API has been scaled to handle memory-intensive operations:

- **CPU**: 1.0 cores (scaled up from 0.5)
- **Memory**: 2Gi (scaled up from 1Gi)
- **Min Replicas**: 2
- **Max Replicas**: 10
- **Auto-scaling**: HTTP-based with 100 concurrent requests threshold

### Frontend (octopetsfe)

- **CPU**: 0.5 cores
- **Memory**: 1Gi
- **Min Replicas**: 1
- **Max Replicas**: 5
- **Auto-scaling**: HTTP-based with 100 concurrent requests threshold

## Deployment

This infrastructure is designed to be deployed using Azure Developer CLI (azd):

```bash
# Initialize azd (if not already done)
azd init

# Provision infrastructure and deploy
azd up
```

## Configuration Changes

The following environment variables are configured for production:

- `ERRORS=false`: Disabled error simulation flag (previously caused OutOfMemoryException)
- `ENABLE_CRUD=false`: CRUD operations disabled in production
- `EnableSwagger=true`: Swagger UI enabled for API documentation

## Incident Resolution Reference

This IaC configuration codifies the resolution of the production incident where:
- Container app was experiencing 500 errors due to OutOfMemoryException
- Manual scaling to 1 CPU and 2Gi memory resolved the issue
- This configuration prevents drift by maintaining scaled resources in code

For more details, see the incident issue.
