# Introduction

This API is to demonstrate health check and running first API.

# Prerequisite
Below mentioned tools must be installed in your development machine: 
- [OpenJDK](https://adoptopenjdk.net/)
- [Anypoint Studio](https://www.mulesoft.com/lp/dl/studio) 
- [Atom](https://atom.io/)
- [API workbench](http://apiworkbench.com/)

# Requirement
We need an API with health resource to demostrate.

# API Development
Any API development needs couple of steps:
- RAML/OAS Specification
This section requires to be implemented on eithor Anypoint Design Center or API Workbench(Locally). We will create an API called ```demo-api``` with one resource ```/health``` using API Workbench.

- API Implementation
This section requres the API specification to develop and run the API. We will use Anypoint Studio tool to implement the API.

> We have used best practises to setup the project for any Mule4 API.

## RAML Specification
### Project Structure
![](/.attachments/demo-raml-structure.PNG)

We will use modular approach for maximum reusability of all the raml fragements. We should create diffrent directory sturcture for all the specific raml type. 
|Name|Description|
|-----------|----------------------------------|
|dataTypes|This section contains all the data types for any request/response of the API and refer them from main API or resourceTypes raml defination|
|examples|We should move/create all the examples in this directory and refer them from main raml API defination|
|resourceTypes| Every resource defination should be created under this section and then refer them from main API raml definition|
|traits|These are the like small function and allows security restriction/requests/responses/headers, we want to add on the API resource|
|libraries|These are the collection of traits/resourceTyps/dataTypes, which then can be refers for grouped functionality|
|demo-api.raml|This is main RAML definition where we refer all the resourceTypes/dataTypes/examples/traits/libraries| 

Following the above best practises, I've created the demo-api specification.

![](/.attachments/demo-raml-structure-health.PNG)

# Project Structure

![](/.attachments/project-explorer.PNG)

## Create new files
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

