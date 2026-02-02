# Deployment Architecture

Define deployment strategy based on platform choice.

## Deployment Strategy

**Frontend Deployment:**
- **Platform:** Azure App Service
- **Build Command:** `nx build web --prod`
- **Output Directory:** `dist/apps/web`
- **CDN/Edge:** Azure CDN

**Backend Deployment:**
- **Platform:** Azure App Service (for Web API), Azure Functions (for Function App)
- **Build Command:** `dotnet publish -c Release -o out`
- **Deployment Method:** Azure DevOps Pipelines (Continuous Deployment)

## CI/CD Pipeline

The CI/CD pipeline, managed by Azure DevOps, will automate the build, test, and deployment processes. For secure management of sensitive data such as secret keys and connection strings, **Azure Key Vault** will be integrated with **Azure DevOps Variable Groups**.

**Secret Management with Azure Key Vault:**
1.  **Store Secrets:** All application secrets (e.g., database connection strings, API keys, service principal credentials) will be securely stored in Azure Key Vault instances, typically one per environment (Development, Staging, Production).
2.  **Variable Groups:** Azure DevOps Variable Groups will be created for each environment and linked to their respective Azure Key Vaults. This allows Azure DevOps pipelines to fetch secret values from Key Vault at runtime.
3.  **Secure Injection:** Secrets will be injected into pipeline jobs as environment variables, ensuring they are never exposed in plaintext in logs or committed to source control. Access policies in Key Vault will control which service principals (used by Azure DevOps) can access which secrets.

```yaml
# azure-pipelines.yml example for a monorepo
trigger:
  - main

variables:
  # Link to an Azure Key Vault-backed variable group for each environment
  - group: Azure-Dev-Secrets # Example: for development environment
  - group: Azure-Staging-Secrets # Example: for staging environment
  - group: Azure-Prod-Secrets # Example: for production environment

stages:
  - stage: Build
    displayName: Build Applications
    jobs:
      - job: BuildWeb
        displayName: Build Frontend (Angular)
        pool:
          vmImage: 'ubuntu-latest'
        steps:
          - task: NodeTool@0
            inputs:
              versionSpec: '24.x' # Use the Node.js LTS version
            displayName: 'Install Node.js'

          - script: npm install
            displayName: 'Install npm dependencies'

          - script: nx build web --prod
            displayName: 'Build Angular App'

          - task: PublishBuildArtifacts@1
            inputs:
              pathToPublish: 'dist/apps/web'
              artifactName: 'frontend-app'
            displayName: 'Publish Frontend Artifact'

      - job: BuildApi
        displayName: Build Backend (.NET Web API)
        pool:
          vmImage: 'windows-latest'
        steps:
          - task: UseDotNet@2
            inputs:
              version: '10.x' # Use the .NET LTS version
            displayName: 'Install .NET SDK'

          - script: dotnet restore src/UserEventHub.Api/UserEventHub.Api.csproj
            displayName: 'Restore .NET API dependencies'

          - script: dotnet publish src/UserEventHub.Api/UserEventHub.Api.csproj -c Release -o $(Build.ArtifactStagingDirectory)/api
            displayName: 'Publish .NET API'

          - task: PublishBuildArtifacts@1
            inputs:
              pathToPublish: '$(Build.ArtifactStagingDirectory)/api'
              artifactName: 'backend-api'
            displayName: 'Publish Backend API Artifact'

      - job: BuildFunction
        displayName: Build Backend (Azure Function)
        pool:
          vmImage: 'windows-latest'
        steps:
          - task: UseDotNet@2
            inputs:
              version: '10.x' # Use the .NET LTS version
            displayName: 'Install .NET SDK'

          - script: dotnet restore src/UserEventHub.Processor/UserEventHub.Processor.csproj
            displayName: 'Restore .NET Function dependencies'

          - script: dotnet publish src/UserEventHub.Processor/UserEventHub.Processor.csproj -c Release -o $(Build.ArtifactStagingDirectory)/function
            displayName: 'Publish Azure Function'

          - task: PublishBuildArtifacts@1
            inputs:
              pathToPublish: '$(Build.ArtifactStagingDirectory)/function'
              artifactName: 'backend-function'
            displayName: 'Publish Backend Function Artifact'

  - stage: Deploy
    displayName: Deploy Applications
    dependsOn: Build
    jobs:
      - deployment: DeployFrontend
        displayName: Deploy Frontend
        environment: 'Production' # Or 'Staging'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureWebApp@1
                  inputs:
                    azureSubscription: 'Your Azure Subscription'
                    appName: 'user-event-hub-web-prod'
                    package: '$(Pipeline.Workspace)/frontend-app'
                  displayName: 'Deploy Frontend to Azure App Service'

      - deployment: DeployBackendApi
        displayName: Deploy Backend API
        environment: 'Production' # Or 'Staging'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureWebApp@1
                  inputs:
                    azureSubscription: 'Your Azure Subscription'
                    appName: 'user-event-hub-api-prod'
                    package: '$(Pipeline.Workspace)/backend-api'
                  displayName: 'Deploy Backend API to Azure App Service'

      - deployment: DeployBackendFunction
        displayName: Deploy Azure Function
        environment: 'Production' # Or 'Staging'
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureFunctionApp@1
                  inputs:
                    azureSubscription: 'Your Azure Subscription'
                    appType: 'functionApp'
                    appName: 'user-event-hub-function-prod'
                    package: '$(Pipeline.Workspace)/backend-function'
                  displayName: 'Deploy Azure Function App'
```

## Environments

| Environment | Frontend URL | Backend URL | Purpose |
|---|---|---|---|
| Development | `http://localhost:4200` | `http://localhost:5001/api/v1` | Local development |
| Staging | `https://staging.usereventhub.com` | `https://staging-api.usereventhub.com/v1` | Pre-production testing |
| Production | `https://usereventhub.com` | `https://api.usereventhub.com/v1` | Live environment |
