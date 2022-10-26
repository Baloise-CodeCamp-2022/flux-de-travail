# Build images on k8s with Kaniko and Argo

## Intro

> Do not use this setup in production

* Create GitHub webhook which triggers Argo Events
* Argo Events
  * github event-source
  * sensor creates workflow
* Argo Workflows
  * create image build workflow
    * run image build with [Kaniko](https://github.com/GoogleContainerTools/kaniko)
  * use a generic ClusterWorkflowTemplates

### Minikube install

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

### Argo workflows part

> Image Build will runAsUser 0. Creation of SCC on OpenShift might be necessary

Create flux02 namespace

```bash
kubectl create ns flux02
```

Create Kubernetes secret `credentials` in `flux02` namespace with key `.dockerconfigjson` containing credentials to push to private registry containing "username", "password" and "registry"

More information: <https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#registry-secret-existing-credentials>

```yaml
apiVersion: v1
data:
  .dockerconfigjson: XXXXXX
kind: Secret
metadata:
  name: credentials
  namespace: flux02
type: kubernetes.io/dockerconfigjson
```

Create ClusterWorkFlowTemplate

```bash
kubectl create -f examples/workflows-clusterworkflowtemplate-image-build.yml
```

Prepare namespace to run Kaniko image builds

```bash
kubectl create -f examples/workflows-scc-kaniko.yml
kubectl -n flux02 create -f examples/workflows-sa-kaniko.yml
```

Assign RBAC to ServiceAccount
reference: <https://argoproj.github.io/argo-workflows/workflow-rbac/>

```bash
kubectl -n flux02 create -f examples/workflows-role-kaniko.yml
kubectl -n flux02 create -f examples/workflows-rolebinding-kaniko.yml
```

Create workflow

```bash
kubectl -n flux02 create -f examples/workflows-busybox.yml
```

References:

* <https://argoproj.github.io/argo-workflows/>
* <https://github.com/alexmt/argo-combined-demo>
* <https://github.com/vfarcic/argo-workflows-demo/blob/master/workflows/cd-mock.yaml>

#### Argo Events part

* Create GitHub webhook which triggers Argo Events
<https://docs.github.com/en/developers/webhooks-and-events/webhooks/about-webhooks>

* Deploy argo-events and eventbus
* generate secret and provide it to argo-events

```bash
kubectl -n flux02 create -f examples/events-sensor-rbac.yaml
kubectl -n flux02 create -f examples/events-route-event-github.yml

kubectl -n flux02 create -f examples/events-eventsource.yml
kubectl -n flux02 create -f examples/events-sensor-webhook.yml
```

References:

* <https://argoproj.github.io/argo-events/sensors/triggers/argo-workflow/>
