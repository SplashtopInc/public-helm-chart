# argocd version
FROM argoproj/argocd:latest

# Switch to root for the ability to perform install
USER root

# Install tools needed for your repo-server to retrieve & decrypt secrets, render manifests 
# (e.g. curl, awscli, gpg, sops)
RUN apt-get update && \
    apt-get install -y \
        curl \
        awscli \
        gpg && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* 

# Switch back to non-root user
USER argocd

# install a specific release version
ARG HELM_S3_PLUGIN_VERSION="0.10.0"
ARG HELM_S3_PLUGIN_REPO="https://github.com/hypnoglow/helm-s3.git"

# Helm plugin install https://github.com/hypnoglow/helm-s3.git --version 0.10.0
RUN helm plugin install ${HELM_S3_PLUGIN_REPO} --version ${HELM_S3_PLUGIN_VERSION}

# Helm plugins live in $HELM_PLUGINS. You can find the current value of this
ENV HELM_PLUGINS="/home/argocd/.local/share/helm/plugins/"

# s3 region
ENV AWS_REGION="ap-northeast-1"