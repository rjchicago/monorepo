# MetalLB

## Debug

``` sh
helm lint -f clusters/${CLUSTER:-local}/values.yaml .

helm template --debug -f clusters/${CLUSTER:-local}/values.yaml .

# create namespace
kubectl create ns metallb -o yaml --dry-run=client | kubectl apply -f -

# debug/dry-run
helm upgrade --install -n metallb --debug --dry-run -f clusters/${CLUSTER:-local}/values.yaml metallb .
# NOTE: the above will fail if CRDs have not yet been applied. To apply the CRDs manually, run:
# helm install -n metallb metallb charts/metallb-${METALLB_VERSION:-0.13.9}.tgz

# helm install/upgrade
helm upgrade --install -n metallb -f clusters/${CLUSTER:-local}/values.yaml metallb .
```
