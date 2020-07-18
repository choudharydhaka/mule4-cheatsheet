- [Mule4 cheatsheet](#mule4-cheatsheet)
- [Mule projects](#mule-projects)
  - [Object Store](./projects/object-store/Object-Store.md)
  - [Dataweave-2.0 Examples](./projects/Dataweave-2.0/)
- [Cloudhub](#Cloudhub)
- [TLS with Mule4](#tls-with-mule4)
  * [Keytool](#keytool)
    + [Generate mule.jks and mule-trust.jks](#generate-mulejks-and-mule-trustjks)
    + [Restrict server-crt to dhaks.localhost.nip.io dns name and ip address 127.0.0.1](#restrict-server-crt-to-dhakslocalhostnipio-dns-name-and-ip-address-127001)
  * [Curl](#curl)
  * [Debug java ssl](#debug-java-ssl)
  * [sed, a stream editor](#sed--a-stream-editor)
  * [Example1:  Tls v1.2 enabled in Mule4](#example1---tls-v12-enabled-in-mule4)
  * [Example2: Add logger in Mule4 code](#example2--add-logger-in-mule4-code)
    + [Before: File health-implementation.xml](#before--file-health-implementationxml)
    + [Run below command:](#run-below-command-)
    + [After: File health-implementation.xml](#after--file-health-implementationxml)
  * [Example3: Enable debug for database extension](#example3--enable-debug-for-database-extension)
  * [Custom Security Policy](#custom-security-policy)
    + [Create a project using Maven archtype](#create-a-project-using-maven-archtype)
  * [Dataweave 2.0](#dataweave-20)
    + [Define New Class object](#define-new-class-object)

# Mule4 cheatsheet
This repository for users to help themselves for Mule4 projects.

# Mule projects
Here you can find the projects which has an example API running with concepts in details.

|Project|Description|Links|
|-|-|-|
|Design API with Workbench| Workbench an Atom editor plugin allows us to design RAML specification |Please click [here](https://github.com/choudharydhaka/mule4-cheatsheet/tree/master/projects/health-check/demo-api) for a detailed step by step guidelines to design, develop and run an API.|
|How to use secure properties| Mule allows to encrypt any secrets|Please click [here](https://github.com/choudharydhaka/mule4-cheatsheet/tree/master/projects/secure-property)

# Cloudhub
## How to find IP address of a cloudhub worker
Cloudhub provide logging facility, where it will log an entry for the ipaddress.
Please search the following text 
``` Your application has started successfully```

![](.attachments/cb-find-worker-ip-address.PNG)

# TLS with Mule4
TLS allows to secure API's with Mule4. 
## Keytool
Keytool is a java based tool which help to generate, list, import, export etc. TLS certificates to/from java keystore. Mule support both ``` JKS``` and ``` PKCS12``` keystores,however here we gonna generate JKS keystore & trust store using keytool.
### Generate mule.jks and mule-trust.jks
```sh
## move to tmp directory
cd /tmp/keystore/jks/
## Generate mule.jks keystore with self signed certificate using alias server-crt
keytool -genkeypair -dname "CN=*.localhost, OU=Integartion, O=dhaka, L=New Delhi, ST=New Delhi, C=IN"  -keypass password  -storepass changeit -validity 9999 -keystore mule.jks -alias server-crt 

## Expoert public certificate of alias server-crt
keytool -export -keystore mule.jks -storepass changeit -alias server-crt -file server-pub.cer
## Create trust mule keystore
keytool -import -file server-pub.cer  -alias server-crt -keystore mule-trust.jks -storepass changeit -noprompt
```

### Restrict server-crt to dhaks.localhost.nip.io dns name and ip address 127.0.0.1
```
keytool -genkeypair -dname "CN=*.localhost, OU=Integartion, O=dhaka, L=New Delhi, ST=New Delhi, C=IN"  -keypass password  -storepass changeit -validity 9999 -keystore mule.jks -alias server-crt -ext SAN=DNS:dhaks.localhost.nip.io,IP:127.0.0.1

## TLS 
```yaml


 local:
    host: "localhost"
    port: "8081"
    basepath: "demo-api"    
    timezone: "NZ"
    tls-port: "8443"
    tls-keystore: "/tmp/keystore/jks/mule.jks"
    tls-keystore-password: "changeit"
    tls-server-cert: "server-crt"
    tls-sever-cert-password: "changeit"
    tls-trust-keystore: "/tmp/keystore/jks/mule-trust.jks"
    tls-trust-keystore-password: "changeit" 
    
 ```



## Curl

This will help team to copy and paste headers to use curl.
```sh
curl -v
  -H 'x-api-key: bba3ea5180432a871837726488934592' \
  -H 'x-api-secret: 1837726488934592bba3ea5180432a87' \
   https://localhost:8082/demo-api/v1/health
```  

## Debug java ssl
Sometimes it's helpful to check ssl communication to debug the issues, Mule4 allows to pass on properties to jvm using ``` -M-D<PropertyName>=<PropertyValue>```.
```
-M-Djavax.net.debug=ssl
```

## sed, a stream editor
Sed is a linux command to help to subtitute values of any file. I'm using to comment out enableProtocols to enforce TLSv1.2 in tls-default.conf file in Mule4 runtime. please click [here](https://www.gnu.org/software/sed/manual/sed.html) for official documents.

```
sed SCRIPT INPUTFILE
sed 's/hello/world/' input.txt > output.txt
```
For example, to replace all occurrences of ‘hello’ to ‘world’ in the file input.txt:

## Example1:  Tls v1.2 enabled in Mule4
```sh
sed -i 's/enabledProtocols/#enabledProtocols/' tls-default.conf |grep enabledProtocols
echo "########################## Change TASK 00001 enforce TLSv1.2 TLS support #######################################" >> tls-default.conf && \
echo "enabledProtocols=TLSv1.2" >> tls-default.conf
```
## Example2: Add logger in Mule4 code
We will add custom logger to the code in the API implementation file using sed tool.

### Before: File health-implementation.xml
```xml
<flow-ref doc:name="Call Downstream Inbound Success Log subFlow" name="downstream-inbound-success-log-sub-flow" doc:id="d6bf12b6-a4e2-4e77-9694-61e577123456" />

					<choice doc:name="Choice" doc:id="123456-a4e2-4e77-9694-61e577123456" >

```
We are using **doc:id** "d6bf12b6-a4e2-4e77-9694-61e577123456" to uniquily identify the element in the file. Below command allow to add ```<logger level="INFO" doc:name="DHAKA" doc:id="DHAKA" message="#[output application/json --- SUCCESS: payload]" />``` to the file ```health-implementation.xml``` to print payload in json format after calling **downstream-inbound-success-log-sub-flow** subflow.

### Run below command:

```sh 
# $MULE_HOME an environement variable pointing to Mule4 runtime home.
cd $MULE_HOME/apps/demo-api/

sed 's/d6bf12b6-a4e2-4e77-9694-61e577123456\" >/DHAKA1\" > <logger level=\"INFO\" doc:name=\"DHAKA\" doc:id=\"DHAKA\" message=\"#\[output application/json \-\-\- SUCCESS: payload\]\"\/>/ health-implementation.xml
```
### After: File health-implementation.xml

```xml
<flow-ref doc:name="Call Downstream Inbound Success Log subFlow" name="downstream-inbound-success-log-sub-flow" doc:id="DHAKA1" />
        <logger level="INFO" doc:name="DHAKA" doc:id="DHAKA" message="#[output application/json --- SUCCESS: payload]" />
					<choice doc:name="Choice" doc:id="431c44ef-36c5-47b0-956d-810abccd8ad5" >

```



## Example3: Enable debug for database extension
```sh
# $MULE_HOME an environement variable pointing to Mule4 runtime home.
cd $MULE_HOME/apps/demo-api/

sed 's/<\/Loggers/<AsyncLogger name=\"org.mule.extension.db\" level=\"DEBUG\" \/> <\/Loggers/' log4j2.xml

```

## Custom Security Policy
Mule4 allows to apply security policies using Anypoint API Manager, Mule4 runtime has an embeded API gateway component to intercept the requests/responses and enforces the policies on them. Sometimes we need to create some custom policies to meet an out of the box business requirement. 

### Create a project using Maven archtype
MuleSoft provides an Maven compatible to generate template artifects to starts with. You must have some of the key values before hands to pass on to ```mvn`` command as arguments.


```sh
mvn -Parchetype-repository archetype:generate \
-DarchetypeGroupId=org.mule.tools \
-DarchetypeArtifactId=api-gateway-custom-policy-archetype \
-DarchetypeVersion=1.1.0 \
-DgroupId=${orgId} \
-DartifactId=${policyName} \
-Dversion=1.0.0-SNAPSHOT \
-Dpackage=mule-policy

```
If you might have noticed couple of variables( ```$orgId``` and ```${policyName}``` ) you will need to provide when you will issue the command.

|Argument  | Description|
|-----|---------|
|orgId| This is an organization Id provided by MuleSoft against your created organisation, Please check Access Management -> Organization -> Click on the org Name -> Organization Id, example: 123456789-2345-6789-12-3456789-887777 |
|policyName| Name of the policy, exmple gw-http-custom-policy|


```
mvn -Parchetype-repository archetype:generate -DarchetypeGroupId=org.mule.tools -DarchetypeArtifactId=api-gateway-custom-policy-archetype -DarchetypeVersion=1.1.0 -DgroupId=123456789-2345-6789-12-3456789-887777 -DartifactId=gw-http-custom-policy -Dversion=1.0.0 -Dpackage=mule-policy
```

![](/.attachments/aam-orgid.PNG)

You can find above example [here](https://docs.mulesoft.com/api-manager/2.x/custom-policy-getting-started) on MuleSoft website. 



once the API is up and running.

 You have applied the policy

## Dataweave 2.0


### Define New Class object 
```
payload.requestTimestamp as DateTime { class : "java.sql.Date"}
```




