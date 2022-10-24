# flux-de-travail

## batch-workflows

How can argo-workflow can be used to support batch-processing, triggered via arbitrary systems.
[See Detailed documentation](/batch-workflows/README.md)

## image-build

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

https://argoproj.github.io/argo-events/quick_start/



References:
* https://argoproj.github.io/argo-events/quick_start/
* https://github.com/alexmt/argo-combined-demo
* https://gist.github.com/vfarcic/28e2adb5946ca366d7845780608591d7
* https://github.com/vfarcic/argo-workflows-demo/blob/master/workflows/cd-mock.yaml
