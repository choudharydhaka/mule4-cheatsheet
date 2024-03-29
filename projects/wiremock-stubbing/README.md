- [URL matching](#url-matching)
- [Exact path matching](#exact-path-matching)
- [Request header matching](#request-header-matching)
- [Query parameter matching](#query-parameter-matching)
- [Request body matching](#request-body-matching)
- [JSON body matching](#json-body-matching)
- [Query parameter matching](#query-parameter-matching-1)
- [Request body matching](#request-body-matching-1)
- [XML body matching](#xml-body-matching)
- [Sending response header](#sending-response-header)
- [Specifying the response body](#specifying-the-response-body)
- [base64Body](#base64body)
  * [Mermaid for Azure DevOps](#mermaid-for-azure-devops)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>

# Prerequisite
Please make sure all the request goes to endpoint **POST localhost:8081/__admin/mappings**
```
POST localhost:8081/__admin/mappings
{
    "request": {
        "method": "GET",
        "urlPath": "/query"
    },
    "response": {
        "status": 200
    }
}
```
## URI Path in resposne
- urlPathPattern
- **{{request.path.[3]}}**
- "fixedDelayMilliseconds": 2000
```
{  
    "request": {
        "method": "GET",
        "urlPathPattern": "/api/v1/enrolments/.*"
				 
    },
	"response": {
        "status": 200,
		"headers": {
            "Content-Type": "application/json",
            "Cache-Control": "no-cache"
        },
         
           "fixedDelayMilliseconds": 2000,
           "body": "{ \"id\": \"{{request.query.identifier}}\"}"
	}
}
```
## Query Path in resposne
- urlPathPattern
- **{{request.path.[3]}}**
- "fixedDelayMilliseconds": 2000
```
{  
    "request": {
        "method": "GET",
        "urlPathPattern": "/api/v1/enrolments\\?identifier=.*"
				 
    },
	"response": {
        "status": 200,
		"headers": {
            "Content-Type": "application/json",
            "Cache-Control": "no-cache"
        },
         
           "fixedDelayMilliseconds": 2000,
           "body": "{ \"id\": \"{{request.query.identifier}}\"}"
	}
}
```
## Body Matching
- bodyPatterns
```json
{  
    "request": {
        "method": "POST",
        "urlPattern": "/api/v1/orders"
		,
       	"bodyPatterns" : [ {
            "matchesJsonPath" : {
                "expression": "$.description",
                "contains": "Order to suspend Enrolment"
            }
    } ]				  
    },
	"response": {
        "status": 201,
		"headers": {
            "Content-Type": "application/json",
            "Cache-Control": "no-cache"
        },
           "fixedDelayMilliseconds": {{delay}},
        "body":  "{ \"success\" : true, \"orderNumber\" : \"O-00124355\", \"accountNumber\" : \"{{jsonPath request.body '$.existingAccountNumber'}}\", \"status\" : \"Completed\", \"subscriptionNumbers\" : [ \"{{jsonPath request.body '$.subscriptions[0].subscriptionNumber'}}\" ]}"
	}
}
```

## URL matching
```
{
    "request": {
        "method": "PUT",
        "urlPattern": "/thing/matching/[0-9]+"
    },
    "response": {
        "status": 200
    }
}
```
## Exact path matching
```
{
    "request": {
        "method": "GET",
        "urlPath": "/query"
    },
    "response": {
        "status": 200
    }
}
```
## Request header matching
```
{
    "request": {
        "method": "POST",
        "url": "/with/headers",
        "headers": {
            "Content-Type": {
                "equalTo": "text/xml"
            },
            "Accept": {
                "matches": "text/.*"
            },
            "etag": {
                "doesNotMatch": "abcd.*"
            },
            "X-Custom-Header": {
                "contains": "2134"
            }
        }
    },
    "response": {
            "status": 200
    }
}
```

## Query parameter matching
```
{
    "request": {
        "method": "GET",
        "urlPath": "/with/query",
        "queryParameters": {
            "search": {
                "contains": "Some text"
            }
        }
    },
    "response": {
            "status": 200
    }
}
```
## Request body matching
```
{
    "request": {
        "method": "POST",
        "url": "/with/body",
        "bodyPatterns": [
            { "matches": "<status>OK</status>" },
            { "doesNotMatch": ".*ERROR.*" }
        ]
    },
    "response": {
            "status": 200
    }
}
```
## JSON body matching
```
{
    "request": {
        "method": "POST",
        "url": "/with/json/body",
        "bodyPatterns" : [
            { "equalToJson" : "{ \"houseNumber\": 4, \"postcode\": \"N1 1ZZ\" }", "jsonCompareMode": "LENIENT" }
        ]
    },
    "response": {
            "status": 200
    }
}
```
```
{
    "request": {
        "method": "POST",
        "url": "/with/json/body",
        "bodyPatterns" : [
            { "matchesJsonPath" : "$.status"},
            { "matchesJsonPath" : "$.things[?(@.name == 'RequiredThing')]" }
        ]
    },
    "response": {
            "status": 201
    }
}
```



## Query parameter matching

```
{
    "request": {
        "method": "GET",
        "urlPath": "/with/query",
        "queryParameters": {
            "search": {
                "contains": "Some text"
            }
        }
    },
    "response": {
            "status": 200
    }
}
```

## Request body matching
```
{
    "request": {
        "method": "POST",
        "url": "/with/json/body",
        "bodyPatterns" : [
            { "equalToJson" : "{ \"houseNumber\": 4, \"postcode\": \"N1 1ZZ\" }", "jsonCompareMode": "LENIENT" }
        ]
    },
    "response": {
            "status": 200
    }
}
```
```
{
    "request": {
        "method": "POST",
        "url": "/with/json/body",
        "bodyPatterns" : [
            { "matchesJsonPath" : "$.status"},
            { "matchesJsonPath" : "$.things[?(@.name == 'RequiredThing')]" }
        ]
    },
    "response": {
            "status": 201
    }
}
```
## XML body matching
```
"bodyPatterns" : [
    { "equalToXml" : "<thing>value</thing>" }
]
```
```
{
    "request": {
        "method": "PUT",
        "url": "/xpath",
        "bodyPatterns" : [
            { "matchesXPath" : "/todo-list[count(todo-item) = 3]" },
        ]
    },
    "response": {
            "status": 200
    }
}
```
```
{
    "request": {
        "method": "PUT",
        "url": "/xpath",
        "bodyPatterns" : [
            { "matchesXPath" : "/stuff:outer/stuff:inner[.=111]" ,
                "withXPathNamespaces" : {
                    "stuff" : "http://foo.com/"
                }
            },
        ]
    },
    "response": {
            "status": 200
    }
}

```

## Sending response header
```
{
    "request": {
        "method": "GET",
        "url": "/whatever"
    },
    "response": {
        "status": 200,
        "headers": {
            "Content-Type": "text/plain",
            "Cache-Control": "no-cache"
        }
    }
}
```

## Specifying the response body
```

{
    "request": {
        "method": "GET",
        "url": "/body"
    },
    "response": {
        "status": 200,
        "body": "Literal text to put in the body"
    }
}
```

> To read the body content from a file, place the file under the __files directory. By default this is expected to be under src/test/resources when running from the JUnit rule. When running standalone it will be under the current directory in which the server was started. To make your stub use the file, simply call bodyFile() on the response builder with the file’s path relative to __files:


```
{
    "request": {
        "method": "GET",
        "url": "/body-file"
    },
    "response": {
        "status": 200,
        "bodyFileName": "path/to/myfile.xml"
    }
}
```

## base64Body
```
{
    "request": {
        "method": "GET",
        "url": "/binary-body"
    },
    "response": {
        "status": 200,
        "base64Body" : "WUVTIElOREVFRCE="
    }
}
```

### Mermaid for Azure DevOps

```mermaid

::: mermaid

sequenceDiagram
    participant Client as API Client
    participant Proc as Demo Proc API
    participant Sys as Demo Sys API 
    participant db as Demo Database

    Client->>+Proc: GET /addresses/{pincode}

    alt Validation passed
	Proc ->> +Sys: [GET] /addresses/{pincode}
	alt Validation passed
	    Sys ->>+Register: [dbo].[Addresses] Stroed Proc
	    alt Validation passed

	        Register-->>-Sys: Address records result set

	    else Error
	        Register-->>-Sys: Error response
	    end
           Sys-->>Proc: Address Object JSON Response 
	else Error
	    Sys -->>-Proc: Error Object JSON response
        end

        Proc -->>Client : Address JSON response
    else Error
        Proc -->>Client : Error JSON response
    end

:::
```
# References
- https://wiremock.org/docs/request-matching/
