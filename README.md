# flux-de-travail

We try to learn [Argo Workflow](https://argoproj.github.io/argo-workflows/)
by working with the tutorials provided by [Getting started Guide](https://argoproj.github.io/argo-workflows/quick-start/).

![kickoff-flipchart](/assets/kickoff-flipchart.jpg)

After some local experience, we plan to setup the argo-suite to our [baloise.dev incubator-cluster](/argo-setup-incubator/README.md).

Then, we focus on two use-cases we expect to fit with argo ecosystem:

* Implementing batch with argo-workflow
* Implementing cloud native CI/CD automation with argo-events, argo-rollout etc.

## batch-workflows

How argo-workflow can be used to support batch-processing, triggered via arbitrary systems.
[See Detailed documentation](/batch-workflows/README.md)

## image-build

How can Argo Workflows can be used to support image builds on k8s, triggered via Argo Events.
[See Detailed documentation](/image-build/README.md)

## Minikube Quick Start

### Minikube

Minikube quick start if running local.

```bash
minikube start
minikube addons enable ingress
```

#### Argo Events

Install Argo Events

```bash
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo-workflows/stable/manifests/install.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/eventbus/native.yaml

```

reference: <https://argoproj.github.io/argo-events/quick_start/>

#### Argo Workflows

Install Argo Workflows

```bash
kubectl create namespace argo
kubectl apply -n argo -f https://github.com/argoproj/argo-workflows/releases/download/v3.4.2/install.yaml
kubectl patch deployment \
  argo-server \
  --namespace argo \
  --type='json' \
  -p='[{"op": "replace", "path": "/spec/template/spec/containers/0/args", "value": [
  "server",
  "--auth-mode=server"
]}]'

```
