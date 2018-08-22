# A Lap Around the Azure Portal and Cloud Shell

This is a walkthrough of the different areas of the Azure Portal along with what is possible with Azure Cloud Shell using demos.

## Prerequisites

* Access to an Azure Subscription and Logged In to [Azure Portal](http://portal.azure.com)

## Azure Portal

1. Terminology

    * Navigation Bar
    * Portal Settings
        * Search
        * Bell (Notifications)
        * Cloud Shell
        * Gear (UI Settings)
        * Send Feedback
        * Ask for Help
        * Directory and Subscription Filter
    * Identity & Switch Directory
    * Dashboard

2. Dashboards

    * Default with Azure Status
    * Creating Custom Dashboard
    * Show Existing Custom Dashboard

3. Navigation Bar

    * Resource Groups & Resources - Create RG
    * All Services - Pin AKS

4. Create a Resource, Notification, Security & Support

    * Azure Market Place (AMP)
    * Azure Container Registry - Premium SKU
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

1. Azure Container Registry Build & Secure Deploy to ACI

    * Git Clone
    * Build Container Image using ACR Build
    * Create Azure Key Vault and Service Principal for Secure ACI Deployment
    * Deploy to ACI

2. Using Terraform to Deploy Containers

    * Introduce VS Code Integration
    * Introduction to Hashicorp Terraform
    * Drill into [main.tf](main.tf)
    * Execute Terraform Script using CLI
    * Validate Deployment

3. Deploy Multi-Region Solution using Web App for Containers

    * Clone Repository
    * Build Docker Container using ACR Build
    * Enable ACR Geo-Replication
    * Deploy Container Image to East US and West US
    * Validate Deployment

## Docs / References

* [Azure Portal Docs](https://azure.microsoft.com/en-ca/features/azure-portal/)
* [Azure Cloud Shell Docs](https://docs.microsoft.com/en-us/azure/cloud-shell/overview)
* [Azure Container Registry Build Docs](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-build-overview)
* [Azure Container Instances Docs](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-overview)
* [Web App for Containers Docs](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-intro)