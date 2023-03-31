# Charts

## Publishing to Local ChartMuseum

### ChartMuseum

To run `ChartMuseum` locally see [documentation here](../k8s/infrastructure/chartmuseum/README.md).

``` sh
# port forward 8080 to chartmuseum service
( exec -a pf8080 kubectl port-forward svc/chartmuseum -n ${NAMESPACE:-chartmuseum} 8080:8080 > /dev/null ) &

# add local repo to helm
helm repo add local http://localhost:8080
```

### CM-Push

> NOTE: Install Helm plugin `cm-push` ([docs](https://github.com/chartmuseum/helm-push))

``` sh
# install plugin
helm plugin install https://github.com/chartmuseum/helm-push
```

``` sh
# push demo/v1
helm cm-push -u test -p test demo/v1 local

# open api
open http://localhost:8080/api/charts
```
