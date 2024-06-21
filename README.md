# argocd-app-config

## Install and configure ArgoCD in a cluster
1. To Install ArgoCD
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

2. Access ArgoCD UI
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

The default username is `admin`. Retrieve the password with:
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```
3. Create an ArgoCD Application to Watch the Terraform Repo and apply the yaml
```
kubectl apply -f argocd-application.yaml
```


## Access ArgoCD UI with NodePort

1. Patch the argocd-server Service
```
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
```
2. Get NodePort service
```
kubectl get svc argocd-server -n argocd
```
The output will show a port in the NodePort range (e.g., 30000-32767).

3. Access the ArgoCD UI:

- Find the external IP of one of your nodes.
- Access the ArgoCD UI at https://<node-ip>:<nodeport>.
