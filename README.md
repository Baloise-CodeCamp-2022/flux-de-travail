# flux-de-travail

We try to learn [Argo Workflow](https://argoproj.github.io/argo-workflows/)
by working with the tutorials provided by [Getting started Guide](https://argoproj.github.io/argo-workflows/quick-start/).

![](/assets/kickoff-flipchart.jpg)

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
