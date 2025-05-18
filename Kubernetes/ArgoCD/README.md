Starting out with an empty cluster.

Create an application configuration repo

argocdc-app-config

On my Ubuntu server I utilized [Canonical Kubernetes](https://ubuntu.com/kubernetes) to setup 

## Step 1

Reference the [Getting Started guide for ArgoCD](https://argo-cd.readthedocs.io/en/stable/getting_started/)

```shell
kubctl create namespace argocd
kubctl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

This will create a new namespace, argocd, where Argo CD services and application resources will live.

This installs:

- ArgoCD API server
- UI
- Repo server
- Application controller
- Dex (for login/auth)


**Check Installation Progress**

```shell
kubectl get pods -n argocd -w
```

Wait until all pods show STATUS: Running.

## Step 4. Expose ArgoCD API Server



## References

- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)

