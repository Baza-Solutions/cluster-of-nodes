Cold install / start of the cluster
---
First, execute this command to install the dependencies for cold start of the cluster:

```shell
helm repo add argo-cd https://argoproj.github.io/argo-helm
helm dep update charts/argo-cd/

kubectl create namespace argo-cd
helm install argo-cd charts/argo-cd -f charts/argo-cd/values.yaml -n argo-cd
```

This is not needed anymore due to all dependencies will be managed by ArgoCD itself.

We only allow outside connections to our cluster through NGINX Proxies. 
Everything else is encapsulated in a private network. 

## Argo Applications Setup

- Root 
- NGINX -> Controller that handles the traffic to the other applications. 
- TMKMS -> This is a server that holds the priv_validator_key and signs the blocks. Needs to talk to Fortanix DSM through the root application. 
- Celestia-App -> Our celestia-app node that talks to the other nodes in the network. Needs to talk to TMKMS to get the priv_validator_key data.

```shell
kubectl apply -f config/app-of-apps.yaml
kubectl apply -f ingress-nginx/ingress-nginx-app.yaml
kubectl apply -f validator/validator-application.yaml
```