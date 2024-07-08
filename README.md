<div align="center">
  <a href="#">
    <img src=".img/fun-with-kube.jpg" alt="Logo" width="319" height="207.5">
  </a>

  <h3 align="center">Fun with Kube</h3>

  <p align="center">
    Playing around with local Kube cluster!
  </p>
</div>

## Prerequisites

    - go
    - docker

## Run tooling suite

### Run local Kubernetes cluster

_Install [Kind](https://kind.sigs.k8s.io)_

`go install sigs.k8s.io/kind@v0.23.0`

_Run a local Kind cluster_

`kind create cluster --config .kind/kind-config.yaml`

_Install [Helm](https://helm.sh)_

`go install helm.sh/helm/v3/cmd/helm@v3.15.1`

_Install [Envoy Gateway](https://gateway.envoyproxy.io)_

`helm install envoy-gateway -f .envoy/values.yaml -n envoy-gateway-system .envoy --create-namespace`

_Install the demo application_

`kubectl apply -f ./demo.yaml`

_Test the configuration_

`export ENVOY_SERVICE=$(kubectl get svc -n envoy-gateway-system --selector=gateway.envoyproxy.io/owning-gateway-namespace=default,gateway.envoyproxy.io/owning-gateway-name=eg -o jsonpath='{.items[0].metadata.name}')`

`kubectl -n envoy-gateway-system port-forward service/${ENVOY_SERVICE} 8888:80 &`

`curl --header "Host: www.example.com" http://localhost:8888`

> To access your service without port-forward, you will need a load balancer solution like [MetalLB](https://github.com/metallb/metallb)
