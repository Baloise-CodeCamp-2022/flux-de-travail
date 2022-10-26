## Intro

* github (webhook)
* argo events
  * github event-source
  * sensor creates workflow
* argo workflows
  * image build workflow
    * image build with kaniko
  * generic clusterworkflow template

### Minikube install

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

### Deploy image builder
#### Argo workflows part

* Create docker secrets .dockerconfigjson with credentials to push to private registry

```
k create -f examples/workflows-clusterworkflowtemplate-image-build.yml
k -n flux02 create -f examples/workflows-busybox.yml

k -n flux02 create -f examples/workflows-sa-kaniko.yml
k create -f examples/workflows-scc-kaniko.yml

k -n flux02 create -f examples/workflows-role-kaniko.yml
k -n flux02 create -f examples/workflows-rolebinding-kaniko.yml

```
https://argoproj.github.io/argo-workflows/


References:
* https://github.com/alexmt/argo-combined-demo
* https://gist.github.com/vfarcic/28e2adb5946ca366d7845780608591d7
* https://github.com/vfarcic/argo-workflows-demo/blob/master/workflows/cd-mock.yaml

#### Argo Events part

* Create webhook in github
* Deploy argo-events and eventbus
* generate secret and provide it to argo-events

```
k -n flux02 create -f examples/events-sensor-rbac.yaml
k -n flux02 create -f examples/events-route-event-github.yml

k -n flux02 create -f examples/events-eventsource.yml
k -n flux02 create -f examples/events-sensor-webhook.yml
```

References:
* https://argoproj.github.io/argo-events/sensors/triggers/argo-workflow/