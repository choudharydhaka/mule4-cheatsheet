# Introduction

This project repository defines basic steps for designing API specification using API Workbench (without Anypoint design center) and then develop the API using Anypoint Studio. 

# Prerequisite
Below mentioned tools must be installed in your development machine: 
- [OpenJDK](https://adoptopenjdk.net/)
- [Anypoint Studio](https://www.mulesoft.com/lp/dl/studio) 
- [Atom](https://atom.io/)
- [API workbench](http://apiworkbench.com/)

You should have basic understanding of Mule4 developement or an significant background in integration space to starts with.

# Requirement
We need an API ```demo-api``` with ```/health``` resource to demostrate our first working API development.

# API Development
Any API development needs couple of steps:
- RAML/OAS Specification
- API Implementation


> We have used best practises to setup the project for any Mule4 API.

## RAML Specification
This section requires to be implemented on eithor Anypoint Design Center or API Workbench(Locally). We will create an API called ```demo-api``` with one resource ```/health``` using API Workbench.
### Project Structure
![](/.attachments/demo-raml-structure.PNG)

We will use modular approach for maximum reusability of all the raml fragements. We should create diffrent directory sturcture for all the specific raml type. 

### RAML directory structure

|Name|Description|
|-----------|----------------------------------|
|dataTypes|This section contains all the data types for any request/response of the API and refer them from main API or resourceTypes raml defination|
|examples|We should move/create all the examples in this directory and refer them from main raml API defination|
|resourceTypes| Every resource defination should be created under this section and then refer them from main API raml definition|
|traits|These are the like small function and allows security restriction/requests/responses/headers, we want to add on the API resource|
|libraries|These are the collection of traits/resourceTyps/dataTypes, which then can be refers for grouped functionality|
|documentation| We should define the documentation here as DocumentItem, RAML supports Mark Down inside content.|
|demo-api.raml|This is main RAML definition where we refer all the resourceTypes/dataTypes/examples/traits/libraries| 

Following the above best practises, I've created the demo-api specification which is stored under ```demo-api/src/main/resources/api``` for reference.

![](/.attachments/demo-raml-structure-health.PNG)

#### dataTypes/health.raml
This is a response data type file when client will request for /health resource. we don't have any request payload so we don't require any request dataType file for /health resource.

```raml
#%RAML 1.0 DataType

description: Status of the API with release version.
type: object
properties:
  status:
    type: string
    description: Status of the API.
    example: Running
  version:
    type: string
    description: Release version of the API deployed.
    example: r.master.0001
```
#### examples/health-get-response.json
This example is the response for /health resource, if you might have noticed this response is aligned with dataTypes/health.raml file.
```json
{
    "status": "Running",
    "version": "r.master.9999"
}
```
#### resourceTypes/health.raml
This resource type defines the /health resource for GET method, which includes the references to dataTypes and examples. 

```raml
#%RAML 1.0 ResourceType

usage: This is health resource response.

get:
  description: This is successful response.
  responses:
    200:
      body:
        application/json:
          type: !include ../dataTypes/health.raml
          example: !include ../examples/health-get-response.json
```

#### demo-api.raml
This is main API raml file, which defines the API specification by referencing resourceTypes only.


```raml
#%RAML 1.0

title: demo-api
description: This API is to demostrate and understand cheatseat API.
version: v1
baseUri: https://{environment}.dhaks.localhost.nip.io/demo-api/{version}/
mediaType: application/json
protocols: [HTTPS]

resourceTypes:
  health: !include ./resourceTypes/health.raml

/health:
  type: health

```
> finally we have defined the RAML specification for the demo-api locally using API workbench, now we need to implement the API using Anypoin Studio.

## API Implementation
This section requres the API specification to develop and run the API. We will use Anypoint Studio tool to implement the API.

To starts with development:
- import RAML specification 
- Create few new files to align with Mule4 best practices
- Develop/Test

### Import RAML
We need to start with a new Mule project in Anypoint studio, and then privide the address to main demo-api.raml file and it will populate many of the fields based on it. Finally finish the import, Anypoint studio will create few files for your development. I have defined project struction in next section, which includes new files are already created.

- Open Anypoint studio
- Start with new project creation

    ![](/.attachments/create-mule-project.PNG)
- Select Mule Project

    ![](/.attachments/create-mule-project-1.PNG)

- Provide path to the demo-api.raml file

    ![](/.attachments/import-api.PNG)

- Make sure all the highligted values are populated

    ![](/.attachments/create-mule-project-refer-local-raml.PNG)

- Once you proceed with finish button, this will create a project which includes a set of files created and referenced together in order to run the project.

    ![](/.attachments/mule-project-explorer.PNG)



### Create new files as Mule4 best practices
We should create few files to seperate similar functionality for easier management.

|File name| Description|
|---------|---------------------------------------------|
|error-handler.xml| Move all the error handlers here and refer them from ```flows``` or ```try``` scope|
|globals.xml| Move all the configuration related items which is shared across all the Mule files. example: http listeners/requeqesters, sftp connectors, APIkit configuration |
|interface.xml| This file must contain only main flow, and all the private flows. Private flows should call implementation flows/subflows|
|```<method>```-```<resource>```-implementation.xml| Implementation file should be a seperate file and called by the private flow. For naming conventions, we should utilize MethodName-ResourceName-Implementation (all in lowercase), example: get-health-implementation.xml|
|configs-```<ENV>```.yaml| This is configuration file holding all the enviroment specific properties, example: LOCAL|
|test/postman|Postman collection file should be attached to the API repository for any kind of automation in future stage. |

> You might have noticed we have created couple of config files, second file has the name TEMPLATE, we will use this file to create file using shell script for all the environment dynamically based on the deployment enviroment.


### Project Structure
- After creating new files, we will rename ```demo-api.xml``` to ```interface.xml```.
- New project structure
    
    ![](/.attachments/project-explorer.PNG)

We can run this project now easily, however we will move some configuration to these newly created files.


### Globals.xml
- Open globals.xml file
    ![](/.attachments/globals-1.PNG)    
- Create new configuration for http listener
    ![](/.attachments/globals-create.PNG)
      
- Type http in search bar, then select ```HTTP Listener config``` and click ok
    ![](/.attachments/globals-http-ok.PNG)     
- Make sure all the values are in place  
    ![](/.attachments/globals-http-provided.PNG)
- Click on OK and save the document.

### error-handler.xml
- Open interface.xml file and goto xml view
    ![](/.attachments/goto-xml.png)
- Select and cut error-handler element
    ![](/.attachments/error-handler-cut.PNG) 
- Open error-handler.xml file
    ![](/.attachments/globals-http-ok.PNG) 
- goto xml view 
- Paste the copied element here, Add new attribute called "name" and the value "api-error-handler" and save it.
    ![](/.attachments/error-handler-paste.PNG) 

- Once, we have moved the error hander, now reference this error handler from interface.xml file and save it.
    ![](/.attachments/error-handler-refer.PNG)

### interface.xml
We will move couple of items into both gloabls.xml and error-handelr.xml. 
- If you might have noticed we have moved error-handler element into ```api-error-handler.xml``` file and refering it from interface.xml
- Open ```interface.xml``` file and goto xml view (configuration XML tab on the bottom right)
- Find element
    ![](/.attachments/interface-http-listener-delete.PNG)
- Reference new Listener element we already created in globals.xml file, update the value of ```config-ref``` attribute in ```http:listener`` with **API_HTTP_Listerner_config** and save it.
    ![](/.attachments/interface-http-listener-initial-config.PNG)


## Run 
We have completed the initial developent, now we can run the project and test it.

- Right click on the project, select run 
    ![](/.attachments/demo-api-run.PNG)
- In the console tab, you might have noticed there were some information of Building and then starting Mule runtime will display. Once you will see the demo-api DEPLOYED which means our API is running now. 
    ![](/.attachments/demo-api-running.PNG)

- You can access this API on url "http://localhost/8081/demo-api/health"
    ![](/.attachments/pm-demo-api-get-health-check-response.PNG)

- Congratulation your API is up and running. :)
