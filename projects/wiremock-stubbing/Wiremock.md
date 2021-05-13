# Wiremock

## Spin up container

### Virtual machine

You will need to install developer virtual machine (dev-vm) in you local system.

### How to install virtual machine
Please follow the wiki page [TODO here]())


## How to start container

### Prerequisite
#### 1. Developer Virtual machine
You will need to either install docker in your system or alternatively you can install developer virtual machine (Dev-vm) in your system.

Dev-vm has everything packeged in to start with which include both **docker** and **docker-compose**.

You can spin a container with docker run command or alternatively you can you use docker-compose with the yaml file.

> Wiremock docker image is designed for Openshift which reaquire couple of Openshift signing certs in base64 encoding, which then allow wiremock startup script to create JKS keystore and trust store. So we will need to create the JKS manually to run in the dev-vm and then mount those keystore with trust store.

### 2. JKS keystore

Please use below mentioned commands to creat the jks in your local system.

```sh
## move to tmp directory
cd /tmp/keystore/jks/

## Generate wiremock.jks keystore with self signed certificate using alias server-crt
keytool -genkeypair -dname "CN=*.localhost, OU=Dhaka, O=Dhaka, L=NEW Delhi, ST=New Delhi, C=IN"  -keypass password  -storepass changeit -validity 9999 -keystore wiremock.jks -alias server-crt 

## Expoert public certificate of alias server-crt
keytool -export -keystore wiremock.jks -storepass changeit -alias server-crt -file server-pub.cer
## Create trust mule keystore
keytool -import -file server-pub.cer  -alias server-crt -keystore wiremock-trust.jks -storepass changeit -noprompt

```

### docker-compose.yaml
```yaml
version: '2'
services:
  wm:
    image: dhaks/wiremock:1.0.1-wiremock-2.24.1
    hostname: wm
    ports:
      - "9082:8082"
      - "9080:8080"
#      - "LOCAL_PORT:CONTAINER_PORT"
    volumes:
      - <UPDATE_ME>:/opt/wiremock/mappings:rw
      - /tmp/keystore/jks:/opt/wiremock/security
```

**UPDATE_ME** -> Please provide the absolute (full) location of your wiremock mocking files here, so it will be mounted to the container and Wiremock would be to read them.

## How to use Wiremock to mock



### Request with JSON response
```json
{
	"mappings": [
		{
			"request": {
				"method": "GET",
				"url": "/demo-api/v1",
			},
			"response": {
				"status": 200,
				"jsonBody": [{   
					"Name": "Dhaka"
				}
			
				],

				"headers": {
					"Content-Type": "application/json"
				}
			}
		}
		]
}
```
### Request with XML response


```json


{
	"mappings": [
		{
			"request": {
				"method": "POST",
				"urlPattern": "/demo-api/search",
				 
			},
			"response": {
				"status": 200,
				 "headers": {
				          "Content-Type": "application/xml"
				          },
				
						  "body": "<soap:Envelope xmlns:soap=\"http://www.w3.org/2003/05/soap-envelope\" xmlns:iden=\"http://integrason.dhaks.co.in/search/search_data/\"><soap:Header/><soap:Body><iden:search_Response><iden:search_matches><iden:name>dhaks</iden:name></iden:search_matches></iden:search_Response></soap:Body></soap:Envelope>"
			}
		}
    ]
}

```

### Validate Request query params


```json

{
	"mappings": [
		{
			"request": {
				"method": "GET",
				"url": "/demo-api/v1/v1",
				"queryParameters" : {
				  "team" : {
					"equalTo" : "integration"
				  }
				},
				"cookies" : {
				  "session" : {
					"matches" : ".*12345.*"
				  }
				},
			},
			"response": {
				"status": 200,
				"jsonBody": [{   
					"name": "Dhaka"
				}
			
				],

				"headers": {
					"Content-Type": "application/json"
				}
			}
		}
		]
}
```

### Validate Request headers

```json
{
	"mappings": [
		{
			"request": {
				"method": "GET",
				"url": "/demo-api/v1",
				"headers": {
					"x-gw-api-key": {
						"matches": "[A-Za-z0-9]+"
					},
					"x-gw-api-secret": {
						"matches": "(.*)"
					},
					"x-txn-identifiers": {
						"matches": "(.*)"
					}

				}
			},
			"response": {
				"status": 200,
				"jsonBody": [{   
					"name": "Dhaka"
				}
			
				],

				"headers": {
					"Content-Type": "application/json"
				}
			}
		}
		]
}
```

### Validate Request XML body

```json


{
	"mappings": [
		{
			"request": {
				"method": "POST",
				"urlPattern": "/demo-api/search",
				"bodyPatterns":[
					{
						"xPathNamespaces":{
							"iden":"http://integrason.dhaks.co.in/search/search_data/"
						},
						"matchesXPath" : "//iden:name"
					  },
				
					  {
						"xPathNamespaces":{
							"iden":"http://integrason.dhaks.co.in/search/search_data/"
						},
						"expression" : "//iden:name",
						"contains": "Dhaka"
					  }
					]
			},
			"response": {
				"status": 200,
				 "headers": {
				          "Content-Type": "application/xml"
				          },
				
						  "body":  "<soap:Envelope xmlns:soap=\"http://www.w3.org/2003/05/soap-envelope\" xmlns:iden=\"http://integrason.dhaks.co.in/search/search_data/\"><soap:Header/><soap:Body><iden:search_Response><iden:search_matches><iden:name>dhaks</iden:name></iden:search_matches></iden:search_Response></soap:Body></soap:Envelope>"
			}
		}
    ]
}

```
### Validate Request JSON body


