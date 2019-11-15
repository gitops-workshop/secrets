# Demo: Secrets

### 1. Install Sealed Secrets Into Your Cluster

```
argocd app create sealed-secrets --repo https://kubernetes-charts.storage.googleapis.com --helm-chart sealed-secrets --revision '1.5.0' --dest-server https://kubernetes.default.svc --dest-namespace kube-system 
argocd app sync sealed-secrets
```

### 2. Install Kubeseal

```
brew install kubeseal
```

### 3. Create Secret

```
echo -n bar | kubectl create secret generic mysecret --dry-run --from-file=foo=/dev/stdin -o json > mysecret.json
```

### 4. Seal The Secret

```
kubeseal --cert https://argo-cd-kubecon.apps.argoproj.io/secret/cert.pem < mysecret.json > mysealedsecret.json
```

### 5. Delete The Un-Sealed Secret

```
rm mysecret.json
```

### 6. Commit The Change

```
git add mysealedsecret.json
git commit -am 'Add secret'
git push
```

### 7. Create An App

```
argocd app create ${username}-secrets --repo https://github.com/${username}/secrets.git --path . --revision HEAD --dest-server https://kubernetes.default.svc --dest-namespace default
argocd app sync ${username}-secrets
```

Open the UI to observe the created secret, or use CLI:

```
kubectl -n default get secrets -o yaml
```

Decode `YmFy` using http://bit.ly/cnvcode.

### Clean-Up

```
argocd app delete ${username}-secrets
argocd app delete sealed-secrets
```
