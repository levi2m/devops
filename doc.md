
# Documentação de IAC para criação de recurso AKS no Azure

Este documento descreve as etapas necessárias para a criação de um cluster AKS (Azure Kubernetes Services) usando Infrastructure as Code (IAC) no Azure. Para este tutorial, será utilizado o Terraform.

## Pré-requisitos

Antes de começar, é necessário ter os seguintes itens instalados e configurados:

-   Azure CLI
-   Terraform
-   Conta do Azure

## Passo 1: Criação do arquivo de configuração

O primeiro passo é criar um arquivo de configuração para o Terraform. Este arquivo contém as informações necessárias para a criação do recurso AKS.


    # Provider
    provider "azurerm" {
      features {}
    }
    
    # Resource Group
    resource "azurerm_resource_group" "aks_rg" {
      name     = "myAKSResourceGroup"
      location = "eastus"
    }
    
    # Virtual Network
    resource "azurerm_virtual_network" "aks_vnet" {
      name                = "myAKSVnet"
      address_space       = ["10.0.0.0/16"]
      location            = azurerm_resource_group.aks_rg.location
      resource_group_name = azurerm_resource_group.aks_rg.name
    }
    
    # Subnet
    resource "azurerm_subnet" "aks_subnet" {
      name                 = "myAKSSubnet"
      resource_group_name  = azurerm_resource_group.aks_rg.name
      virtual_network_name = azurerm_virtual_network.aks_vnet.name
      address_prefixes     = ["10.0.1.0/24"]
    }
    
    # AKS Cluster
    resource "azurerm_kubernetes_cluster" "aks_cluster" {
      name                = "myAKSCluster"
      location            = azurerm_resource_group.aks_rg.location
      resource_group_name = azurerm_resource_group.aks_rg.name
      dns_prefix          = "myAKSDNSPrefix"
    
      default_node_pool {
        name            = "default"
        node_count      = 1
        vm_size         = "Standard_D2_v2"
        vnet_subnet_id  = azurerm_subnet.aks_subnet.id
      }
    
      service_principal {
        client_id     = "your_client_id"
        client_secret = "your_client_secret"
      }
    
      tags = {
        Environment = "Dev"
      }
    }

Este arquivo de configuração irá criar um novo recurso AKS no Azure com um único nó. É possível personalizar as opções de acordo com as suas necessidades.

## Passo 2: Autenticação com o Azure

Para se autenticar no Azure, é necessário utilizar o Azure CLI. Execute o seguinte comando para fazer login:

`az login` 

Será necessário inserir suas credenciais do Azure para completar o login.

## Passo 3: Inicialização do Terraform

Antes de criar o recurso, é necessário inicializar o Terraform com o seguinte comando:

`terraform init` 

Este comando irá baixar todos os módulos necessários para criar o recurso AKS.

## Passo 4: Criação do recurso AKS

Agora que o Terraform está inicializado, é possível criar o recurso AKS com o seguinte comando:

`terraform apply` 

Será necessário confirmar a criação do recurso digitando "yes". O processo pode levar alguns minutos para ser concluído.

## Passo 5: Verificação do recurso AKS criado

Após a criação do recurso AKS, é possível verificar se o cluster foi criado com sucesso. Para isso, execute o seguinte comando no Azure CLI:

`az aks show --resource-group myAKSResourceGroup --name myAKSCluster --query "provisioningState"` 

Este comando irá retornar o estado de provisionamento do cluster. O resultado deve ser "Succeeded" se o cluster foi criado com sucesso.

## Passo 6: Configuração do kubectl

Para gerenciar o cluster AKS, é necessário configurar o kubectl. Para isso, execute o seguinte comando no Azure CLI:

`az aks get-credentials --resource-group myAKSResourceGroup --name myAKSCluster` 

Este comando irá configurar o kubectl com as informações de autenticação do cluster AKS.

## Passo 7: Verificação do cluster AKS usando o kubectl

Após configurar o kubectl, é possível verificar o estado do cluster AKS usando o seguinte comando:

`kubectl get nodes` 

Este comando irá listar todos os nós do cluster AKS. Se o cluster foi criado com sucesso, o comando deve retornar informações sobre o nó criado.

## Passo 8: Limpeza dos recursos

Após concluir as atividades no cluster AKS, é importante limpar os recursos para evitar cobranças adicionais. Para isso, execute o seguinte comando no Terraform:

`terraform destroy` 

Este comando irá destruir todos os recursos criados no Azure para o cluster AKS.

## Conclusão

Este tutorial demonstrou como criar um cluster AKS usando IAC com o Terraform no Azure. É possível personalizar as opções de acordo com as suas necessidades e gerenciar o cluster usando o kubectl. Lembre-se sempre de limpar os recursos após concluir as atividades no cluster para evitar cobranças adicionais.
