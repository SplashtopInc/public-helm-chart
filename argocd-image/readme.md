# Custom Tooling

Argo CD bundles preferred versions of its supported templating tools (helm, kustomize, ks, jsonnet) as part of its container images. Sometimes, it may be desired to use a specific version of a tool other than what Argo CD bundles. Some reasons to do this might be:

* To upgrade/downgrade to a specific version of a tool due to bugs or bug fixes.
* To install additional dependencies which to be used by kustomize's configmap/secret generators (e.g. curl, vault, gpg, AWS CLI)
* To install a config management plugin

As the Argo CD repo-server is the single service responsible for generating Kubernetes manifests, it can be customized to use alternative toolchain required by your environment.

# BYOI (Build Your Own Image)

Sometimes replacing a binary isn't sufficient and you need to install other dependencies.   
The following example builds an entirely customized repo-server from a Dockerfile, installing extra dependencies that may be needed for generating manifests.   

## Helm plugins

> v1.5

Argo CD is un-opinionated on what cloud provider you use and what kind of Helm plugins you are using, that's why there are no plugins delivered with the ArgoCD image.

But sometimes it happens you would like to use a custom plugin. One of the cases is that you would like to use Google Cloud Storage or Amazon S3 storage to save the Helm charts, for example: https://github.com/hypnoglow/helm-s3 where you can use `s3://` protocol for Helm chart repository access.

In order to do that you have to prepare your own ArgoCD image with installed plugins.

Example `Dockerfile`:

```
FROM argoproj/argocd:v1.5.7

USER root
RUN apt-get update && \
    apt-get install -y \
        curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

USER argocd

ARG HELM_S3_PLUGIN_VERSION="0.10.0"
ARG HELM_S3_PLUGIN_REPO="https://github.com/hypnoglow/helm-s3.git"

RUN helm plugin install ${HELM_S3_PLUGIN_REPO} --version ${HELM_S3_PLUGIN_VERSION}

ENV HELM_PLUGINS="/home/argocd/.local/share/helm/plugins/"
```

You have to remember about `HELM_PLUGINS` environment property - this is required for plugins to work correctly.

After that you have to use your custom image for ArgoCD installation.

## Helm Version

Argo CD will assume that the Helm chart is v3 (even if the apiVersion field in the chart is Helm v2), unless v2 is explicitly specified within the Argo CD Application (see below).

If needed, it is possible to specifically set the Helm version to template with by setting the `helm-version` flag on the cli (either v2 or v3):

```bash
argocd app set helm-guestbook --helm-version v3
```

Or using declarative syntax:

```yaml
spec:
  source:
    helm:
      version: v3
```

***

# Push commands for stp-argocd-guj52ta
1. Retrieve an authentication token and authenticate your Docker client to your registry. 
Use the AWS CLI:
```
aws ecr-public get-login-password --region us-east-1 --profile aad-sso-tperd | docker login --username AWS --password-stdin public.ecr.aws/k7c3r6j5
```

2. Build your Docker image using the following command. For information on building a Docker file from scratch see the instructions here . You can skip this step if your image is already built:
```
docker build -t stp-argocd-guj52ta .
```

3. After the build completes, tag your image so you can push the image to this repository:
```
docker tag stp-argocd-guj52ta:latest public.ecr.aws/k7c3r6j5/stp-argocd-guj52ta:latest
```

4. Run the following command to push this image to your newly created AWS repository:
```
docker push public.ecr.aws/k7c3r6j5/stp-argocd-guj52ta:latest
```