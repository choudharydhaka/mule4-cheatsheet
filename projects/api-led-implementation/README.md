
# Intro
Claims API allows to demostrate API-LED connectivity approach over health endpoint. We can run all 3 layer API's on both onPrim and CloudHub.

>Note: To run the APIs on Cloudhub please generate a Mule Java KeyStore for TLS connectivity. Please follow the details below.

# Generate Mule JKS
We need to make sure the CN align with your CloudHub DNS app name.
```sh
## <TODO> - Please provide a dns name of your cloudhub APP 
## Exampel *ssin.us-e2.cloudhub.io
keytool -genkeypair -dname "CN=*<APPNAME>.us-e2.cloudhub.io, OU=Integartion, O=dhaka, L=New Delhi, ST=New Delhi, C=IN"  -keypass password  -storepass changeit -validity 9999 -keystore mule.jks -alias server-crt   -ext SAN=IP:127.0.0.1

```

## Example
```sh
## <TODO> - Please provide a dns name of your cloudhub APP 
## Exampel *ssin.us-e2.cloudhub.io
keytool -genkeypair -dname "CN=*ssin.us-e2.cloudhub.io, OU=Integartion, O=dhaka, L=New Delhi, ST=New Delhi, C=IN"  -keypass password  -storepass changeit -validity 9999 -keystore mule.jks -alias server-crt -ext SAN=IP:127.0.0.1

```

# Envrionment Variables
|Name|Values| Comment|
|--|--|-----|
|MULE.ENV|DEV  or PROD| Pease use PROD for Cloudhub Configs, Also update the downstream.host values in the config-PROD.yaml file|


# Endpoint
All the API has enabled both HTTP and HTTPS endpoint, please check config-DEV/PROD.yaml files.

|API Name|Layer|Resource| Comment|
|--|--|---|--|
|Claims API|EXP| GET /api/health| Get health check of Claims API only|
||EXP GET /api/health?downstream=true| Get health check of all the Claims API|
|Claims PAPI|PROC| GET /api/health| Get health check of Claims API only|
||PROC | GET /api/health?downstream=true| Get health check of all the Claims API|
|Claims SAPI|SYS| GET /api/health| Get health check of Claims API only|
||SYS| GET /api/health?downstream=true| Get health check of all the Claims API|


# Run on CloudHub
- Create you APP 
- Update Config-PROD.yaml with the DNS name
```sh
## <TODO> - Please provide a dns name of your cloudhub APP 
## Exampel *ssin.us-e2.cloudhub.io
keytool -genkeypair -dname "CN=*ssin.us-e2.cloudhub.io, OU=Integartion, O=dhaka, L=New Delhi, ST=New Delhi, C=IN"  -keypass password  -storepass changeit -validity 9999 -keystore mule.jks -alias server-crt -ext SAN=IP:127.0.0.1

```
- Import in the Runtime manager

# Run on Anypoint Studio/OnPrim
- Update Config-DEV.yaml details if you want otherwise it should work fine 
|API Name|HTTP PORTS|HTTPS PORTS|
|--|--|---|
|Claims API|8081| 8082
|Claims PAPI|8083|8084|
|Claims SAPI|8085|8086|
- Create JKS for all 3 API's
## Example
```sh
keytool -genkeypair -dname "CN=locahost, OU=Integartion, O=dhaka, L=New Delhi, ST=New Delhi, C=IN"  -keypass password  -storepass changeit -validity 9999 -keystore mule.jks -alias server-crt -ext SAN=IP:127.0.0.1
```
- Import the Studio and run the APIs