# A Lap Around the Azure Portal and Cloud Shell

This is a walkthrough of the different areas of the Azure Portal along with what is possible with Azure Cloud Shell using demos.

## Prerequisites

* Access to an Azure Subscription and Logged In to [Azure Portal](http://portal.azure.com)

## Azure Portal

1. Terminology

    * Navigation Bar
    * Portal Settings
        * Search
        * Cloud Shell
        * Directory and Subscription Filter
        * Bell (Notifications)
        * Gear (UI Settings)
        * Ask for Help
        * Send Feedback        
    * Identity & Switch Directory
    * Dashboard

2. Dashboards

    * Default with Azure Status
    * Creating Custom Dashboard
    * Show Existing Custom Dashboard & Sharing

3. Navigation Bar

    * View into Resources
    * All Services - Pin AKS
    * Resource Groups & Resources - Create RG

4. Create a Resource, Notification, Security & Support

    * Azure Market Place (AMP)
    * Azure Container Registry - Standard SKU
    * Blades
    * Access Control Blade - Security
    * New Support Request Blade - Ask for Help

5. Search

    * Global Search
    * Blade Search

6. Cloud Shell

    * Integrated with Portal
    * [Standalone Cloud Shell](http://shell.azure.com)
    * Cloud Drive - Backed by Azure Storage

## Azure Cloud Shell

1. Create Resource Group & Azure Container Registry

    * Clone Repo
    * Create Resource Group
    * Create Azure Container Registry (ACR) Using Premium SKU

    ```bash
    # Clone Repository
    cd ~
    git clone https://github.com/OSSCanada/portal-and-cloudshell.git
    cd portal-and-cloudshell
    # Create Resource Group.
    RG="ossvc20180913-rg"
    LOC="eastus"
    NAME="ossvc20180913"
    ACR_NAME="${NAME}acr"
    AKV_NAME="${NAME}vault"
    az group create --name $RG --location $LOC
    # Create Registry
    az acr create -g $RG -n ${ACR_NAME} --sku Premium --admin-enabled
    # List the new Registry
    az acr list -o table
    ```

2. Azure Container Registry Build & Secure Deploy to ACI

    * Using Git - Clone Repository
    * Build Container Image using ACR Build
    * Create Azure Key Vault and Service Principal for Secure ACI Deployment
    * Deploy to ACI

    ```bash
    cd ~
    git clone https://github.com/Azure-Samples/acr-build-helloworld-node.git
    cd acr-build-helloworld-node
    az acr credential show -n ${ACR_NAME}
    az acr show -n ${ACR_NAME} --query "{acrLoginServer:loginServer}" -o table
    az acr repository list -n ${ACR_NAME}
    az acr build --registry ${ACR_NAME} --image helloacrbuild:v1 .
    az acr repository list -n ${ACR_NAME}
    az acr repository show-manifests -n ${ACR_NAME} --repository helloacrbuild
    az acr repository show-tags -n ${ACR_NAME} --repository helloacrbuild
    # Create Key Vault and Deploy to ACI
    az keyvault create -g $RG -n ${AKV_NAME}
    # Create service principal, store its password in AKV (the registry *password*)
    az keyvault secret set \
    --vault-name ${AKV_NAME} \
    --name ${ACR_NAME}-pull-pwd \
    --value $(az ad sp create-for-rbac \
                    --name ${ACR_NAME}-pull \
                    --scopes $(az acr show --name ${ACR_NAME} --query id --output tsv) \
                    --role reader \
                    --query password \
                    --output tsv)
    # Store service principal ID in AKV (the registry *username*)
    az keyvault secret set \
        --vault-name ${AKV_NAME} \
        --name ${ACR_NAME}-pull-usr \
        --value $(az ad sp show --id http://${ACR_NAME}-pull --query appId --output tsv)
    # Deploy Container using Azure Container Instance (ACI)
    az container create \
        --resource-group $RG \
        --name acr-build \
        --image ${ACR_NAME}.azurecr.io/helloacrbuild:v1 \
        --registry-login-server ${ACR_NAME}.azurecr.io \
        --registry-username $(az keyvault secret show --vault-name $AKV_NAME --name ${ACR_NAME}-pull-usr --query value -o tsv) \
        --registry-password $(az keyvault secret show --vault-name $AKV_NAME --name ${ACR_NAME}-pull-pwd --query value -o tsv) \
        --dns-name-label acr-build-${ACR_NAME} \
        --query "{FQDN:ipAddress.fqdn}" \
        --output table
    az container attach --resource-group $RG --name acr-build
    # Navigate to fqdn to Validate Deployment
    ```

3. Using Terraform to Deploy Containers

    * Introduce VS Code Integration
    * Introduction to Hashicorp Terraform
    * Drill into [main.tf](main.tf)
    * Execute Terraform Script using CLI
    * Validate Deployment

    ```bash
    # Use VS Code to look at main.tf
    # Use Terraform CLI Integration to Execute Hashicorp Script (main.tf)
    code .
    terraform init
    terraform plan
    terraform apply
    # Validate Deployment
    az group list -o table | grep $RG
    az container list -o table
    az container show -g $RG -n aci-helloworld
    curl $(az container show -g $RG -n aci-helloworld -o tsv --query "ipAddress.fqdn")
    az container show -g $RG -n aci-helloworld -o tsv --query "ipAddress.fqdn"
    ```

4. Deploy Multi-Region Solution using Web App for Containers

    * Clone Repository
    * Build Docker Container using ACR Build
    * Enable ACR Geo-Replication
    * Deploy Container Image to East US and West US
    * Validate Deployment

    ```bash
    # ACR Premium SKU Already Created
    # Go into Portal & Enable Geo-Replication to West US
    # Log into ACR
    cd ~
    git clone https://github.com/Azure-Samples/acr-helloworld.git
    cd acr-helloworld
    # Update Dockerfile with ACR Registry Name
    az acr show -n ${ACR_NAME} --query "{acrLoginServer:loginServer}" -o table
    code .
    # Build Image
    az acr repository list -n ${ACR_NAME}
    az acr build --registry ${ACR_NAME} -f ./AcrHelloworld/Dockerfile --image acr-helloworld:v1 .
    az acr repository list -n ${ACR_NAME}
    az acr repository show-manifests -n ${ACR_NAME} --repository acr-helloworld
    az acr repository show-tags -n ${ACR_NAME} --repository acr-helloworld
    # Deploy to Web App for Containers Using Portal
    ```

## Docs / References

* [Azure Portal Docs](https://azure.microsoft.com/en-ca/features/azure-portal/)
* [Azure Cloud Shell Docs](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)
* [Azure Container Registry Build Docs](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-build-overview)
* [Azure Container Instances Docs](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-overview)
* [Web App for Containers Docs](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-intro)