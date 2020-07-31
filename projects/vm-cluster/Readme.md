![](/.attachments/persitent-vm-mutilple-ch-worker


# Prerequisite
- Subscription Account (To run two cloudhub workers)
- Mule4 Docker VM
   - click [here](Mule4-docker)
        - please run ``` docker-compose up -d```
    - To send request please use hostname ``` http://w1.127.0.0.1.nip.io/```
    > Please add hosts file entry if necessary to resolve the hostname to 127.0.0.1

VM Project
- [Download](/.attachments/persitent-vm-mutilple-ch-worker/vm-muckaround.zip)
- Project has two type of VM configuration
    - Persistent (aw-received)
    >queues are slower but reliable
    - Transient (t-aw-received)
    >queues are faster than persistent queues, but they are not reliable in the case of a system crash.

![](/.attachments/persitent-vm-mutilple-ch-worker/porject0.PNG)

- We do have a listener configured ``` /receive``` to push the reqeuest to ``` aw-received``` queue
![](/.attachments/persitent-vm-mutilple-ch-worker/project1.PNG)
- We do have a listener configured ``` /t/receive``` to push the reqeuest to ``` t-aw-received``` queue
![](/.attachments/persitent-vm-mutilple-ch-worker/project1.PNG)


# Cloudhub
- Two workers need to deployed with  the same application
- Cloudhub provide funtionality to use Persistent storage which will allow to split the work load between workers

## Without Cloudhub persistent option

### **Persitent queue configuration enabled**
#### Worker0
![](/.attachments/persitent-vm-mutilple-ch-worker/non-persistent-cloudhub-and-persistentworker0.PNG)
### Worker1
![](/.attachments/persitent-vm-mutilple-ch-worker/non-persistent-cloudhub-and-persistentworker1.PNG) 

### **Transient queue configuration enabled**
 
#### Worker0 
![](/.attachments/persitent-vm-mutilple-ch-worker/non-persistent-cloudhub-and-transient-worker0.PNG)

#### Worker1
![](/.attachments/persitent-vm-mutilple-ch-worker/non-persistent-cloudhub-and-transient-worker1.PNG)

## With Cloudhub persistent option
![](/.attachments/persitent-vm-mutilple-ch-worker/persistent-config-check.JPG)


### **Persistent queue configuration enabled**
 
#### Worker0 
![](/.attachments/persitent-vm-mutilple-ch-worker/persistent-ch-persisitent-w0.PNG)
#### Worker1
![](/.attachments/persitent-vm-mutilple-ch-worker/persistent-ch-persisitent-w1.PNG)
### **Transient queue configuration enabled**
#### Worker0
![](/.attachments/persitent-vm-mutilple-ch-worker/transient-ch-persisitent-w0.PNG)
#### Worker1
![](/.attachments/persitent-vm-mutilple-ch-worker/transient-ch-persisitent-w1.PNG) 
#### Worker1



# Customer hosted Cluster 
- Both persistent and transient behave same because its backed by on memory grid

![](/.attachments/persitent-vm-mutilple-ch-worker/vm-persistent-queue-oncluster-processing.PNG)

![](/.attachments/persitent-vm-mutilple-ch-worker/vm-transient-queue-oncluster-processing.PNG)


