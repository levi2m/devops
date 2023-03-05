## Objetivo 🎯

O objetivo deste desafio é criar um ambiente de deployment baseado em GitOps usando Flux e Kustomize em um cluster AKS no Azure.

## Passo 1: Criar um cluster Azure Kubernetes Services 🚀

1.1. Crie uma conta no Azure ou faça login na sua conta existente.

1.2. Crie um grupo de recursos para o seu ambiente.

1.3. Crie um cluster AKS usando Terraform ou Terragrunt. Você pode usar o módulo AKS para Terraform suportado pela MS: [https://registry.terraform.io/modules/aztfmod/caf/azurerm/5.1.2/submodules/aks](https://registry.terraform.io/modules/aztfmod/caf/azurerm/5.1.2/submodules/aks)

## Passo 2: Criar um Projeto no Azure DevOps 🛠️

2.1. Acesse o portal do Azure DevOps.

2.2. Crie um novo projeto.

2.3. Crie um novo repositório chamado "Desafio-Azure-Devops" e compartilhe com [jeyvisson.gomes@devoteam.com](mailto:jeyvisson.gomes@devoteam.com)

## Passo 3: Configurar Flux e Kustomize 🔧

3.1. Instale o Flux CLI na sua máquina local.

3.2. Configure o Flux para se conectar ao cluster AKS criado no Passo 1.

3.3. Crie um repositório no GitHub para o Kustomize e faça o commit dos arquivos de configuração.

3.4. Crie um fluxcd.io GitHub App para se conectar ao repositório.

3.5. Configure o Kustomize para usar as imagens dos containers do repositório de imagens do Azure.

## Passo 4: Configurar CD baseado em GitOps 🚚

4.1. Configure o Flux para reconciliar o repositório em Azure DevOps de 1 em 1 minuto.

4.2. Faça o push das atualizações no branch master do repositório em Azure DevOps.

4.3. O Kustomize e o Flux devem garantir que as atualizações sejam realizadas no cluster a cada push ao repositório master.

## Passo 5: Deploy do POD de exemplo 🚀

5.1. Faça o deploy de um POD de exemplo funcional, como Aspnetapp, Nginx ou Busybox.

5.2. Use operações de replace baseadas em Overlay de Kustomizations.

----------

🎉 Parabéns! Você concluiu o desafio Azure DevOps. Agora você tem um ambiente de deployment baseado em GitOps com Flux e Kustomize em um cluster AKS no Azure. Se tiver alguma dúvida, não hesite em entrar em contato.
