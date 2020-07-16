- [Mule4 cheatsheet](#mule4-cheatsheet)
- [Mule projects](#mule-projects)
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


```
<set-variable value='#["(objectGUID=" ++ (java!operator::Convertor::getSearchFilterFormattedGUID(vars.searchGuid)) ++ ")"]' doc:name="Set Guid Criteria" doc:id="5c1a95c5-fe2d-4a6d-b9c6-62bfb77cbcc0" variableName="criteria"/>
		 ("guid": java!operator::Convertor::convertToDashedString(payload.objectGUID )) if (payload.objectGUID !=null ),
write( java!operator::Convertor::getPass('&quot;' ++ vars.originalRequestData.password ++ '&quot;'),&quot;application/java&quot;)

	 ("guid": java!operator::Convertor::convertToDashedString(vars.adSearchResponse.objectGUID )) if (vars.adSearchResponse.objectGUID !=null ),
```

```java
package operator;

import java.util.UUID;

/**
 * Convertor class to convert objectGUID byte array to a human readable string.
 * code taken from Mulesoft Article : https://help.mulesoft.com/s/article/Ldap-ObjectGUID-field-retrieval
 * 
 * @author HaryoT
 *
 */
 
public class Convertor {
	
	/**
	 * Convert to human readable string.
	 * @param objectGUID byte array returned by AD 
	 * @return example: 91de7a48-7785-4ede-8d12-873e9c09deb0
	 */
	public static String convertToDashedString(byte[] objectGUID) {
		if(objectGUID == null || objectGUID.length==0) return "false";
		UUID uuid = bytesToUUID(objectGUID);
		return uuid.toString();
	}
	
	/**
     * Converts a byte array into an {@link UUID} object.
     * <p/>
     * Microsoft stores GUIDs in a binary format that differs from the RFC standard of UUIDs (RFC #4122). (See details
     * at http://en.wikipedia.org/wiki/Globally_unique_identifier) This function takes a byte array read from Active
     * Directory and correctly decodes it as a {@link UUID} object.
     *
     * @param bytes Byte array received as en entry attribute from Active Directory.
     * @return {@link UUID} object created from the byte array, or null in case the passed array is not exactly 16 bytes long.
     */
	private static UUID bytesToUUID(byte[] bytes) {
        if (bytes != null && bytes.length == 16) {
            long msb = bytes[3] & 0xFF;
            msb = msb << 8 | (bytes[2] & 0xFF);
            msb = msb << 8 | (bytes[1] & 0xFF);
            msb = msb << 8 | (bytes[0] & 0xFF);

            msb = msb << 8 | (bytes[5] & 0xFF);
            msb = msb << 8 | (bytes[4] & 0xFF);

            msb = msb << 8 | (bytes[7] & 0xFF);
            msb = msb << 8 | (bytes[6] & 0xFF);

            long lsb = bytes[8] & 0xFF;
            lsb = lsb << 8 | (bytes[9] & 0xFF);
            lsb = lsb << 8 | (bytes[10] & 0xFF);
            lsb = lsb << 8 | (bytes[11] & 0xFF);
            lsb = lsb << 8 | (bytes[12] & 0xFF);
            lsb = lsb << 8 | (bytes[13] & 0xFF);
            lsb = lsb << 8 | (bytes[14] & 0xFF);
            lsb = lsb << 8 | (bytes[15] & 0xFF);

            return new UUID(msb, lsb);
        }
        return null;
    }
}

```

