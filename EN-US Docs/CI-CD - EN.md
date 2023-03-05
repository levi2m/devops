
# 2 - Azure Arc Kubernetes: GitOps and Flux v2 Tutorial

This tutorial will guide you through the process of using GitOps and Flux v2 to manage the configuration and deployment of applications on an Azure Arc Kubernetes cluster.

## Prerequisites

Before you begin, you will need the following:

-   An Azure account with permission to create Azure Arc and Kubernetes resources.
-   A Kubernetes cluster that is registered with Azure Arc.
-   A Git account (GitHub, GitLab, or Bitbucket) to host your YAML files.
-   The Flux v2 installed on your Kubernetes cluster.

## Flux v2

Flux v2 is a continuous deployment tool that supports the GitOps methodology. Flux v2 uses Git as the source of truth to manage the configuration and deployment of applications in Kubernetes environments. Flux v2 can be used in conjunction with Azure Arc Kubernetes to manage the configuration and deployment of applications on a Kubernetes cluster.

Flux v2 includes the following components:

-   The Flux controller: a Kubernetes controller that is responsible for synchronizing the state of the cluster with the state defined in the Git YAML files.
-   The Flux toolkit: a set of CLI tools that facilitate the configuration and management of Flux.

## Configuring Flux v2

To configure Flux v2, follow these steps:

1.  Install Flux v2 on your Kubernetes cluster.
    
2.  Create a Git repository to host your YAML files. Ensure that the repository has at least one YAML file.
    
3.  Configure the Flux controller to connect to your Git repository. The Flux controller will monitor your Git repository and synchronize the state of the cluster with the state defined in the Git YAML files.
    
4.  Configure the Flux controller to deploy your applications. This involves creating a Flux YAML file that defines the deployment policies for your applications. The Flux controller will monitor the Git repository at regular intervals and synchronize the state of the cluster with the state defined in the Git YAML files. If there is a change in the Git YAML files, the Flux controller will automatically update the state of the cluster to reflect that change.
    
5.  You can use the Flux toolkit to manage Flux v2. The Flux toolkit includes a set of CLI tools that facilitate the configuration and management of Flux.
    
6.  To deploy a new application or update an existing application, simply make a change to the Git YAML files and commit and push the changes to the Git repository. The Flux controller will detect the change and automatically deploy the new application or update the existing application.
    

## Apply a Flux configuration

To apply a Flux configuration to your Azure Arc Kubernetes cluster, follow these steps:

1.  Connect to your Kubernetes cluster using the Azure CLI:
        
    `az login
    az account set --subscription <subscription-id>
    az connectedk8s connect --name <cluster-name> --resource-group <resource-group-name>
    az connectedk8s show --name <cluster-name> --resource-group <resource-group-name>
    kubectl config use-context <cluster-name>` 
    
2.  Install Flux v2 on your Kubernetes cluster:
        
    `flux install` 
    
3.  Create a Git repository to host your Flux YAML files.
    
4.  Configure the Flux controller to connect to your Git repository:
        
    `export GITHUB_TOKEN=<your-GitHub-token>
    flux create source git flux-system \
    --url=<your-Git-repo> \
    --branch=<your-branch> \
    --interval=1m \
    --export > ./clusters/<cluster-name>/flux-system/gotk-sync.yaml` 
    
5.  Configure the Flux controller to deploy your applications:
        
    `flux create kustomization flux-system \
    --source=flux-system \
    --path="./" \
    --prune=true \
    --validation`

6.  Commit and push the YAML files to your Git repository:
        
    `git add .
    git commit -m "Add Flux configuration"
    git push` 
    
7.  Verify that the Flux controller is running:
        
    `kubectl -n flux-system get pods` 
    
    You should see output similar to the following:
        
    `NAME                                     READY   STATUS    RESTARTS   AGE
    gotk-components-cert-manager-59f6xb24    1/1     Running   0          3m18s
    gotk-components-controller-8479c77fb7   1/1     Running   0          3m18s
    gotk-components-webhook-6cc96b4c85       1/1     Running   0          3m18s` 
    
8.  Verify that the Flux controller has synchronized with the Git repository:
        
    `flux get sources git` 
    
    You should see output similar to the following:
        
    `NAME         READY  MESSAGE                                                         REVISION                                      SUSPENDED
    flux-system True   Fetched revision: main/07c32f6a148e6a59fb75b2d6ca906532ee2b83f7  main/07c32f6a148e6a59fb75b2d6ca906532ee2b83f7  False` 
    
9.  Verify that the Flux controller has deployed the applications:
        
    `kubectl get deployments` 
    
    You should see output similar to the following:
        
    `NAME          READY   UP-TO-DATE   AVAILABLE   AGE
    nginx-deploy  1/1     1            1           4m21s` 
    

Congratulations! You have successfully used GitOps and Flux v2 to manage the configuration and deployment of applications on an Azure Arc Kubernetes cluster.

## Conclusion

GitOps and Flux v2 are powerful methodologies for managing the configuration and deployment of applications in Kubernetes environments. By using Git as the source of truth, you can manage the configuration and deployment of applications declaratively, allowing the operations team to manage infrastructure as code. Azure Arc Kubernetes and Flux v2 are a powerful combination that enables you to manage Kubernetes clusters across multiple locations and clouds with the same Azure experience.
