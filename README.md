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

_Create namespace and install the CRD for emissary_

`kubectl create namespace emissary`

`kubectl apply -f .emissary/crds.yaml`

_Install the emissary helm chart_

`helm install emissary-ingress -f .emissary/helm/values.yaml -n emissary .emissary/helm`

_Install the demo application_

`kubectl apply -f ./demo.yaml`

_Test the configuration_

`curl http://192.168.228.2/backend/ -i`

> Emissary is using the "hostNetwork" in order to access our service. To avoid this setup, you will need a load balancer solution like [MetalLB](https://github.com/metallb/metallb)
