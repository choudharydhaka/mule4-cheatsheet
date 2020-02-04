# mule4-cheatseat
This repository for users to help themselves for Mule4 projects.



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

## Dataweave 2.0


### Define New Class object 
```
payload.requestTimestamp as DateTime { class : "java.sql.Date"}
```




