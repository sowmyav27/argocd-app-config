# Install and configure ArgoCD in a cluster
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

# Deploy vCluster using ArgoCD
Assumption - ArgoCD is deployed on the same cluster where the application needs to be deployed.
1. Configure your app similar to this one - https://github.com/sowmyav27/argocd-app-config/blob/main/vcluster/my-vcluster-app.yaml
2. Configure argoCD application like - https://github.com/sowmyav27/argocd-app-config/blob/main/argocd-application.yaml
3. Apply the `argocd-application.yaml` yaml in the cluster manually using
```
kubectl apply -f argocd-application.yaml
```
4. You will see a vCluster is deployed using command
```
vcluster list

     NAME  | NAMESPACE | STATUS  |    VERSION    | CONNECTED |  AGE   
  ---------+-----------+---------+---------------+-----------+--------
    argocd | argocd    | Running | 0.21.0-beta.6 |           | 6m43s  
```
5. Then to test auto-sync - make any changes in github - https://github.com/sowmyav27/argocd-app-config/blob/main/argocd-application.yaml - example: changed vCluster version to `0.20.3`
6. You will notice the application in ArgoCD will be in `Out of Sync` status.
7. Because auto-sync is configured, it will automatically sync the changes to the deployed application (Else, in the ArgoCD UI --> click on Application Details --> Under `SYNC POLICY` --> Select `Enable auto-Sync`
8. You will see version of vCluster will changed
```
vcluster list
  
     NAME  | NAMESPACE | STATUS  | VERSION | CONNECTED |  AGE    
  ---------+-----------+---------+---------+-----------+---------
    argocd | argocd    | Running | 0.20.3  |           | 19m44s  
```
