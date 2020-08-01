 
# **Mulesoft VM Queue**

# Prerequisite
- A CloudHub Enterprise or Partner account (To run two cloudhub workers)
- Mule4 Docker VM
   - click [here](Mule4-docker)
        - please run ``` docker-compose up -d```
    - To send request please use hostname ``` http://w1.127.0.0.1.nip.io/```
    > Please add hosts file entry if necessary to resolve the hostname to 127.0.0.1

VM Project
- [Download](/.attachments/persitent-vm-mutilple-ch-worker/vm-muckaround.zip)
- Project has two type of VM configuration
    - Persistent (aw-received)
        - queues are slower but reliable
    - Transient (t-aw-received)
        - queues are faster than persistent queues, but they are not reliable in the case of a system crash.

![](/.attachments/persitent-vm-mutilple-ch-worker/porject0.PNG)

- We do have a listener configured ``` /receive``` to push the reqeuest to ``` aw-received``` queue
![](/.attachments/persitent-vm-mutilple-ch-worker/project1.PNG)
- We do have a listener configured ``` /t/receive``` to push the reqeuest to ``` t-aw-received``` queue
![](/.attachments/persitent-vm-mutilple-ch-worker/project1.PNG)


# Cloudhub
Cloudhub provide two type of configuration for VM queues
1. Non persistent queues (default)
    - Persistent
    - Transient
2. Persistent queues (Enabled using Runtime manager)
    - Persistent
    - Transient


## Cloudhub architecture for both VM and Object Store
![](/.attachments/persitent-vm-mutilple-ch-worker/ArchitectureDiagrams-INTSOL-cloudhub.png)

## How is the setup of Cloudhub?
- Two workers need to deployed with the same application
- Cloudhub provide funtionality to use Persistent storage which will allow to split the work load between workers, However you need to check the option from Runtime manager

## **1. Non persistent queues (default)** - Without Cloudhub Persistent option (Default)
- Cloudhub doesn't allow to share anything to other worker, they are seperate Amazon ec2 instances. 
- All the requests will be processed by individual nodes regardless queue types 

### **A. Persistent** queue configuration enabled
- Persistent queues work by serializing and storing the contents on the disk.

#### Worker0
![](/.attachments/persitent-vm-mutilple-ch-worker/non-persistent-cloudhub-and-persistentworker0.PNG)
### Worker1
![](/.attachments/persitent-vm-mutilple-ch-worker/non-persistent-cloudhub-and-persistentworker1.PNG) 

### **B. Transient** queue configuration enabled
-  Transient queues work by storing data in memory so they are faster than Persistent queues

#### Worker0 
![](/.attachments/persitent-vm-mutilple-ch-worker/non-persistent-cloudhub-and-transient-worker0.PNG)

#### Worker1
![](/.attachments/persitent-vm-mutilple-ch-worker/non-persistent-cloudhub-and-transient-worker1.PNG)

- ``` if you noticed, both workers are doing there processing individually, However Transient queue type stores the data in memory and Persistent queue stores the data on the disk.```

## **2. Persistent queues (Enabled using Runtime manager)** With Cloudhub persistent option
![](/.attachments/persitent-vm-mutilple-ch-worker/persistent-config-check.JPG)
Once we have configured this option Cloudhub will override any specific configuration which is coded into the Application.

Cloudhub allow to configure persistent queues in -
1. Single Application (Not demostrated here)
2. Clustering mode

## Customer-hosted cluster mode architecture for both VM and Object Store with Hazelcast
![](/.attachments/persitent-vm-mutilple-ch-worker/ArchitectureDiagrams-clustered-mule-runtime-customer-hosted_RTF.png)

### Things to note
- Retention time for messages in a persistent queue is up to 4 days
- There is no limit on message size or the number of messages in a persistent queue.
- The worker’s persistent queue is located in the same region as the worker
- guarantees delivery of your messages, even if one or more workers or data centers go down
- enable data-at-rest encryption
### Limitations
- do not guarantee one-time-only message delivery. Duplicate messages might be sent. If one-time-only message delivery is critical for your use case, do not enable persistent queues

### Use cases
- Persistent queues has a performance implication
    - Putting a small message (**50KB** or less) on a queue can take **10-20 milliseconds** (ms)
    - Taking the same message off a queue can take **70-100 milliseconds**.

###  References
- More details on Persistent-queues (https://docs.mulesoft.com/runtime-manager/cloudhub-fabric#persistent-queues)
- How to enable clustering (https://docs.mulesoft.com/runtime-manager/cloudhub-fabric#enable-clustering-features)

``` Regardless, Cloudhub persistent queue override the configuration hardcoded in the application code.```

### 1. Single Application (Not demostrated here)
### **2. Clustering mode**

#### **A. Persistent queue** configuration enabled
This option is again backed by the persistent queue, the messages are not stored on the disk.
##### Worker0 
![](/.attachments/persitent-vm-mutilple-ch-worker/persistent-ch-persisitent-w0.PNG)
##### Worker1
![](/.attachments/persitent-vm-mutilple-ch-worker/persistent-ch-persisitent-w1.PNG)

#### **B. Transient** queue configuration enabled
This option is again backed by the persistent queue, the messages are not stored on the disk.
##### Worker0
![](/.attachments/persitent-vm-mutilple-ch-worker/transient-ch-persisitent-w0.PNG)
##### Worker1
![](/.attachments/persitent-vm-mutilple-ch-worker/transient-ch-persisitent-w1.PNG) 

> You might have noticed that both workers are accepting the request by Cloudhub load balancer individually, however since peristent queue is enabled the load is distributed across the multiple workers.

# Customer hosted Cluster 
VM queue can be configured on -
1. Cluster mode
    - Persistent
    - Transient
2. Single mode
    - Persistent 
        > Persistent queues work by serializing and storing the contents on to disk.
    - Transient
        > In memory
> You can configure the maximum number of outstanding messages using the queue-profile element.
> Transaction are supported always.

## **1. Cluster mode**
- Both persistent and transient behave same because its backed by the memory grid in cluster mode
- Mule runtime engine (Mule) decides whether to process that message in the same origin node or to send it out to the cluster to be picked up and processed by another node.


![](/.attachments/persitent-vm-mutilple-ch-worker/onprim-clustered-runtime.PNG)
As shown in the above figure, we do have two Mule runtime running on docker machine using docker-compose in a clustered mode. 

``` Since clustered mode is backed by memory grid, both persistent and transient will behave same```
![](/.attachments/persitent-vm-mutilple-ch-worker/vm-persistent-queue-oncluster-processing.PNG)

![](/.attachments/persitent-vm-mutilple-ch-worker/vm-transient-queue-oncluster-processing.PNG)

## **2. Single mode(not demostrated)**


# References
- https://docs.mulesoft.com/vm-connector/2.0/
- https://docs.mulesoft.com/vm-connector/2.0/vm-reference
- https://docs.mulesoft.com/runtime-manager/managing-queues
- https://docs.mulesoft.com/runtime-manager/cloudhub-fabric
- https://docs.mulesoft.com/runtime-manager/cloudhub-networking-guide