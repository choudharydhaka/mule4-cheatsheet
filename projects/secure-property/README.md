- [Introduction](#introduction)
- [Prerequisite](#prerequisite)
- [Requirement](#requirement)
- [How to encrypt a string in Mule4 using Secure property tool](#how-to-encrypt-a-string-in-mule4-using-secure-property-tool)
  * [Download Secure property tools](#download-secure-property-tools)
  * [Encrypt a string text value](#encrypt-a-string-text-value)
      - [Output](#output)
- [How to use secure module in the project](#how-to-use-secure-module-in-the-project)
  * [install Extension Module in Studio](#install-extension-module-in-studio)
  * [Use module in global configuration](#use-module-in-global-configuration)
  * [Supply encrypted text values in configs-LOCAL.yaml file](#supply-encrypted-text-values-in-configs-localyaml-file)
    + [**configs-LOCAL.yaml**](#--configs-localyaml--)
    + [**get-health-implementation.xml**](#--get-health-implementationxml--)
- [API Development](#api-development)
  * [Run the Secure Property API](#run-the-secure-property-api)

# Introduction

This project repository defines basic steps to create a secure property value using Secure property tools jar file provided by MuleSoft.

# Prerequisite
Below mentioned tools must be installed in your development machine: 
- [Secure Property tool](https://docs.mulesoft.com/downloads/mule-runtime/4.2/secure-properties-tool.jar)
- [OpenJDK](https://adoptopenjdk.net/)
- [Anypoint Studio](https://www.mulesoft.com/lp/dl/studio) 
- [Atom](https://atom.io/)
- [API workbench](http://apiworkbench.com/)

You should have basic understanding of Mule4 developement or an significant background in integration space to starts with.

# Requirement
We need an API ```secure-property-api``` with ```/health``` resource to demostrate our working API development.


# How to encrypt a string in Mule4 using Secure property tool
## Download Secure property tools
We will need to download secure property jar file from MuleSoft website, please click [here](https://docs.mulesoft.com/downloads/mule-runtime/4.2/secure-properties-tool.jar) for Mule 4.2 to downlaod the file. 

## Encrypt a string text value
To encrypt a string you need to specify below mentioned parameters:
```
java -jar secure-properties-tool.jar \
<method> \
<operation> \
<algorithm> \
<mode> \
<key> \
<value>
```
|Item|Description|Example|Other|
|--|-----------------------------|-|-|
|method|  string or file| ```string```|
|Operation| encrypt or decrypt| ```encrypt```|Decryption will be done by API itself|
|algorithm|MuleSoft provide a set of supported algorithms and respective modes to encrypt the file, you can refer the [page](https://docs.mulesoft.com/mule-runtime/4.2/secure-configuration-properties#supported_algorithms) | ```AES```|
|mode|MuleSoft provide a set of supported algorithms and respective modes to encrypt the file, you can refer the [page](https://docs.mulesoft.com/mule-runtime/4.2/secure-configuration-properties#supported_modes) | ```CBC```|
|key| any key based on the algorithm and it's more|```abcdefghm123456789nMdGi74JfcPMlx```| We need to pass this key to Mule runtime, <br> standlone: -D<key>=<masterKey> ```-DAPI_MASTER_KEY=abcdefghm123456789nMdGi74JfcPMlx``` <br>
Cloudhub: please click [here](https://docs.mulesoft.com/runtime-manager/secure-application-properties) 
|value|The secret we want to encrypt|```this is encrypted value```| 

```
java -jar secure-properties-tool.jar \
string \ 
encrypt \ 
AES \ 
CBC \ 
"abcdefghm123456789nMdGi74JfcPMlx" 
"this is encrypted value"
```
#### Output
```
9T5f/4hVrfREVPUjGg+ZK6lt/GCx1KcJDjjXUf8+w7A=
```

We will use this key to pass in our configuration file and then use the transform scope to read it.

# How to use secure module in the project
## install Extension Module in Studio

1. Open your Mule project in Anypoint Studio.
![](/.attachments\secure-prop\project-open.PNG)

2. Go to the Mule Palette section and click Search in Exchange.
![](/.attachments\secure-prop\mule-pallet.PNG)

4. In the Add Module to Project window, search for Mule Secure Configuration Property Extension in the Type a search term to look up in Exchange box.
5. Select the module name in the list.
6. Click Add and then Finish.

![](/.attachments\secure-prop\installed.PNG)

## Use module in global configuration

1. Open your global.xml in the project.
2. Go to Global Element section and click on create
![](/.attachments\secure-prop\global0.PNG)

3. Now Provide the below mentioned configuration  and click ok then save the file.

|Item|Description|Example|Other|
|--|-----------------------------|-|-|
|File|Configuration file which has encrypted values| ```configs-LOCAL.yaml```
|Key|Master secret key reference to use to encypt the text string| ``` ${API_MASTER_KEY}```|This is an envrionment valiable (command line argument) refernce which we will pass to the Mule runtime in order to deploy the API|
|Algorithm| | AES|
|Mode|| CBC|

![](/.attachments\secure-prop\global2.PNG)

## Supply encrypted text values in configs-LOCAL.yaml file
1. Encrypt a text value 
> Please refer the section [How to encrypt a string in Mule4 using Secure property tool](./#how-to-encrypt-a-string-in-mule4-using-secure-property-tool)

2. Add encrypted values into the configs-LOCAL.yaml

> You must provide the encrypted values in the following santax ``` ![ENCTYPED_VALUE]```

**TEXT**: ``` "this is encrypted value" ```

**ENCRYPTED**: ``` 9T5f/4hVrfREVPUjGg+ZK6lt/GCx1KcJDjjXUf8+w7A=```

### **configs-LOCAL.yaml**
```
local:
  enc-test: "![9T5f/4hVrfREVPUjGg+ZK6lt/GCx1KcJDjjXUf8+w7A=]"
  normal-test: "this is non encrypted value"

```

### **get-health-implementation.xml**
We will need to read the secure property in our transformation to demostrate.
```xml
%dw 2.0
output application/json
---
{
  status: "Running",
  version: "r.master.9999",
  normal: p('local.normal-test'),
  normalUsingSecureModule: p('secure::local.normal-test'), 
  decrypted: p('secure::local.enc-test')
}
```




![](/.attachments\secure-prop\health-impl.PNG)


# API Development
Please click [here](/demo-api/README.md) to refer RAML specification


## Run the Secure Property API
We have completed the initial developent, now we can run the project and test it.

1. Right click on the project, select Run As and Mule Application (configure) 
    ![](/.attachments/secure-prop\run-configuration-0.PNG)

2. Provide **API_MASTER_KEY** in the Arguments as a **Program arguments**, so Mule can use to decrypt the encryted values and click on **Run** button.
 ![](/.attachments/secure-prop\run-configuration.png)

3. In the console tab, you might have noticed there were some information of Building and then starting Mule runtime will display. Once you will see the app secure-property is DEPLOYED which means our API is running now. 
 ![](/.attachments/secure-prop\deployed.png)

4. You can access this API on url "http://localhost:8081/secure-property-api/api/health"
     ![](/.attachments/secure-prop\api-output.png)

- Congratulation your API is up and running. :)
