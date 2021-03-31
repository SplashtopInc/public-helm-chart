# Create a public Helm chart repository with GitHub Pages

## Create a helm chart from scratch (or copy your own)
As a pre-requisite of this stage, you need to have the Helm CLI installed and initialized. Please find here the instructions in case you haven’t install Helm client yet.

use the directory ./helm-chart-sources/ for the sources of our charts. You might want to copy into ./helm-chart-sources/ your charts instead:

```
mkdir ./helm-chart-sources && cd ./helm-chart-sources/

helm create helm-chart-sources/helm-chart-test
```

## Create the Helm chart package
modify the chart.yaml

ensure the chart version and app version
![chart value graph](./readme-img/modfiy-chart-value.png)

## Lint the chart

```
helm lint helm-chart-sources/*
```
![helm lint](./readme-img/helm-lint.png)

## Create the Helm chart package
```
helm package helm-chart-sources/*
```
![Helm package](./readme-img/Helm-package.png)

## Create the Helm chart repository index

According to [Helm](https://helm.sh/docs/topics/chart_repository/#create-a-chart-repository)

A chart repository is an HTTP server that houses an index.yaml file and optionally some packaged charts. When you're ready to share your charts, the preferred way to do so is by uploading them to a chart repository.

```
helm repo index --url https://splashtopinc.github.io/public-helm-chart/ .
```
![helm index](./readme-img/helm-index.png)

### Add the Helm chart to an existing repository

Whenever you need to add a new chart to the Helm chart repository, it’s mandatory for you to regenerate the index.yaml file. The $ helm repo index command will completely rebuild the index.yaml file from scratch, including only the charts that it finds locally, which very likely is our case. However, it worth notice that you can use the --merge flag to incrementally add new charts to an existing index.yaml:

```
helm repo index --url https://splashtopinc.github.io/public-helm-chart/ --merge index.yaml .
```

## Push the git repository on GitHub

```
git add . && git commit -m "argocd chart version update" && git push origin master
```

## Configure the repository as a Github pages site

![github pages](./readme-img/gitHub-pages.png)

## Add the splashtop helm repo

```
helm repo add splashtop https://splashtopinc.github.io/public-helm-chart/
```
![helm add repo](./readme-img/helm-add-repo.png)

## helm 

```
# --dry-run
helm install argocd splashtop/argo-cd -n argocd --dry-run

# install 
helm install argocd splashtop/argo-cd -n argocd 

# upgrade
helm upgrade argocd splashtop/argo-cd -n argocd

```
![dry-run](./readme-img/dry-run.png)

Ref:   

https://medium.com/@mattiaperi/create-a-public-helm-chart-repository-with-github-pages-49b180dbb417

https://helm.sh/docs/topics/chart_repository/#create-a-chart-repository