```json
	
{
	"mappings": [
		{
			"request": {
				"method": "GET",
				"url": "/demo-api/v1",
				"bodyPatterns" : [ {
			         "matchesJsonPath" : "$.name"
			    },
                {	
                    "matchesJsonPath" : "$.things[?(@.name == 'RequiredThing')]"
                }
                ]
			},
			"response": {
				"status": 200,
				"jsonBody": [{   
					"name": "Dhaka",
				}
			
				],

				"headers": {
					"Content-Type": "application/json"
				}
			}
		}
		]
}
```

#### Matching example
```json
{ "things": { "name": "RequiredThing" } }
{ "things": [ { "name": "RequiredThing" }, { "name": "Wiremock" } ] }
  
 ``` 

#### Not matching example

 ```json
{ "price": 15 }
{ "things": { "name": "Wiremock" } }


```

### Create test cases using test data


### TC01 - Empty response when query param Name is "EMPTY"
```json
	
{
	"mappings": [
		{
			"request": {
				"method": "GET",
				"url": "/demo-api/v1",
				"queryParameters" : {
				  "Name" : {
					"equalTo" : "EMPTY"
				  }
				}
			},
			"response": {
				"status": 200,
                "jsonBody": {},
    			"headers": {
					"Content-Type": "application/json"
				}
			}
		}
		]
}
```
### TC02 - Empty list response  when query param Name is "LIST EMPTY"
```json
	
{
	"mappings": [
		{
			"request": {
				"method": "GET",
				"url": "/demo-api/v1",
				"queryParameters" : {
				  "Name" : {
					"equalTo" : "LIST EMPTY"
				  }
				}
			},
			"response": {
				"status": 200,
				"jsonBody": [],

				"headers": {
					"Content-Type": "application/json"
				}
			}
		}
		]
}
```

### TC03 - One object response when request body Name is "One"

```json
	
{
	"mappings": [
		{
			"request": {
				"method": "GET",
				"url": "/demo-api/v1",
				"bodyPatterns" : [
                   {   "matchesJsonPath" : {
                        "expression": "$.Name",
                        "contains": "One"
                    }}
                ]
			},
			"response": {
				"status": 200,
				"jsonBody": [{   
					"name": "Dhaka",
				}
			
				],

				"headers": {
					"Content-Type": "application/json"
				}
			}
		}
		]
}
```

### TC04 - Validate full transformation response when request body Name is "FULL"



```json
	
{
	"mappings": [
		{
			"request": {
				"method": "GET",
				"url": "/demo-api/v1",
				"bodyPatterns" : [
                    {
                      "matchesJsonPath" : {
                        "expression": "$.Name",
                        "contains": "FULL"
                    } }
                ],
			},
			"response": {
				"status": 200,
				"jsonBody": {   
					"name": "Dhaka",
				}
			
			
				,

				"headers": {
					"Content-Type": "application/json"
				}
			}
		}
		]
}
```
### TC05 - One object in the list response when request body Name is "LIST"

```json
	
{
	"mappings": [
		{
			"request": {
				"method": "GET",
				"url": "/demo-api/v1",
				"bodyPatterns" : [
                    {
                      "matchesJsonPath" : {
                        "expression": "$.Name",
                        "contains": "One"
                    }
                    }
                ]
			},
			"response": {
				"status": 200,
				"jsonBody": [{   
					"name": "Dhaka",
				}
			
				],

				"headers": {
					"Content-Type": "application/json"
				}
			}
		}
		]
}
```


### TC06 - More than one object in the list response when request body Name is "LIST5"


```json
	
{
	"mappings": [
		{
			"request": {
				"method": "GET",
				"url": "/demo-api/v1",
				"bodyPatterns" : [
                   {   "matchesJsonPath" : {
                        "expression": "$.Name",
                        "contains": "One"
                    }}
                ]
			},
			"response": {
				"status": 200,
				"jsonBody": [{   
					"name": "Dhaka",
				},
                {   
					"name": "Poonia",
				}
			
				],

				"headers": {
					"Content-Type": "application/json"
				}
			}
		}
		]
}
```

### TC07 - Error 500 response when request body Name is "ERROR500"


```json
	
{
	"mappings": [
		{
			"request": {
				"method": "GET",
				"url": "/demo-api/v1",
				"bodyPatterns" : [
                 {     "matchesJsonPath" : {
                        "expression": "$.Name",
                        "contains": "ERROR500"
                    }}
                ]
			},
			"response": {
				"status": 500,
				"jsonBody":  {
                    "status": 500,
                    "errorCode": "INTERNAL_SERVER_ERROR",
                    "errorMessage": "Internal server error",
                    "errorDescription": "Internal server error, Please check errorDetail and provide valid values",
                    "errorDetail": [
                    {
                    "item":"internal_server_error",
                    "message":"Internal server error, unexpected condition encountered"
                    }]
                },

				"headers": {
					"Content-Type": "application/json"
				}
			}
		}
		]
}
```
### TC08 - Error 400 response when request body Name is "ERROR400"


```json
	
{
	"mappings": [
		{
			"request": {
				"method": "GET",
				"url": "/demo-api/v1",
				"bodyPatterns" : [
                    {
                      "matchesJsonPath" : {
                        "expression": "$.Name",
                        "contains": "One"
                    }}
                ]
			},
			"response": {
				"status": 400,
				"jsonBody": {
                    "status": 400,
                    "errorCode": "VALIDATION_ERROR",
                    "errorMessage": "Invalid search parameters",
                    "errorDescription": "Validation has been failed, Please check errorDetail and provide valid values",
                    "errorDetail": [
                    {
                    "item":"modified_date",
                    "message":"Please provide valid date"
                    }]
                }
              ,

				"headers": {
					"Content-Type": "application/json"
				}
			}
		}
		]
}
```

