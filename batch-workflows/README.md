# Batch Usecases für Argo-Workflow

GitOps Repos
* [Flux01: Namespace to publish Workflow templates](https://github.com/baloise-incubator/code-camp-apps/tree/master/flux01)

## Workflows

### Hello World
[Hello World](https://github.com/baloise-incubator/code-camp-apps/blob/master/flux01/templates/wft-hello-world.yaml)

Example how to submit a new workflow-run based on a preinstalled workflow-template, passing a parameter:
```bash
$ argo submit -n flux01 --from workflowtemplate/wft-hello-world -p message=CodeCamp --watch --log | tee response.txt

$  cat response.txt                                                    
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
