## ArgoCD Installation (K8s)

Add chart repository.
```
helm repo add argo https://argoproj.github.io/argo-helm
```

Update sub-charts.
```
helm dependency update
```

Install the chart.
```
helm upgrade --install \
    -n argocd \
    --set argo-cd.global.image.repository=rainchei/docker-argocd \
    --set argo-cd.global.image.tag=0.0.1 \
    --set 'argo-cd.repoServer.env[0].name=AWS_REGION' \
    --set 'argo-cd.repoServer.env[0].value=ap-northeast-1' \
    argocd .
```
