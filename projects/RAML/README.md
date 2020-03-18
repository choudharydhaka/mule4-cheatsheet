## File naming
- lowercase
- alaphanumeric
- snake case

## Header
### API Title
- should be short
- plain text label
ex: ```title: ACME Banking API```

### Version
- Alphanumeric

example: 

``` versoin:1.0.0```

### Protocols
- Optional
- if not specified and ``` baseUri``` node is used.
``` protocols: [HTTP, HTTPS]```
### Media Types
- optional
- sets default media type 
``` mediaType: application/json```

NOTE: If mediaType is set at the root level, the media type within each body definition does not need to be specified.

NOTE: Explicitly defining a mediaType node for a body of an API request or response overrides the default media type.

### Documentation
- provides humain friendly description
- [GitHub-Flavored Markdown](https://help.github.com/categories/writing-on-github/)

```raml
#%RAML 1.0 DocumentationItem
title: ACME Banking API Home
content: |
  **ACME Banking API** enables developers to build applications that make use of the information from resource methods implemented in the API.

  This API contains functionality that allows developers to retrieve and manipulate _customer_, _account_ and _transaction_ information. Check out the [API Portal]() for more details.

```
API Refers:

```
documentation:
 - !include documentation/acme-bank-doc.raml
```

### Data Types
- Concise and powerful way of describing the information utilized within the API
- Adds validation rules against a type deeclaration
- created as separate files
- included in the API Raml file

For example (account.raml):

```
#%RAML 1.0 DataType
type: object
properties: 
  accountID: string
  accountType: 
    enum: [Checking, Savings, Overdraft Savings, Credit Card] 
  accountNumber: string
  accountOwner: 
    type: array
    items: !include account-owner.raml
  accountBalance: !include money.raml
  IBAN:
    type: string
    pattern: ^[A-Z]{2,2}[0-9]{2,2}[a-zA-Z0-9]{1,30}$
  bank: !include bank.raml
  interestRate?:
    type: number
    format: double
  createdAt: datetime
  modifiedAt?: datetime 
```
The types node within the API RAML is used to refer to external DataTypes:
```
types: 
  customer: !include datatypes/customer.raml
  account: !include datatypes/account.raml
  transaction: !include datatypes/transaction.raml
```


DataType declarations can also be described using schemas:
- For JSON, use JSON Schema (http://json-schema.org/)
  -	JSON Schema files should use the .schema.json suffix
- For XML, use XML Schema (XSD) (https://www.w3.org/XML/Schema)
  - XML Schema files should use the .xsd suffix

### ResourceType
- reuse patterns across multiple resources and methods
- consistency
- reduce complaxity
- optional
- improve readibility
- created in separate files

For example (collection.raml):

```
#%RAML 1.0 ResourceType
post?:
  description: Add a new <<resourcePathName | !singularize>>
  displayName: Add new <<resourcePathName | !singularize>>
  body:
    type: <<resourcePathName | !singularize | !uppercamelcase>>
  responses: 
    201:
      headers: 
        Location:
          description: URL of the new <<resourcePathName | !singularize>> information
          example: /<<resourcePathName>>/8f19cb50-3f57-4d38
      body: 
    202:
      description: The request has been accepted for processing. Use the URI provided in the Location header of the response to monitor the status.
      headers: 
        Location:
          example: /<<resourcePathName>>/1234/status
    503:
      body:
        type: <<customErrorDataType>>
```
The resourceTypes node within the API RAML is used to refer to external ResourceTypes:
```
resourceTypes: 
  collection: !include resourceTypes/collection.raml
  member: !include resourceTypes/member.raml
```
Resource types can be mapped in the API RAML by referencing the assigned type:
```
/customers:
  type:
    collection:
      customErrorDataType: customErrorMessage
```

### Traits
- like a method
- can provide method level nodes suck as -
    - description
    - headers
    - query string parameters
    - responses
- Methods that use one or more traits inherit nodes of those traits
- optional
- improve readibility
- created in separate files


For example (cacheable.raml):
```
#%RAML 1.0 Trait
usage: Apply this trait to any GET method that supports caching control.
responses: 
  200:
    headers: 
      Cache-Control:
        description: |
          Activates caching and defines cache behavior through cache response directives. 
          Usually defines public or private (cacheable by proxy or not) and max-age for resource.
          See http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html for more information.
        example: private, max-age=31536000
      Expires:
        description: |
          Sets a date in RFC 1123 format from which the cached resource should no longer be considered valid.
          If both the Expires header and max-age in the Cache-Control header are set, max-age will take precedence.
        type: datetime
        example: Tue, 18 Apr 2017 09:30:41 GMT
        format: rfc2616
```
The traits node within (not in the traits itself) the **API RAML** is used to refer to external Traits:

```
traits: 
  cacheable: !include traits/cacheable.raml
```
Traits can be mapped in the **API RAML** by referencing the assigned trait:
```
/customers:
  get:
    is: [ cacheable ]
    description: Retrieve a list of customers
    displayName: Get all customers
    securedBy: oauth2_0
```


### **Security Schemes**
- Authentication pattern supported by the API must be expressed as an element of securitySchemees
- separate files
- Included in API RAML

For example (oauth2.raml):
```
#%RAML 1.0 SecurityScheme
type: OAuth 2.0
description: Apply the OAuth 2.0 security policy to resource methods for authenticating API requests
describedBy:
  headers:
      Authorization:
        description: |
          Used to send a valid OAuth2 access token. Do not use with the "access_token" query. string parameter.
        type: string
  queryParameters:
    access_token:
      description: |
        Used to send a valid OAuth2 access token. Do not use together with the "Authorization" header.
      type: string
  responses:
    401:
      description: |
        Bad or expired token. This can happen if the API consumer uses a revoked or expired access token. To fix, you should re-authenticate the user.
    403:
      description: |
        Bad OAuth request (wrong consumer key, bad nonce, expired timestamp...). Unfortunately, re-authenticating the user won't help here.
settings:
  authorizationUri: https://placeholder.com/oauth2/authorize
  accessTokenUri: https://placeholder.com/oauth2/access_token
  authorizationGrants: implicit
```

The securitySchemes node within the API RAML is used to refer to external security schemes:
```
securitySchemes: 
  oauth2_0: !include securitySchemes/oauth2.raml
```
NOTE: The default security schema for all resources can be defined by adding the **securedBy**  tag at the __root level__. Each method can override the default security scheme by having its own securedBy tag.


## Structuring RAML Resources
A RAML file may reference additional resources, like 
- traits 
- resourceTypes
- JSON Schemas
- example data

API authors are encouraged to follow certain conventions for structuring and naming external resources. According to these conventions, the following relative URL paths and file suffixes should be used:

|Resource|Path|Suffix|Example|
|----|---------|-----|-------|
|API Definition	|/	|.raml	|acme-banking.raml
|Trait	|/traits/	|.raml	|cacheable.raml
|Resource Types	|/resourceTypes/	|.raml	|collection.raml
|Security Schemes	|/securitySchemes/	|.raml	|oauth2.raml
|Data Types	|/dataTypes/	|.raml	|account.raml
|           |               |.schema.json|account.schema.json
|           |               |.xsd|account.xsd
|Documentation|/documentation/|.raml|acme-bank-doc.xml
|Examples|/examples/|.raml|account-example.raml
|           |               |.json|account-example.json
|           |               |.xml|account-example.xml

References between these resources of the RAML API definition should be made by relative URLs.


## Error Messages
Every API should use a common error message schema for when an error needs to be returned in a response payload:

The common error type schema contains these attributes:

- name – Name of the API returning the response.
- code – The HTTP Status code of the response.
- title – The human-readable text of the equivalent HTTP status code.
- link – URL that provides additional information on the status code (optional). 
- transactionID – The unique ID associated with the transaction in which this API is involved (also known as a correlation ID).
- description – A detailed message describing the problem in relation to the error type in human-readable form.
- invocationTimestamp – The time at which the API was invoked (optional).

```json
{
    "name": "Reporting Process API",
    "code": "201",
    "title": "Created",
    "link": "http://mulesoft.org/..../LoB/API.html#201",
    "transactionID": "c70332d0-260f-11e7-98a1-c4b301c8ce30",
    "description": "Report Generated Successfully.",
    "invocationTimestamp": "2017-10-02T17:42:38Z"
}
```

## References

|Purpose|Tool|
|--------------------|-----------------------|
|XSDs to JSON schema conversion	| XSDs to JSONs
|RAML DataType to JSON Schema and vice version| JSONs to RAML-DataTypes 
|Online Tool to convert RAML APIs <br> Supported conversions:<br>From RAML 0.8 to RAML 1.0<br>From RAML 0.8/1.0 to OAS 2.0/3.0<br>From OAS 2.0 to RAML 1.0 <br>From OAS 2.0 to OAS 3.0|  Online Converter Tool: https://mulesoft.github.io/oas-raml-converter/ <br> Source Code<br>RAML Lint Tool	https://github.com/QuickenLoans/ramllint
|API Workbench – RAML IDE | http://apiworkbench.com/

