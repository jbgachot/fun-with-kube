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

_Install the Emissary components and CRDs_

`kubectl apply -f .emissary/crds.yaml` 

> Here you should wait 1 or 2 minutes ⏳

_Install the emissary helm chart_

`helm install emissary-ingress -f .emissary/helm/values.yaml -n emissary-system .emissary/helm`

_Generate a self-signed certificat and create the secrets_

`openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -subj '/CN=ambassador-cert' -nodes`

`kubectl create secret tls tls-cert --cert=cert.pem --key=key.pem`

_Apply the Host configuration_

`kubectl apply -f .emissary/wildcard-host.yaml`

_Install the demo application_

`kubectl apply -f ./infra/demo.yaml`

_Test the configuration_

> <WORKER_IP> being your kind-worker node ip

`export WORKER_IP=$(docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' kind-worker)`

`curl https://$WORKER_IP/demo/ -ik`

> Emissary is using the "hostNetwork" in order to access our service. To avoid this setup, you will need a load balancer solution like [MetalLB](https://github.com/metallb/metallb)

_Remove demo application_

`kubectl delete -f ./infra/demo.yaml`

_Install [ArgoCD](https://argoproj.github.io/cd)_

`kubectl create namespace argocd`

`kubectl apply -n argocd -f .argocd/install.yaml`

`kubectl apply -n argocd -f .argocd/ingress.yaml`

_Retrieve initial admin password_

`kubectl -n argocd get secret argocd-initial-admin-secret --template={{.data.password}} | base64 -D; echo`

> Browse the UI at [https://<WORKER_IP>/argocd](https://<WORKER_IP>/argocd) and connect with the admin user and the password you just retrieved

*optional*

_Install new relic_

`helm repo add newrelic https://helm-charts.newrelic.com`

`helm repo update`

`kubectl create namespace newrelic`

`helm install newrelic-bundle -f .newrelic/helm/values.yaml -n newrelic .newrelic/helm --set global.licenseKey=xxx`

