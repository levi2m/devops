
# 2 - Usar o GitOps com Flux v2 no Azure Arc Kubernetes

O GitOps é uma metodologia de gerenciamento de configuração e implantação de aplicativos para Kubernetes que usa o Git como fonte de verdade. O Flux v2 é uma ferramenta de GitOps que automatiza a implantação de aplicativos e a sincronização de configurações do Git com um cluster Kubernetes. Neste tutorial, você aprenderá como usar o GitOps com o Flux v2 para gerenciar a configuração e a implantação de aplicativos em um cluster Kubernetes do Azure Arc.

## Pré-requisitos

Antes de começar, você precisa dos seguintes itens:

-   Uma conta do Azure com permissões para criar recursos.
-   Um cluster Kubernetes do Azure Arc.
-   O Azure CLI instalado.

## Criar um repositório Git

1.  Crie um novo repositório Git no GitHub ou em outro serviço Git.
    
2.  Clone o repositório Git para o seu computador:
        
    `git clone <URL do repositório Git>` 
    

## Instalar o Flux v2

1.  Adicione o repositório do Helm do Flux v2:
        
    `helm repo add fluxcd https://charts.fluxcd.io` 
    
2.  Instale o Helm Operator:
        
    `kubectl apply -f https://raw.githubusercontent.com/fluxcd/helm-operator/main/deploy/crds.yaml
    helm upgrade -i helm-operator fluxcd/helm-operator --namespace flux-system --set helm.versions=v3` 
    
3.  Instale o Flux:
        
    `flux bootstrap git --url=<URL do repositório Git> --branch=main --path=./clusters/cluster1 --namespace=flux-system` 
    
4.  Aplique o arquivo de configuração para que o Flux possa acessar o repositório Git:
        
    `flux create source git flux-system --url=<URL do repositório Git> --branch=main --interval=1m --export > ./clusters/cluster1/flux-system/gotk-sync.yaml` 
    
5.  Aplique o arquivo de configuração para implantar os aplicativos:
        
    `flux create kustomization flux-system --source=flux-system --path="./clusters/cluster1" --prune=true --interval=1m --export > ./clusters/cluster1/flux-system/kustomization.yaml` 
    

## Implantar os aplicativos com o Flux v2

1.  Crie um arquivo YAML para implantar um aplicativo. Por exemplo, o seguinte YAML implanta um deployment do nginx:
        
    `apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: nginx-deploy
      namespace: default
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: nginx
      template:
        metadata:
          labels:
            app: nginx
        spec:
          containers:
          - name: nginx
            image: nginx:latest
            ports:
            - containerPort: 80` 
    
2.  Salve o arquivo YAML em uma pasta no seu repositório Git.
    
3.  Adicione, confirme e faça push dos arquivos YAML para o seu repositório Git:
        
    `git add .
    git commit -m "Adicionar configuração do Flux"
    git push` 
    
4.   Verifique se o controlador do Flux está em execução:
        
      `kubectl -n flux-system logs deployment/flux -f` 
    
5.  Verifique se o deployment foi implantado:
        
    `kubectl get deployment nginx-deploy` 
    

Se tudo estiver configurado corretamente, o controlador do Flux detectará as alterações no seu repositório Git e implantará o deployment do nginx no cluster Kubernetes do Azure Arc. Se você quiser atualizar ou excluir o deployment, basta fazer as alterações no arquivo YAML e fazer push para o seu repositório Git. O Flux detectará as alterações e as implantará automaticamente no cluster Kubernetes.

## Conclusão

O GitOps com o Flux v2 é uma maneira poderosa e fácil de gerenciar a configuração e a implantação de aplicativos em um cluster Kubernetes do Azure Arc. Neste tutorial, você aprendeu como usar o GitOps com o Flux v2 para implantar um deployment do nginx em um cluster Kubernetes do Azure Arc. Agora você pode usar essa mesma metodologia para implantar seus próprios aplicativos em um cluster Kubernetes do Azure Arc.
