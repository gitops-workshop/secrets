# Secrets

Set-up:

```
brew install kubeseal
```

Install `sealed-secrets` chart into `kube-system`, e.g.

```
argocd app create sealed-secrets --repo https://kubernetes-charts.storage.googleapis.com --helm-chart sealed-secrets --revision '*' --dest-server https://kubernetes.default.svc --dest-namespace kube-system 
argocd app sync sealed-secrets
```

```
kubectl create ns my-secrets
```

Example:

```
argocd app create my-secrets --repo https://github.com/gitops-workshop/argo-cd-demos.git --path . --revision 'master' --dest-server https://kubernetes.default.svc --dest-namespace my-secrets
argocd app sync my-secrets
```
