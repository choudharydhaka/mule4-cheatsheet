
# Object Store explained with CloudHub and Clustered (customer hosted) demostration

# Prerequisite
- Download Mule runtime 4.3 (https://www.mulesoft.com/lp/dl/mule-esb-enterprise)
- Trail account with Anypoint Platform (https://anypoint.mulesoft.com/login/signup)
- Register all 3 customer hosted nodes to the Anypoint Platform 
(https://docs.mulesoft.com/runtime-manager/servers-create)
- Create a cluster of all 3 registered Mule runtime
(https://docs.mulesoft.com/runtime-manager/cluster-about)


# Customer hosted cluster
This cluster is a group of 3 server nodes
- 2 nodes are running on same machine 
    - Demostrate one app with running on the same port 
        - Will only run on one server due to port conflict
-  Node3 is running on a Virutal machine 
    - Demostrate Cluster nodes on different machine 
         - Deploy the app across all the nodes

## Deploy an app with same port 8081 

We do have 3 nodes running in a cluster.

![](/.attachments/object-store/cluster-with-3nodes-2onsamesystem.PNG)
### Same server
- Node1 

![](/.attachments/object-store/mycluster-node1-laptop.PNG)

- Node2
![](/.attachments/object-store/mycluster-node2-laptop.PNG)

### Different server
- Node1 

![](/.attachments/object-store/mycluster-node1-dvm.PNG)

## Runtime states of deployment
1. Its clear that we can't deploy an APP on the same port in a cluster which are on same server.
2. There is no option for Object store 
![](/.attachments/object-store/mycluster-state-app-1.PNG)

# Server Setup for postman

```
var node1= "http://localhost:8081"
var node2= "http://localhost:8081"

// Node3 is under Virtual box VM which has mapping to the port 18181 -> 8081
var node3= "http://d-vm:18181"
var baseUrl= "api"
 
postman.setGlobalVariable('laptop-node1',node1);
postman.setGlobalVariable('laptop-node2',node2);
postman.setGlobalVariable('dvm-node1',node3);

postman.setGlobalVariable('baseUrl',baseUrl); 
postman.setGlobalVariable('ran', Math.floor(Math.random() * 5));
```

# Scenario

## **Use Default Object Store for customer hosted cluster**
```xml
<flow name="store" doc:id="7c9f55a6-1298-4351-bf94-d7b92392ddb8" >
		<http:listener doc:name="/store" doc:id="1dde1f7b-53d7-413c-9148-b319dd79c5f3" config-ref="HTTP_Listener_config" path="/store"/>
		<set-variable value="#[attributes.queryParams.key default 'defaultKey']" doc:name="key" doc:id="d30ef55d-ad14-42a7-a0f3-7a6e6fdd8f4f" variableName="key"/>
		<set-variable value="#[attributes.queryParams.value 
default 'defaultValue']" doc:name="value" doc:id="9df6d7b9-7b51-48d9-b9ad-3317a9e5c969" variableName="value" />
		<os:store doc:name="store the given key and value" doc:id="4973e250-1902-4161-ab0d-04d52db6efcd" key="#[vars.key]">
			<os:value ><![CDATA[#[vars.value]]]></os:value>
		</os:store>
		<set-payload value='#["Stored the following in the OS key="
++ vars.key ++ " and value=" ++ 
vars.value]' doc:name="give a message back to the user" doc:id="a17b0018-e1b9-4420-8a9a-6ec1b9c30d30" />
	</flow>
	<flow name="retrieve" doc:id="6450bff4-c38e-416a-94f6-c875d90b7bd0" >
		<http:listener doc:name="/retrieve" doc:id="aba939a8-bb1d-4ffb-b09f-4eb6c0b39b17" config-ref="HTTP_Listener_config" path="/retrieve"/>
		<set-variable value="#[attributes.queryParams.key default 'defaultKey']" doc:name="key" doc:id="282e0cde-ebb4-473d-b247-d7cdd2041dd3" variableName="key" />
		<os:retrieve doc:name="Retrieve" doc:id="fc53b2ef-328e-4ca7-bda4-665f55b4b645" key="#[vars.key]">
			<os:default-value ><![CDATA[null]]></os:default-value>
		</os:retrieve>
	</flow>
```

## 1. Store in Node1 and retrieve from Node3

![](/.attachments/object-store/mycluster-state-scenario-store-retrieve-from-any-node.PNG)

- Its clear that, you can access value from any server in a cluster 
- We used default Object Store capability

## 2. Store in Node1, Node1 Stopped and retrieve from Node3 (At least one node is up in Cluster)

|Server|Status|
|-|-|
|Node1| Down|
|Node2 | Down|
|Node3 |UP|

![](/.attachments/object-store/mycluster-state-scenario-node3-only-store-retrieve-from-any-node.PNG)

- Node3 Provided the response

![](/.attachments/object-store/mycluster-state-scenario-node3-only-store-retrieve-from-any-node-demo.PNG)


## 3. Store in Node1 , Node1 is down, Initially Node2 down but started back, Initially Node3 is alive however goes down after Node2 comes up and retrieve from Node2 (At least one node is up in Cluster)

1. Initial Setup, 
    - We are using scenario 2 to store the key

|Server|Status|
|-|-|
|Node1| Down|
|Node2 | Down|
|Node3 |UP|

2. Node2 Comes up

|Server|Status|
|-|-|
|Node1| Down|
|Node2 | UP|
|Node3 |UP|

![](/.attachments/object-store/mycluster-state-scenario-node2-only-store-retrieve-from-any-node-2.PNG)

3. Node3 goes down

|Server|Status|
|-|-|
|Node1| Down|
|Node2 | UP|
|Node3 |Down|

![](/.attachments/object-store/mycluster-state-scenario-node2-only-store-retrieve-from-any-node-3.PNG)

4. Retrieve key1

> We can still retrieve the key if at least one of the nodes is UP

![](/.attachments/object-store/mycluster-state-scenario-node2-only-store-retrieve-from-any-node-4.PNG)

5. all goes down

|Server|Status|
|-|-|
|Node1| Down|
|Node2 | Down|
|Node3 |Down|

![](/.attachments/object-store/mycluster-state-scenario-node2-only-store-retrieve-from-any-node-5.PNG)



6. Node1 Comes back
|Server|Status|
|-|-|
|Node1| UP|
|Node2 | Down|
|Node3 |Down|
![](/.attachments/object-store/mycluster-state-scenario-node2-only-store-retrieve-from-any-node-6.PNG)

7. Retrieve key1

    - We can't retrieve the key anymore

![](/.attachments/object-store/mycluster-state-scenario-node2-only-store-retrieve-from-any-node-7.PNG)



## Use Non persistent object store in customer hosted cluster


### 4. Store in Node1 and retrieve from Node3

![](/.attachments/object-store/mycluster-nonpersistent-state-scenario-store-retrieve-from-any-node.PNG

- Its clear that, you can access value from any server in a cluster 
- We used default Object Store capability

**NonPersistent_Object_store**

```xml
	<os:object-store name="NonPersistent_Object_store" doc:name="Object store" doc:id="8a802c7a-79c2-4f69-972f-21c4977b0bc9" persistent="false" />
<flow name="storeNonPersistent" doc:id="b8928e81-71a1-4db1-a408-2b7433805aa7" >
		<http:listener doc:name="/storeNonPersistent" doc:id="cfa1c8cc-e5cc-404b-8c60-655c291b2407" config-ref="HTTP_Listener_config" path="/storeNonPersistent"/>
		<set-variable value="#[attributes.queryParams.key default 'defaultKey']" doc:name="key" doc:id="2f226297-ccf3-4014-a46d-b6184de6cc03" variableName="key"/>
		<set-variable value="#[attributes.queryParams.value 
default 'defaultValue']" doc:name="value" doc:id="33f55a70-db82-4ed8-afb1-6987c5867ff0" variableName="value" />
		<os:store doc:name="store the given key and value" doc:id="33fe7c7b-87a9-4616-9c05-bebc9e20237b" key="#[vars.key]" objectStore="NonPersistent_Object_store">
			<os:value ><![CDATA[#[vars.value]]]></os:value>
		</os:store>
		<set-payload value='#["Stored the following in the OS key="
++ vars.key ++ " and value=" ++ 
vars.value]' doc:name="give a message back to the user" doc:id="7a2221db-e758-4c0e-906e-bbb142566ae3" />
	</flow>
	<flow name="retrieveNonPersistent" doc:id="a41326ae-7256-4f65-a46d-8bd0d70b5110" >
		<http:listener doc:name="/retrieveNonPersistent" doc:id="6ed0a17f-3392-468a-bfa6-c3187b886fd3" config-ref="HTTP_Listener_config" path="/retrieveNonPersistent"/>
		<set-variable value="#[attributes.queryParams.key default 'defaultKey']" doc:name="key" doc:id="119c0bc0-1607-4298-8604-2cb17cd3a706" variableName="key" />
		<os:retrieve doc:name="Retrieve" doc:id="58a25441-2270-43b4-b19c-cf959e35dfd8" key="#[vars.key]" objectStore="NonPersistent_Object_store">
			<os:default-value ><![CDATA[null]]></os:default-value>
		</os:retrieve>
	</flow>
```

### 5. Store in Node1 restarted and retrieve from Node3 (At least one node is up in Cluster)

|Server|Status|
|-|-|
|Node1| Down|
|Node2 | Down|
|Node3 |UP|


![](/.attachments/object-store/mycluster-nonpersistent-state-scenario-store-retrieve-from-any-node-1.PNG)



![](/.attachments/object-store/mycluster-nonpersistent-state-scenario-store-retrieve-from-any-node-2.PNG)

- Node3 Provided the response



## 6. Store in Node1 , Node1 is down, Initiall Node2 down but started back, Initially Node3 is alive however goes down after Node2 comes up and retrieve from Node2 (At least one node is up in Cluster)



1. Initial Setup

|Server|Status|
|-|-|
|Node1| Down|
|Node2 | Down|
|Node3 |UP|

2. Node2 Comes up

|Server|Status|
|-|-|
|Node1| Down|
|Node2 | UP|
|Node3 |UP|

![](/.attachments/object-store/mycluster-nonpersistent-state-scenario-store-retrieve-from-any-node-3.PNG)


3. Node3 goes down
|Server|Status|
|-|-|
|Node1| Down|
|Node2 | UP|
|Node3 |Down|

![](/.attachments/object-store/mycluster-nonpersistent-state-scenario-store-retrieve-from-any-node-4.PNG)


4. Retrieve key1

![](/.attachments/object-store/mycluster-nonpersistent-state-scenario-store-retrieve-from-any-node-5.PNG)


- We can still retrieve the key if at least one of the nodes is UP



5. all goes down
|Server|Status|
|-|-|
|Node1| Down|
|Node2 | Down|
|Node3 |Down|

![](/.attachments/object-store/mycluster-nonpersistent-state-scenario-store-retrieve-from-any-node-6.PNG)


6. Node1 Comes back

|Server|Status|
|-|-|
|Node1| UP|
|Node2 | Down|
|Node3 |Down|

![](/.attachments/object-store/mycluster-nonpersistent-state-scenario-store-retrieve-from-any-node-7.PNG)

7. Retrieve key1


![](/.attachments/object-store/mycluster-nonpersistent-state-scenario-store-retrieve-from-any-node-8.PNG)

- We can't retrieve the key anymore



## **Use Default Object Store Cloudhub**

- OS doesn't have any keys

![](/.attachments/object-store/os-ch-w1-data-1.PNG)

- Store key request

![](/.attachments/object-store/os-ch-w1-data-2.PNG)
- OS does persists the key
![](/.attachments/object-store/os-ch-w1-data-3.PNG)

- myKey retrieve Success
![](/.attachments/object-store/os-ch-w1-data-4.PNG)

- CloudHub worker restarted, and Success to retrieve myKey

![](/.attachments/object-store/os-ch-w1-data-5.PNG)

## Nonpersistent OS in CloudHub
- Request to store mykey1

![](/.attachments/object-store/os-ch-w1-data-6.PNG)

- OS doesn't show the value
![](/.attachments/object-store/os-ch-w1-data-7.PNG)

- Success Retrieve mykey1

![](/.attachments/object-store/os-ch-w1-data-8.PNG)

# Mindmap
Please feel free to comment if I'm missing/wrong something


![](/.attachments/mind-map/26e83130c8ad11eab83d0976b3e8e078.map.png)


# Resources
[Resources](/projects/object-store/resources.zip)