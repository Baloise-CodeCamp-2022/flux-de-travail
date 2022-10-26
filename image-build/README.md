## Intro

* github (webhook)
* argo events (event-source, sensor)
* argo workflow (share state?)
* image build kaniko
* argo rollouts?

### Minikube

```
minikube start
minikube addons enable ingress
```

#### Argo Events

```
k apply -n argo -f https://raw.githubusercontent.com/argoproj/argo-workflows/stable/manifests/install.yaml
k apply -n argo-events -f https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/eventbus/native.yaml

```

https://argoproj.github.io/argo-events/quick_start/

#### Argo Workflows

```
k create namespace argo
k apply -n argo -f https://github.com/argoproj/argo-workflows/releases/download/v3.4.2/install.yaml
kubectl patch deployment \
  argo-server \
  --namespace argo \
  --type='json' \
  -p='[{"op": "replace", "path": "/spec/template/spec/containers/0/args", "value": [
  "server",
  "--auth-mode=server"
]}]'

```

Create docker secrets .dockerconfigjson with credentials to push to private registry


### Deploy image builder
#### Argo workflows part

```
k create -f examples/clusterworkflowtemplate-image-build.yaml
k -n flux02 create -f examples/workflow-busybox.yaml

k -n flux02 create -f examples/sa-kaniko.yaml
k create -f examples/scc-kaniko.yaml

k -n flux02 create -f examples/role-kaniko.yaml
k -n flux02 create -f examples/rolebinding-kaniko.yaml

```
https://argoproj.github.io/argo-workflows/


References:
* https://github.com/alexmt/argo-combined-demo
* https://gist.github.com/vfarcic/28e2adb5946ca366d7845780608591d7
* https://github.com/vfarcic/argo-workflows-demo/blob/master/workflows/cd-mock.yaml

#### Argo Events parts

* Create webhook in github
* Deploy argo-events and eventbus
* generate secret and provide it to argo-events

```
k -n flux02 create -f examples/route-event-github.yml
k -n flux02 create -f examples/eventsource.yaml
k -n flux02 create -f examples/sensor-webhook.yaml
k -n flux02 create -f examples/sensor-rbac.yaml
```

References:
* https://argoproj.github.io/argo-events/sensors/triggers/argo-workflow/