# Introduction

This API is to demostrate health check and running first API.

> We have used best practises to setup the project for any Mule4 API.
# Project Structure

![](/.attachments/project-explorer.PNG)

## Create new files
We should create few files to seperate similar functionality for easier management.

|File name|Description|
|---------|---------------------------------------------|
|error-handler.xml| Move all the error handlers here and refer them from ```flows``` or ```try``` scope|
|globals.xml| Move all the configuration related items which is shared across all the Mule files. example: http listeners/requeqesters, sftp connectors, APIkit configuration |
|interface.xml| This file must contains only main flow, and all the private flows. Private flows should call implementation flows/subflows|
|```<method>```-```<resource>```-implementation.xml| Implementation file should be a seperate file and called by the private flow. For naming conventions we should utilize MethodName-ResourceName-Implementation (all in lowercase), example: get-health-implementation.xml|
|configs-```<ENV>```.yaml| This is configuration file holding all the enviroment specific properties, example: LOCAL|
|test/postman||

> You might have noticed we have created couple of config files, second file has the name TEMPLATE, we will use this file to create file using shell script for all the environment dynamically based on the deployment enviroment.



