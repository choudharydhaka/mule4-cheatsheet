# Introduction 
x



# Design Center Project
https://anypoint.mulesoft.com/designcenter/designer/#/project/TODO

# Exchange Asset
https://anypoint.mulesoft.com/exchange/TODO/demo-api/

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



## Release Notes
|Version|Date|ADO Reference|Change Summary|Changes|
|---|---|-------|-------------|----|
|1.0.0|30-Jan-2021||


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
-DAPI_SECRET_PROP_KEY=<encryption-key> -DCONFIG_HOME=. -DMULE_ENV=LOCAL -M-Danypoint.platform.gatekeeper=disabled -M-Dwrapper.debug=true

> Note: Get the value for the encryption key from the DEV release pipeline variable group Release-Pipeline-Variables

# Contribute

If you want to learn more about creating good readme files then refer the following [guidelines](https://docs.microsoft.com/en-us/azure/devops/repos/git/create-a-readme?view=azure-devops). You can also seek inspiration from the below readme files:
- [ASP.NET Core](https://github.com/aspnet/Home)
- [Visual Studio Code](https://github.com/Microsoft/vscode)
- [Chakra Core](https://github.com/Microsoft/ChakraCore)
