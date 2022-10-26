# Batch Usecases für Argo-Workflow

GitOps Repos
* [Flux01: Namespace to publish Workflow templates](https://github.com/baloise-incubator/code-camp-apps/tree/master/flux01)

## Workflows

### Hello World
[Workflow-Template](https://github.com/baloise-incubator/code-camp-apps/blob/master/flux01/templates/wft-hello-world.yaml)

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

## Artifact Mounting
[Workflow-Template](https://github.com/baloise-incubator/code-camp-apps/blob/master/flux01/templates/wft-artifact-mounting.yaml)

Example how to submit a new workflow-run based on a preinstalled workflow-template, passing a parameter and using a preconfigured pvc for file-share purposes. The pvc is accessible via WebDAV Protocol ([see GitOps Spec](https://github.dev/baloise-incubator/code-camp-apps)):
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