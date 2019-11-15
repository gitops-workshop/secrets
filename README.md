# Demo: Secrets

### 1. Set-Up

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

### 2. Create And Seal Secret

Create the secret:

```
echo -n bar | kubectl create secret generic mysecret --dry-run --from-file=foo=/dev/stdin -o json >mysecret.json
```

Seal it:

```
kubeseal --cert http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/sealed-secrets/proxy/v1/cert.pem < mysecret.json > mysealedsecret.json
git add mysealedsecret.json
git commit -am 'Add secret'
git push
```

### 3. Create An App

```
argocd app create my-secrets --repo https://github.com/gitops-workshop/argo-cd-demos.git --path . --revision 'master' --dest-server https://kubernetes.default.svc --dest-namespace my-secrets
argocd app sync my-secrets
```

Open the UI to observe the created secret, or use CLI:

```
kubectl -n my-secrets get secrets -o yaml
```

Decode `YmFy` using http://bit.ly/cnvcode.

### Clean-Up

```
argocd app delete my-secrets
argocd app delete sealed-secrets
kubectl delete ns my-secrets
```
