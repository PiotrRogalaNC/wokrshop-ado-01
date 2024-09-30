# Azure DevOps - Workshop (2024-10-01)

# wokrshop-ado-01

## prerequisites

Azure DevOps portal - [https://dev.azure.com/YOUR-ORG/YOUR-PROJECT](https://dev.azure.com/YOUR-ORG/YOUR-PROJECT)

- go to page: prerequisites

## exercises

## 1. Intro to pipelines

1. What is where in the portal

## 2. Create simple yaml

1. Open page: https://learn.microsoft.com/en-us/azure/devops/pipelines/process/stages?view=azure-devops&tabs=yaml#specify-stages

### Lab-1

1. Create file lab-1.yml
2. Prepare step: echo "Hello world”
3. Run

### Lab-2

1. Create file lab-2.yml
2. Prepare two jobs with
    1. echo "A”
    2. echo "B”
3. Run

### Lab-3

1. Create file lab-3.yml
2. Prepare two stages with jobs
    1. Stage A
        
        ```
          - job: A1
          - job: A2
        ```
        
    2. Stage B
        
        ```
          - job: B1
          - job: B2
        ```
        
3. Create file lab-3a.yml
4. Copy content from previous lab-3.yml
5. Add trigger to pipeline
    1. path to folder where is yaml from lab-3 and lab-3a
    2. example: `pipelines/lab-3/*`
6. Run

## 3.  Variable in pipeline

### Lab-4

1. Create file lab-4.yml
2. Copy content from lab-1
3. Add trigger like in lab-3a
4. Add `variables`:
    
    ```
    variables:
    - name: adoagent
      value: 'default'
    ```
    
5. In steps add echo with variable:
    
    ```
    - pwsh: echo $(adoagent)
    ```
    
6. Run

### Lab-4a

1. Create file lab-4a.yml
2. Copy content from previous lab-4.yml
3. Replace value for: `pool:` and use there variable with name for agent pool
4. Run

## 4. Deploy bicep file to Azure

### Lab-5

1. Open pages and review
    1. https://learn.microsoft.com/en-us/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml#define-a-single-job
    2. https://learn.microsoft.com/en-us/azure/devops/pipelines/tasks/reference/azure-resource-manager-template-deployment-v3?view=azure-pipelines
2. Create file lab-5.yml
3. Create bicep template for deployment in Azure
    1. Example xxx.bicep
        
        ```
        param location string
        param kvName string
        
        resource keyVault 'Microsoft.KeyVault/vaults@2023-07-01' = {
          name: kvName
          location: location
          properties: {
            tenantId: subscription().tenantId
            sku: {
              family: 'A'
              name: 'standard'
            }
            enabledForDeployment: false
            enabledForTemplateDeployment: false
            enabledForDiskEncryption: false
            enableSoftDelete: true
            enablePurgeProtection: true
            softDeleteRetentionInDays: 7
            accessPolicies: []
          }
        }
        
        ```
        
    2. Example xxx-nonprod.bicepparam
        
        ```
        using './keyVault.bicep'
        
        param location = 'northeurope'
        param kvName = 'NAME-KEY-VAULTE-UNIQUE-NONPROD'
        
        ```
        
4. Based on previous yaml’s create job with deployment and include:
    1. variables for
        1. ado agent name
        2. service connection
        3. subscription id
    2. trigger
    3. environment - create new in Azure DevOps
        1. use name nonprod
    4. in task AzureResourceManagerTemplateDeployment
        1. use path to bicep files
        2. set resource group name
5. Go to ADO and Run

### Lab-6

1. Based on error from Lab-5, try to fix

## 5. Stages for deployment

### Lab-7

1. Copy whole content from Lab-6 and use to Lab7.
2. Add new xxx-prod.bicepparam for prod env.
    
    ```
    using './keyVault.bicep'
    
    param location = 'northeurope'
    param kvName = 'NAME-KEY-VAULTE-UNIQUE-PROD'
    
    ```
    
3. In yaml file, edit jobs and add stages:
    1. nonprod
    2. prod
4. Adjust file paths for .biepparam’s to have one deployment nonprod and one prod
5. Run

## 6. Additional configuration

1. Security in ADO agents
    1. You can set open access for all pipelines
2. Configuration Environments
    1. Additionally add Approval Gate for PROD