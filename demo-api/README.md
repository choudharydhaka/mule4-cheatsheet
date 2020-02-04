# Introduction 
  This API will be used to interact with the Marriage database (through iDAL SYS API). As per the requirement for SVC implementation, it will be having one resource to retrieve marriage registration details for a given marriage registration number. However, this API will/can be extended later to provide more functionality.



# Design Center Project
https://anypoint.mulesoft.com/designcenter/designer/#/project/056b7a17-060b-41da-b2da-cc5b433eeb10

# Exchange Asset
https://anypoint.mulesoft.com/exchange/0b6403b0-a69c-459a-90da-ae4b2f7fc2e4/ei-marriages-proc-api/

# URL
URL: ``` https://{env}.ei.dia.govt.nz/ei-marriages-proc-ap/{version}/```

where the value for **env** is one of mgmt-host or host from the table below.

|Env|Mgmt-host|Host|
|----|---------------------|--------------|
|DEV|sdo-dev-ei.ocp-nonprod|dev|
|TEST|sdo-test-ei.ocp-nonprod|test|
|QA|sdo-qa-ei.ocp-nonprod|qa|
|SUP|sdo-sup-ei.ocp-nonprod|sup|
|PROD|sdo-prod-ei.ocp-prod|prod|

# Downstream dependencies
iDAL sys api


## Release Notes
|Version|Date|ADO Reference|Change Summary|Changes|
|---|---|-------|-------------|----|
|1.0.0|30-Jan-2020|[23848](https://sdo-online.visualstudio.com/Te%20Ara%20Manaaki/_workitems/edit/23848)|Created first marriage api|Marriage api implemented, please check file **changelog** for more details |


# Build and Test
```bash
mvn clean package
```
```bash
mvn clean test
```
```bash
mvn clean install -DskipTests
```

# Run Locally
Define the following runtime arguments
-DAPI_SECRET_PROP_KEY=<ei-encryption-key> -DCONFIG_HOME=. -DMULE_ENV=LOCAL -M-Danypoint.platform.gatekeeper=disabled -M-Dwrapper.debug=true

> Note: Get the value for the ei-encryption key from the DEV release pipeline variable group EI-DEV-Secrets-Release-Pipeline-Variables

# Contribute

If you want to learn more about creating good readme files then refer the following [guidelines](https://docs.microsoft.com/en-us/azure/devops/repos/git/create-a-readme?view=azure-devops). You can also seek inspiration from the below readme files:
- [ASP.NET Core](https://github.com/aspnet/Home)
- [Visual Studio Code](https://github.com/Microsoft/vscode)
- [Chakra Core](https://github.com/Microsoft/ChakraCore)