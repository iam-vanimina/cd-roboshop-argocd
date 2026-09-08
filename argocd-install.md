
Install ArgoCD using helm on k8s cluster (in our case docker desktop)

To install Argo CD on your Kubernetes cluster using Helm, you can use the official community-maintained chart.

[1] (https://argo-cd.readthedocs.io/en/stable/operator-manual/installation/), 

[2] (https://devopscube.com/setup-argo-cd-using-helm/)

Here is the step-by-step installation guide:
1. Add the Argo CD Helm RepositoryFirst, add the official repository to your local Helm setup and run an update to fetch the latest chart indexes:

# Add the official Argo repository

`helm repo add argo https://argoproj.github.io/argo-helm`

# Update your local chart registry

`helm repo update`

<img width="439" height="205" alt="image" src="https://github.com/user-attachments/assets/6fc0217a-4d54-403b-8f14-9307a629c04e" />


# 2. Install ArgoCD Deploy the chart into a dedicated namespace (argocd). 
The --create-namespace flag will automatically build the namespace if it doesn't already exist:


# bash

`helm install argocd argo/argo-cd \`

 ` --namespace argocd \ `
 
  ` --create-namespace `


# Verify the Deployment
It can take a couple of minutes for all the microservices to pull their images and initialize. You can monitor the deployment status by running
  
  `kubectl get pods -n argocd`

  Ensure all pods (such as argocd-server, argocd-repo-server, and argocd-application-controller) show a status of Running
  
