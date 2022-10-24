## Intro

* github (webhook)
* argo events (event-source, sensor)
* argo workflow (share state?)
* image build kaniko
* argo rollouts?

### Deploy

```
minikube start
minikube addons enable ingress
```

#### Argo Events

```
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo-workflows/stable/manifests/install.yaml
kubectl apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/eventbus/native.yaml

```

https://argoproj.github.io/argo-events/quick_start/

#### Argo Workflows

```
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

https://argoproj.github.io/argo-workflows/


References:
* https://github.com/alexmt/argo-combined-demo
* https://gist.github.com/vfarcic/28e2adb5946ca366d7845780608591d7
* https://github.com/vfarcic/argo-workflows-demo/blob/master/workflows/cd-mock.yaml
