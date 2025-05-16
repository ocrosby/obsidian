Starting out with an empty cluster.

Create an application configuration repo

argocdc-app-config

## Step 1

Reference the [Getting Started guide for ArgoCD](https://argo-cd.readthedocs.io/en/stable/getting_started/)

```shell
kubctl create namespace argocd
kubctl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable
```

This will create a new namespace, argocd, where Argo CD services and application resources will live.

