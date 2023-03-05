1- Creating a Kubernetes Cluster with Terraform and AKS

This guide outlines the steps necessary to create a Kubernetes cluster using Terraform and AKS on Azure.

## Prerequisites

Before you begin, you will need the following:

-   An Azure account with sufficient permissions to create a Kubernetes cluster
-   Terraform installed on your local machine
-   Azure CLI installed on your local machine
-   Kubernetes CLI (`kubectl`) installed on your local machine

## Steps

1.  Create an Azure service principal
    
    Use the Azure CLI to create a service principal that Terraform will use to authenticate with Azure:
    
    
    `az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>"` 
    
    Make note of the `appId`, `password`, and `tenant` values returned by this command, as you will need them in the following steps.
    
2.  Create a Terraform configuration file
    
    Create a new file named `main.tf` and paste the following contents:
    
    

>     `provider "azurerm" {
>       version = ">=2.0.0"
>       features {}
>     }
>     
>     resource "azurerm_resource_group" "rg" {
>       name     = "aks-rg"
>       location = "eastus"
>     }
>     
>     resource "azurerm_kubernetes_cluster" "aks" {
>       name                = "aks-cluster"
>       location            = azurerm_resource_group.rg.location
>       resource_group_name = azurerm_resource_group.rg.name
>       dns_prefix          = "aks-cluster"
>     
>       agent_pool_profile {
>         name            = "default"
>         count           = 1
>         vm_size         = "Standard_B2s"
>         os_type         = "Linux"
>         vnet_subnet_id  = "<your-vnet-subnet-id>"
>         os_disk_size_gb = 30
>       }
>     
>       service_principal {
>         client_id     = "<your-app-id>"
>         client_secret = "<your-password>"
>       }
>     
>       tags = {
>         Environment = "Dev"
>       }
>     }
>     
>     output "kube_config" {
>       value = azurerm_kubernetes_cluster.aks.kube_config_raw
>     }`

    
Replace `<subscription-id>`, `<your-vnet-subnet-id>`, `<your-app-id>`, and `<your-password>` with the appropriate values for your environment.
    
3.  Initialize Terraform
    
    Navigate to the directory containing your `main.tf` file and run the following command:
        
    `terraform init` 
    
    This will download the necessary provider plugins and initialize your Terraform workspace.
    
4.  Apply the Terraform configuration
    
    Run the following command to apply the Terraform configuration and create the Kubernetes cluster:
        
    `terraform apply` 
    
    Review the changes that will be made and enter `yes` when prompted to confirm.
    
5.  Configure `kubectl`
    
    After the Terraform configuration has been applied, run the following command to retrieve the Kubernetes configuration:
        
    `terraform output kube_config > ~/.kube/config-aks` 
    
    This will save the configuration to `~/.kube/config-aks`.
    
    To use this configuration with `kubectl`, set the `KUBECONFIG` environment variable to the path of the configuration file:
        
    `export KUBECONFIG=~/.kube/config-aks` 
    
6.  Verify the cluster
    
    Run the following command to verify that your cluster is running:
        
    `kubectl get nodes` 
    
    This should display a list of nodes in your cluster.
    

## Conclusion

You have now created a Kubernetes cluster using Terraform and AKS on Azure. You can use this cluster to deploy and manage containerized applications.
