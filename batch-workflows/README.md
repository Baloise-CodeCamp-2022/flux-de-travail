# Batch Usecases for Argo-Workflow

After some tweaks on our local machines with the [well documented getting-started tutorials](https://argoproj.github.io/argo-workflows/walk-through/), we try to setup argo-workflow in our incubator-cluster (see [/argo-setup-incubator/README.md](/argo-setup-incubator/README.md)).

We decided to deploy argo-workflow-controller central in it's own namespace and to publish project/team-specific workflow-templates in it's decentral managed namespaces.

To be able to run workflows in decentral namespaces like our `flux*`-namespaces, we grant the argo-workflow-controller with [this argo-cluster-rolebinding template](https://github.com/baloise-incubator/code-camp-apps/blob/master/flux01/templates/argo-cluster-rolebinding.yaml).

GitOps Repos
* [Flux01: Namespace to publish Workflow templates](https://github.com/baloise-incubator/code-camp-apps/tree/master/flux01)

## Workflows

### Conceptual view

![](/assets/batch-deploy-submit.drawio.svg)

### Hello World
[getting-started tutorial](https://argoproj.github.io/argo-workflows/walk-through/hello-world/)

[Workflow-Template](https://github.com/baloise-incubator/code-camp-apps/blob/master/flux01/templates/wft-hello-world.yaml)

Example how to submit a new workflow-run based on a preinstalled workflow-template, passing a parameter:
```bash
$ argo submit -n flux01 --from workflowtemplate/wft-hello-world -p message=CodeCamp --watch --log | tee response.txt

$ cat response.txt   
Name:                wft-hello-world-p76j7
Namespace:           flux01
ServiceAccount:      unset
Status:              Pending
Created:             Wed Oct 26 11:29:02 +0200 (now)
Progress:            
Parameters:          
  message:           CodeCamp
wft-hello-world-p76j7:  __________ 
wft-hello-world-p76j7: < CodeCamp >
wft-hello-world-p76j7:  ---------- 
wft-hello-world-p76j7:     \
wft-hello-world-p76j7:      \
wft-hello-world-p76j7:       \     
wft-hello-world-p76j7:                     ##        .            
wft-hello-world-p76j7:               ## ## ##       ==            
wft-hello-world-p76j7:            ## ## ## ##      ===            
wft-hello-world-p76j7:        /""""""""""""""""___/ ===        
wft-hello-world-p76j7:   ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~   
wft-hello-world-p76j7:        \______ o          __/            
wft-hello-world-p76j7:         \    \        __/             
wft-hello-world-p76j7:           \____\______/   
wft-hello-world-p76j7: time="2022-10-26T09:29:09.315Z" level=info msg="sub-process exited" argo=true error="<nil>"
Name:                wft-hello-world-p76j7
Namespace:           flux01
ServiceAccount:      unset
Status:              Succeeded
Conditions:          
 PodRunning          False
 Completed           True
Created:             Wed Oct 26 11:29:02 +0200 (27 seconds ago)
Started:             Wed Oct 26 11:29:02 +0200 (27 seconds ago)
Finished:            Wed Oct 26 11:29:22 +0200 (7 seconds ago)
Duration:            20 seconds
Progress:            1/1
ResourcesDuration:   6s*(100Mi memory),6s*(1 cpu)
Parameters:          
  message:           CodeCamp

STEP                      TEMPLATE           PODNAME                                             DURATION  MESSAGE
 ✔ wft-hello-world-p76j7  whalesay-template  wft-hello-world-p76j7-whalesay-template-1836767436  8s          

```

### Handling exit status
[getting-started tutorial](https://argoproj.github.io/argo-workflows/walk-through/exit-handlers/)

[Workflow-Template](https://github.com/baloise-incubator/code-camp-apps/blob/master/flux01/templates/wft-boohoo.yaml)

Example how to react on exit status e.g. to send an email for error handling:
```bash
$ argo submit -n flux01 --from workflowtemplate/exit-handlers --watch --log | tee response.txt

$ cat response.txt
Name:                exit-handlers-l9g4x
Namespace:           flux01
ServiceAccount:      default
Status:              Pending
Created:             Thu Oct 27 12:02:48 +0200 (now)
Progress:
exit-handlers-l9g4x: intentional failure
exit-handlers-l9g4x: time="2022-10-27T10:02:54.634Z" level=info msg="sub-process exited" argo=true error="<nil>"
exit-handlers-l9g4x: Error: exit status 1
exit-handlers-l9g4x-cry-245919737: boohoo!
exit-handlers-l9g4x-send-email-1914224258: send e-mail: exit-handlers-l9g4x Failed 10.861889. Failed steps [{"displayName":"exit-handlers-l9g4x","message":"Error (exit code 1)","templateName":"intentional-fail","phase":"Failed","podName":"exit-handlers-l9g4x","finishedAt":"2022-10-27T10:02:54Z"}]
exit-handlers-l9g4x-cry-245919737: time="2022-10-27T10:03:04.692Z" level=info msg="sub-process exited" argo=true error="<nil>"
exit-handlers-l9g4x-send-email-1914224258: time="2022-10-27T10:03:04.725Z" level=info msg="sub-process exited" argo=true error="<nil>"
Name:                exit-handlers-l9g4x
Namespace:           flux01
ServiceAccount:      default
Status:              Failed
Message:             Error (exit code 1)
Conditions:
 PodRunning          False
 Completed           True
Created:             Thu Oct 27 12:02:48 +0200 (21 seconds ago)
Started:             Thu Oct 27 12:02:48 +0200 (21 seconds ago)
Finished:            Thu Oct 27 12:03:08 +0200 (1 second ago)
Duration:            20 seconds
Progress:            2/3
ResourcesDuration:   12s*(1 cpu),12s*(100Mi memory)

STEP                           TEMPLATE          PODNAME                         DURATION  MESSAGE
 ✖ exit-handlers-l9g4x         intentional-fail  exit-handlers-l9g4x             6s        Error (exit code 1)

 ✔ exit-handlers-l9g4x.onExit  exit-handlee
 └─┬─○ celebrate               celebrate                                                   when 'Failed == Succeeded' evaluated false
   ├─✔ cry                     cry               exit-handlers-l9g4x-245919737   6s
   └─✔ notify                  send-email        exit-handlers-l9g4x-1914224258  6s
```

## Artifact sharing by volume mounting 
[getting-started tutorial](https://argoproj.github.io/argo-workflows/walk-through/volumes/)

[Workflow-Template](https://github.com/baloise-incubator/code-camp-apps/blob/master/flux01/templates/wft-artifact-mounting.yaml)

For simplicity, we do not implement the proposed s3 artifact mechanism. Instead, we connect a pvc to share file-content.
This example shows how to submit a new workflow-run based on a preinstalled workflow-template, passing a parameter and using a preconfigured pvc for file-share purposes. The pvc is accessible via WebDAV Protocol ([see GitOps Spec](https://github.dev/baloise-incubator/code-camp-apps)):

![](/assets/workflow.png)

```bash
# put inputfile to webdav folder:
$ curl -u <user>:<pw> -T <file> 'https://flux01webdav.apps.okd.baloise.dev'

# submit workflow
$ argo submit -n flux01 --from workflowtemplate/wft-artifact-mounting -p message=CodeCamp --watch --log | tee response.txt

# dump argo-cli output
$  cat response.txt
Name:                wft-artifact-mounting-tlnkn
Namespace:           flux01
ServiceAccount:      unset
Status:              Pending
Created:             Wed Oct 26 13:50:58 +0200 (now)
Progress:            
Parameters:          
  message:           CodeCamp
wft-artifact-mounting-tlnkn-whalesay-4066859623:  _______________ 
wft-artifact-mounting-tlnkn-whalesay-4066859623: < CodeCamp 2022 >
wft-artifact-mounting-tlnkn-whalesay-4066859623:  --------------- 
wft-artifact-mounting-tlnkn-whalesay-4066859623:     \
wft-artifact-mounting-tlnkn-whalesay-4066859623:      \
wft-artifact-mounting-tlnkn-whalesay-4066859623:       \     
wft-artifact-mounting-tlnkn-whalesay-4066859623:                     ##        .            
wft-artifact-mounting-tlnkn-whalesay-4066859623:               ## ## ##       ==            
wft-artifact-mounting-tlnkn-whalesay-4066859623:            ## ## ## ##      ===            
wft-artifact-mounting-tlnkn-whalesay-4066859623:        /""""""""""""""""___/ ===        
wft-artifact-mounting-tlnkn-whalesay-4066859623:   ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~   
wft-artifact-mounting-tlnkn-whalesay-4066859623:        \______ o          __/            
wft-artifact-mounting-tlnkn-whalesay-4066859623:         \    \        __/             
wft-artifact-mounting-tlnkn-whalesay-4066859623:           \____\______/   
wft-artifact-mounting-tlnkn-whalesay-4066859623: time="2022-10-26T11:51:12.149Z" level=info msg="sub-process exited" argo=true error="<nil>"
wft-artifact-mounting-tlnkn-print-message-118296141:  _______________ 
wft-artifact-mounting-tlnkn-print-message-118296141: < CodeCamp 2022 >
wft-artifact-mounting-tlnkn-print-message-118296141:  --------------- 
wft-artifact-mounting-tlnkn-print-message-118296141:     \
wft-artifact-mounting-tlnkn-print-message-118296141:      \
wft-artifact-mounting-tlnkn-print-message-118296141:       \     
wft-artifact-mounting-tlnkn-print-message-118296141:                     ##        .            
wft-artifact-mounting-tlnkn-print-message-118296141:               ## ## ##       ==            
wft-artifact-mounting-tlnkn-print-message-118296141:            ## ## ## ##      ===            
wft-artifact-mounting-tlnkn-print-message-118296141:        /""""""""""""""""___/ ===        
wft-artifact-mounting-tlnkn-print-message-118296141:   ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~   
wft-artifact-mounting-tlnkn-print-message-118296141:        \______ o          __/            
wft-artifact-mounting-tlnkn-print-message-118296141:         \    \        __/             
wft-artifact-mounting-tlnkn-print-message-118296141:           \____\______/   
wft-artifact-mounting-tlnkn-print-message-118296141: time="2022-10-26T11:51:29.955Z" level=info msg="sub-process exited" argo=true error="<nil>"
Name:                wft-artifact-mounting-tlnkn
Namespace:           flux01
ServiceAccount:      unset
Status:              Succeeded
Conditions:          
 PodRunning          False
 Completed           True
Created:             Wed Oct 26 13:50:58 +0200 (45 seconds ago)
Started:             Wed Oct 26 13:50:58 +0200 (45 seconds ago)
Finished:            Wed Oct 26 13:51:42 +0200 (1 second ago)
Duration:            44 seconds
Progress:            2/2
ResourcesDuration:   10s*(1 cpu),10s*(100Mi memory)
Parameters:          
  message:           CodeCamp

STEP                            TEMPLATE           PODNAME                                              DURATION  MESSAGE
 ✔ wft-artifact-mounting-tlnkn  artifact-mounting                                                                   
 ├───✔ generate-artifact        whalesay           wft-artifact-mounting-tlnkn-whalesay-30922292        15s         
 └───✔ consume-artifact         print-message      wft-artifact-mounting-tlnkn-print-message-877254023  9s   
```
