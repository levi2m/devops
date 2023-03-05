
## Como criar um cluster Kubernetes usando Terraform e Azure Kubernetes Service (AKS)

Neste tutorial, você aprenderá a usar o Terraform para criar um cluster Kubernetes em Azure Kubernetes Service (AKS).

### Pré-requisitos

-   [Conta do Azure](https://azure.com/free)
-   [Terraform](https://www.terraform.io/downloads.html) instalado
-   [kubectl](https://kubernetes.io/docs/tasks/tools/) instalado

### Etapa 1: Crie um grupo de recursos

Antes de criar o cluster AKS, você precisa criar um grupo de recursos. Para criar um grupo de recursos, execute o seguinte comando:

`az group create --name myResourceGroup --location eastus` 

### Etapa 2: Crie um arquivo de configuração Terraform

Crie um arquivo chamado `main.tf` e adicione o seguinte código:

    provider "azurerm" {
      features {}
    }
    
    resource "azurerm_resource_group" "myResourceGroup" {
      name     = "myResourceGroup"
      location = "eastus"
    }
    
    module "aks" {
      source              = "Azure/aks"
      resource_group_name = azurerm_resource_group.myResourceGroup.name
      dns_prefix          = "myakscluster"
      agent_count         = 2
    }

Este arquivo contém as configurações necessárias para criar um cluster AKS com dois nós de agente.

### Etapa 3: Inicialize e aplique as configurações do Terraform

No terminal, execute os seguintes comandos:

`terraform init
terraform apply` 

Quando solicitado, confirme que deseja criar os recursos.

### Etapa 4: Configure o Kubernetes

Execute o seguinte comando para configurar o Kubernetes:

`az aks get-credentials --resource-group myResourceGroup --name myakscluster` 

Este comando adiciona as credenciais do Kubernetes ao seu arquivo `~/.kube/config`.

### Etapa 5: Verifique o cluster

Execute o seguinte comando para verificar se o cluster foi criado com sucesso:

`kubectl get nodes` 

Este comando lista os nós no cluster. Se tudo estiver funcionando corretamente, você deverá ver a saída com informações sobre os dois nós de agente.

### Etapa 6: Limpe os recursos

Execute o seguinte comando para remover todos os recursos criados neste tutorial:

`terraform destroy` 

Quando solicitado, confirme que deseja destruir os recursos.

## Conclusão

Neste tutorial, você aprendeu a criar um cluster Kubernetes usando o Terraform e o AKS. Agora você pode começar a implantar seus aplicativos no Kubernetes.
