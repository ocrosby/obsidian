Starting out with an empty cluster.

Create an application configuration repo

argocdc-app-config

On my Ubuntu server I utilized [Canonical Kubernetes](https://ubuntu.com/kubernetes) to setup a Kubernetes environment for experimentation.

**Create a symbolic link to kubectl relative to snap**

```shell
ln -s /snap/k8s/current/bin/kubectl /usr/local/bin/kubectl
```

This makes it so you don't have to execute `sudo k8s kubectl` all the time.

## Step 1. Create a Namespace for ArgoCD

Reference the [Getting Started guide for ArgoCD](https://argo-cd.readthedocs.io/en/stable/getting_started/)

**Create a namespace for ArgoCD**

```shell
kubctl create namespace argocd
```

## Step 2. Install ArgoCD Core Components

```shell
kubctl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

This installs the specified manifest in the argocd namespace.

The following core components will be installed:

- ArgoCD API server
- UI
- Repo server
- Application controller
- Dex (for login/auth)


## Step 3. Check Installation Progress

```shell
kubectl get pods -n argocd -w
```

The `-w` flag tells `kubectl` to wait until all pods show STATUS: Running.

## Step 4. Expose ArgoCD API Server

If you enabled LoadBalancer support via Canonical k8s, expose ArgoCD's server:

```shell
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

Then get the external IP:

```shell
kubectl get svc argocd-server -n argocd
```

Once the EXTERNAL-IP is available, open in browser:

```plaintext
http://<EXTERNAL-IP>
```

## Step 5. Get Initial Admin Password

The defaul tlogin is:

- Username: admin
- Password: auto-generated, stored in a Kubernetes secret

**Get it with**

```shell
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d && echo
```

## References

- [ArgoCD Tutorial for Beginners](https://www.youtube.com/watch?v=MeU5_k9ssrs)

