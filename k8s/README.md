# K8s

## Checklist

* Ensure you are using the correct [context](#kubeconfig).
* Get a local test cluster with [K3D](#k3d).

## Local Setup

``` sh
# install argocd
helm upgrade --install --create-namespace -n ${NAMESPACE:-argocd} --dependency-update --timeout 5m --wait argocd ./infrastructure/argocd

# port forward
( exec -a pf8888 kubectl port-forward svc/argocd-server -n ${NAMESPACE:-argocd} 8888:443 > /dev/null ) &

# admin password
kubectl get secrets/argocd-initial-admin-secret -n ${NAMESPACE:-argocd} --template={{.data.password}} | base64 -D

# login
open http://localhost:8888

# apply meta
kubectl apply -n ${NAMESPACE:-argocd} -f .init/application.${CLUSTER:-local}.yaml
```

### K3D

> [k3d](https://k3d.io/) is a lightweight wrapper to run [k3s](https://github.com/k3s-io/k3s) (Rancher Lab’s minimal Kubernetes distribution) in docker.

* [Installation](https://k3d.io/v5.4.9/#other-installers)
* [Documentation](https://k3d.io/v5.4.9/usage/commands/k3d_cluster_create/)

Create a cluster with 1 controller and 3 workers:

``` sh
k3d cluster create local \
    -s 1 -a 3 \
    --k3s-arg "--disable=traefik@server:*" \
    --k3s-arg "--disable=servicelb@server:*" \
    --k3s-arg "--node-taint=node-role.kubernetes.io/master:NoSchedule@server:*"

# verify
kubectl get nodes
```

When done, you may simply delete the cluster via k3d:

``` sh
k3d cluster delete local
```

### kubeconfig

Ensure you're using a local context for testing...

``` sh
# current context
kubectl config current-context

# list contexts
kubectl config get-contexts

# use context
kubectl config use-context ${CONTEXT}
```

## Debug

``` sh
export CLUSTER=local
export NAMESPACE=argocd

cd meta

helm lint -f clusters/${CLUSTER:-local}/values.yaml .

# w/o cluster validation
helm template --debug -f clusters/${CLUSTER:-local}/values.yaml .

# w/ cluster validation
helm upgrade --install --debug --dry-run -n ${NAMESPACE:-argocd} -f clusters/${CLUSTER:-local}/values.yaml argocd-meta .
```
