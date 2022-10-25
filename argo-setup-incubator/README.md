# Incubator cluster setup

_Desctiption about how, why etc. we made the setup of Argo-Workflows, -Events, -Rollout in our incubator-cluster._

## Deployment

* 1x controller
* app-of-apps opt-in (flag enableWorkflows cluster/project/namespace scoped) creation of
  * rolebinding engineer
  * serviceaccount + annotation
  * clusterworkflowtemplates can contain secret references
    * how to handle clusterworkflowtemplates?
    * e.g. MinIO/bucket per workflow/namespace or namespace?
  * e.g. https://argoproj.github.io/argo-workflows/argo-server-sso/#recommended-usage
  * tbd
    * mapping groups -> OIDC groups
    * read-only by default?
vs
* controller per namespace/project?

## Logs

* stdout: log to aggregator
  * retention defined by tenant
  * batch logs to stdout
  * controller logs (archive)
    * fixme: stdout or define target???
