---
layout: default
title: Paths and Resources
nav_order: 7
---

# Paths and Resources
{: .no_toc}

This section provides details on resources and fields and provides the associated Finastra naming conventions and standards.

- TOC
{:toc}

## Introduction

A REST API's path consists of an HTTP method, a base path and one or more resources and identifiers.

The key abstraction of information in REST is a resource. A resource is a concrete concept related to the business domain e.g. `accounts`. Any information that can be named as a noun can be a resource, for example, a REST resource can be a document or image, a temporal service, a collection of other resources, or a non-virtual object e.g. a person. REST resources are typically plural nouns e.g. `accounts` rather than `account`.

A resource identifier is the unique value or key that is used to identify a resource.

Business domain resources are manipulated using HTTP methods as
required by the business and resources should be addressable via a URI that is 
compatible with [RFC1035](https://tools.ietf.org/html/rfc1035).

The following is an example of a set of CRUD endpoints against an `accounts` resource with an identifier of `{accountId}`:
```source    POST     /accounts                  < - - create a single account
    GET      /accounts                  < - - return a list of accounts
    GET      /accounts/{accountId}      < - - return a single account
    PUT      /accounts/{accountId}      < - - update a single account
    DELETE   /accounts/{accountId}      < - - delete a single account
```
The remainder of this section describes paths, resources, resource identifiers and the rules associated with a resource's Create, Update, Read, Delete (CRUD) lifecycle model.

## Paths

REST APIs use paths to access resources. The following shows a typical path with the OAS2 and OAS3 keywords used to define the path:
```
GET        https://apisandbox.finastra.com/finastraapis/v1/currencies
operation-\scheme/\----------host---------/\---basepath---/\--path--/     # at OAS2

GET        https://apisandbox.finastra.com/finastraapis/v1/currencies
operation-\------------------servers url------------------/\--path--/     # at OAS3
```

The following standards apply to paths:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| SCM-002 | Finastra Open APIs **MUST** use HTTPS and TLS i.e. RFC2660 and RFC2818 are enforced |
| SCM-003 | Finastra Open APIs **SHOULD NOT** define `host` or `basepath`  or `servers` and `url` values because these values are defined in external configuration files |


## Resources

This section provides guidelines on defining resources and identifiers.

### Be Consistent

Resource names **MUST** be defined consistently both within an API for each `GET`, `POST`, `PUT`, `DELETE` method
 and across APIs.

When defining Finastra APIs and paths, it is recommended that the name of the API is the same as the principal resource within the API, for example, an account API should be named Accounts and have at least one endpoint using a `/accounts` resource. 

API designers SHOULD avoid prefixing the `/accounts` resource in the Accounts API with parent resources e.g. **avoid** `/customer/{customerId}/accounts` unless necessary

### Prefer Single Root Resource

In most scenarios the following paths which use a resource at root level can be used. This approach is used when the resource is not a sub-resource and the identifier is unique for all instances of the resource:
- `POST /resources`
- `GET /resources`
- `GET /resources/{resourceId}`
- `PUT /resources/{resourceId}`
- `DELETE /resources/{resourceId}`

The following Finastra rule applies:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PAR-001 | Using a single root resource with a unique identifier in the path **SHOULD** be adopted – it is the preferred approach, for example, use: `GET /resource/{resourceId}` rather than: `GET /parent-resource/{parentId}/resource/{resourceId}` for a resource with a unique identifier across all resource instances |

 
### Defining Sub-Resources

There are scenarios where multiple path segments and sub-resources can be used.

Sub-resources can be used when they are related to and dependant on the associated parent resource, for example:
 `GET /clients/{clientId}/ratings/{ratingId}`
is used rather than `GET /ratings/{ratingId}` when a `rating` cannot be created in isolation. 

If the `rating` resource is not dependant on the `client` resource then `GET /ratings/{ratingId}` should be used.

The following Finastra rule apply:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PAR-010 | Sub-resources **SHOULD** be used when the sub-resource cannot exist without the parent resource, for example: `clients/{clientId}/ratings/{ratingId}` |
| PAR-011 | When using sub-resources, where possible, the number of path segments **SHOULD** be limited to four to reduce the complexity of the API |
| PAR-012 | When using sub-resources, be aware of the API context, for example, the following endpoint **MAY** be defined in a customer API BUT it **SHOULD NOT** be used in an account API: `GET /clients/{clientId}/accounts/{accountId}` |
| PAR-013 | When using sub-resources, endpoints **MUST** be unambiguous, for example, the sub-resource endpoints should not be provided as both a root resource endpoint e.g. `GET /accounts/{accountId}` and a sub-resource endpoint e.g. `GET /clients/{clientId}/accounts/{accountId}` |


### Defining Resources with Non-Unique Identifiers

When a resource identifier is NOT unique across all instances of a resource then there are the following options for defining the path:

**Sub-resources** can be used, for example, the following path handles scenarios where the `accountId` is not unique across all accounts, but is unique for a given `clientId`: 
- `GET /clients/{clientId}/accounts/{accountId}`

Note that `GET accounts/{accountId}` would not support the same scenario because `accountId` is not unique across all accounts.

The following rules apply:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PAR-020 | When using sub-resources to handle resources with non-unique identifiers the number of path segments SHOULD not exceed four – see the compound keys section for further details |
| PAR-021 | When using sub-resources to handle resources with non-unique identifiers the relationship, e.g. 1 to many, between the parent resource and the sub-resource SHOULD be unchanging otherwise the endpoint may need to change |

 **Compound keys** can be used when more than four path segments would be needed, for example, use:
- `GET /resources/{resourceId}`
rather than
- `GET /resource1s/{1id}/resource2s/{2id}/resource3s/{3id}`

The following rules apply:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PAR-030 | When using compound keys it is expected that the external developer using the API contract **SHOULD NOT** need to know how the compound key is constructed i.e. they would only be aware of the unique identifier not its individual components |
| PAR-031 | The API contract **SHOULD NOT** leak implementation details of the compound keys |
| PAR-032 | When using compound keys the implementation **SHOULD** take care to ensure that conflicts with the compound key identifier and any compound key values in the body of a request are handled appropriately and consistently e.g. so that the constituent parts of a compound key are not modified by a `PUT` operation |
| PAR-033 | Compound keys with delimeters such as a slash or hyphen **SHOULD NOT** be used to define individual compound key identifiers because they are not easily extensible if the compound key implementation differs between providers of the API e.g. one provider might use two identifiers to ensure uniqueness of the compound key whereas another provider might require three identifiers to ensure uniqueness of the compound key: <br>- MUST NOT use - `GET /resources/{parentId}/{resourceId}` <br>- MUST NOT use - `GET /resources/{parentId}-{resourceId}` |
| PAR-034 | Query parameters **SHOULD NOT** be used to define individual compound key identifiers:<br> - MUST NOT use – GET /resource/{parentId}?id={resourceId} |

### Defining Resource Actions

Use cases may arise where the HTTP method is not adequate to perform an action on a resource.

In these cases an action, named a *resource controller*, may be used against a resource, for example:

-   `POST /accounts/{accountId}/transactions/{transId}/confirm` can be used to confirm an existing transaction

The *resource controller* approach is not recommended because alternative designs can often be found, for example, the same result can be achieved with:
`PATCH /accounts/{accountId}/transactions/{transId}` with a payload of `{"confirmation-status" : "complete" }` 

Similarly, an action should not be used when the action itself creates a new resource. In these cases its is better to define the resource on the path, for example, consider the case where a client requests a deal pricing scenario to create a pricing session - to achieve this the following path options are possible, however, the *resource controller* approach shown in the second example is not recommended:

- 1 - `POST /pricing-sessions  { dealId="deal1" , scenario="123"}` returns `{"pricingSessionId"="abc"}` - this resource oriented approach is recommended
- 2 - `POST /deals/{deal1}/price  {"scenarioId" = "123"}` returns `{"pricingSessionId"="abc"}` - this approach should be avoided

### Defining Resource Identifiers 

An identifier is the unique value or key that is used to identify a resource, for example: 
```
GET /accounts/{accountId}
{ 
    accountId = "2c76539b-c793-41aa-8082-87621b4e574b"
}
```
The following rules apply to rsource identifiers:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PAR-035 | Resource identifiers **MUST** be a unique technical identifier of a resource and should be a string|
| PAR-036 | Resource identifiers **SHOULD** be named as `<resource-name>Id` where `<resource-name>` is the name of the resource e.g. the resource identifier for a resource named `accounts` will be `accountId` |
| DEF-027<br>IDS-001 | Resource identifiers **SHOULD NOT** be named `{id}` or `{Identifier}` |
| PAR-037 | Resource identifiers **MUST NOT** use personally identifiable information (PII) for the resource identifier |
| PAR-038 | Resource identifiers **SHOULD NOT** be suffixed with `Number` because the suffix `Number` is typically associated with fields containing personally identifiable information (PII) e.g. `accountNumber`, `customerNumber`, `taxIdentificationNumber` |
| PAR-039 | The resource payload **SHOULD** contain a *read only* field named `<resource-name>Id` |


### Finastra Standards for Paths and Resources

The following lists the Finastra standards for paths and resources:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PAR-040 | **MUST** define *useful* resources - see the *Finastra Open API Principles* section |
| PAR-041 | **MUST** use Finastra common business object names where available |
| PAR-042 | **MUST** name resources with nouns |
| PAR-043 | **SHOULD** name resources as plural - this applies to `GET` requests against both an individual resource and the associated resource collection e.g. `GET /accounts/{accountId}` and `GET /accounts` |
| PTH-003 | **SHOULD NOT** use verbs or actions in the path, rather use resources where possible |
| PAR-044 | Where a verb or action is used in the path it **SHOULD** use a present tense verb e.g. `/accounts/{accountId}/activate` |
| PAR-045 | **MUST** not use Personally Identifiable Information (PII) in the path |
| RES-001<br>RES-002 | **MUST** name resources in *kebab case* using the characters a-z, 0-9 and hyphens e.g. a **deposit products** resource should be mapped to `/deposit-products` rather than  `/deposit_products` or `/depositProducts` or `/DepositProducts` |
| RES-001<br>RES-002 | **MUST NOT** use characters that require URL encoding e.g. spaces |
| RES-001<br>RES-002 | **MUST NOT** use an underscore character |
| RES-005 | **MUST** ensure the path has a maximum of 6 segments e.g.: `/resource1/{resource1Id}/resource2/{resource2Id}/resource3/{resource3Id}` |
| PTH-005 | **MUST** enter a description against paths |
| PTH-001 | **MUST** only use characters in the ASCII character set for all descriptions |
| PTH-004 | **SHOULD** not have the text ‘todo’ or ‘tbd’ in descriptions |
| PTH-006 | **MUST** enter an operationId - the recommended format is `<method>-<resource>-<subresource>` e.g. get-accounts-statements`  |
| PTH-007 | **MUST** ensure the operationId is 100 characters or less in length |
| PTH-011 | **MUST** only use the following characters for the operationId: a-z A-Z 0-9 or hyphen or underscore |
| PTH-009 | **SHOULD** enter a summary for each path  |
| PTH-012 | **MUST** ensure the path summary is 200 characters or less in length |


## CRUD - Create Read Update Delete

REST is a resource oriented pattern where a business object is represented as a resource that is addressable by a URL. 

Specific urls are used to manage the CRUD life cycle of a resource where the operation and path define a standard mechanism for creating, reading, updating, and deleting a resource. The standard set of CRUD operations is shown in the following table:

| Operation  | Path                    | Purpose     |
|:----------:|:------------------------|------------ |
| POST       | /resources              | Create a single instance of a resource, should return a resourceId |
| GET        | /resources              | Read a collection of resources. Can be filtered |
| GET        | /resources/{resourceId} | Read details of a unique resource |
| PUT        | /resources/{resourceId} | Update the resource |
| PATCH      | /resources/{resourceId} | Update part of the resource |
| DELETE     | /resources/{resourceId} | Delete the resource |


A complete CRUD sample API document is provided as a template here [CRUD Sample](https://github.com/fusionfabric/open-api-standard/blob/main/pagination-sample.yml). To see the sample API, copy the raw API into the [Swagger editor](https://editor.swagger.io/).

The remainder of this section describes each of the CRUD operations.

### Create a Resource

The following HTTP operation and path are used to create a single resource in a system: 

- `POST /resources/`

The resource definition is contained in the `POST` payload although the payload may be empty for specific use cases.

The `POST` path does not contain a resource identifier (`resourceId`) because the system receiving the resource definition generates a `resourceId` for the created object. 

There are use cases where the `resourceId` is provided as an externally generated resource identifier in the resource payload of the `POST` operation. This approach is not a recommended pattern because RESTful HTTP APIs should be unaware of the client and a mechanism must be introduced to ensure that different clients provide unique resource identifiers.

The following lists the standards when creating a resource:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| RSP-001 | `POST` operations used to create a resource **MUST** return 201 or 202 for a successful response |
| POS-001 | `POST` operations used to create a resource **MUST** return the resource identifier in the response payload |
| POS-002 | `POST` operations used to create a resource **MAY** return resource details and calculated fields in the response payload  |
| PPM-006 | When the creation of a resource is business critical, e.g. for payments, the create operation **SHOULD** implement the [idempotency pattern](https://fusionfabric.github.io/open-api-standard/Idempotency.html)) |
| POS-003 | `POST` operations used to create a resource **MAY** return an `ETag` header |


### Read Resources

There are two REST paths to define read operations in a CRUD model:

| Operation  | Path                    | Purpose     |
|:----------:|:------------------------|------------ |
| GET        | /resources              | Read a collection of resources. Can be filtered |
| GET        | /resources/{resourceId} | Read details of a unique resource |

#### Read a Resource Collection 

The following HTTP operation and path are used to read (or list) all resources in a system: 

- `GET /resources/`

This operation will return an array of objects wrapped up within an object as shown in the following example. 

The array name must be called either `items` or the plural resource name e.g. `accounts` for `GET /accounts`.   

```
{
  "items": [   # or accounts
    {
      "accountId": "0001123467001",
      "name": "Current GBP",
      "uri": "/accounts/0001123467001"
    },
    {
      "accountId": "0001123467002",
      "name": "Current EUR",
      "uri": "/accounts/0001123467002"
    },
    {
      "accountId": "0001123467003",
      "name": "Current USD",
      "uri": "/accounts/0001123467003"
    }
  ]
}
```

Each resource object within a collection can contain either the resource identifier, a partial view of the resource, or the complete resource. 

When the number of resources returned in the collection is large, pagination and filtering should be implemented - for more information see [Collections and Pagination](https://fusionfabric.github.io/open-api-standard/Collections_and_Pagination.html)

When no resources are returned in a collection, either because the filtering applied is too restrictive or because no resources exist then the response must return a `200` with an empty array, a `404` response is not appropriate.  

A `GET` request only allows path and query parameters which will be visible in the url, hence, searching personally identifiable information (PII) such as a social security number using `GET` is usually prohibited by the Security Officer. In these cases a `POST` can be used with the PII data passed in the payload body e.g. 'POST /resources/search' {"ssn": "122223"}`.

The following lists the standards when reading a resource collection:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| RSP-001 | `GET` operations used to read a resource collection **MUST** return 200 for a successful response |
| REB-003 | `GET` operations using a filter MUST NOT return 404 if there are no resources, rather a 200 must be returned |
| PAG-001 | When a query parameter is considered as classified data (personally identifiable information - PII), POST SHOULD be used instead of GET so that the query parameters are contained in the POST body rather than the url path |
| PAG-002 | The array name must be called either `items` or the plural resource name e.g. `accounts` for `GET /accounts` |


#### Read a Single Resource

The following HTTP operation and path are used to read a single resource in a system: 

- `GET /resources/{resourceId}`

The following lists the standards when reading a single resource:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| RSP-010 | `GET` operations used to read a single resource **MUST** return a `200` with the content of the resource |
| REA-001 | `GET` operations used to read a single resource **MUST** return a  a `404` if the resource is not accessible  |
| REA-002 | `GET` operations used to read a single resource **MUST** return a `403` if the resource exists but user is not allowed to access it it |
| PPM-005<br>RSP-016 | `GET` operations used to read a single resource **SHOULD** return an `ETag` header if an update or delete operation is available in the resource API - for more information [Concurrency](https://fusionfabric.github.io/open-api-standard/Concurrency.html) |

### Update a Single Resource 

Once a resource is created it may be useful to update it, for example, to add or remove additional optional field details or to change mandatory fields such as `status`.

The following HTTP operation and path are used to update a single resource in a system: 

- `PUT /resources/{resourceId}`   - to update the entire resource
- `PATCH /resources/{resourceId}` - to update a part of a resource

For further information on PATCH see [this section](https://fusionfabric.github.io/open-api-standard/Introduction_to_REST.html#patch).

When updating a resource full consideration must be given to concurrency issues - for further information see [this section](https://fusionfabric.github.io/open-api-standard/Concurrency.html).

Note that read only fields cannot be updated, for example, the `resourceId` is immutable.  

The following lists the standards when updating a resource:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| RSP-002 | `PUT` or `PATCH` operations MUST return 200 or 204 for a successful response |
| RSP-009 |	`PUT` or `PATCH` operations SHOULD support 412 and 428 responses for concurrency purposes |
| RSP-013 |	`PUT` or `PATCH` operations SHOULD contain an identifier e.g. PUT /resource/{resourceId} or PUT /resource/{resourceId}/status |
| RSP-002 | The client **MUST** set the `If-Match` header with the `ETag` value given provided by the corresponding `GET` or `POST` operation |
| RSP-002 | The server **MUST** return back an E-TAG with the a new value if the update succeed |


### Delete a Single Resource

The following HTTP operation and path are used to delete a single resource in a system: 

- `DELETE /resources/{resourceId}`

`DELETE` can be used to delete, or cancel, or close a resource or a session. Once a `DELETE` has taken place, a subsequent `GET` with the given resource identifier should return a `404`.

When deleting a resource full consideration must be given to concurrency issues - for further information see [this section](https://fusionfabric.github.io/open-api-standard/Concurrency.html).

The following lists the standards when updating a resource:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| RSP-011 | DELETE operations **MUST** return 200 or 202 or 204 for a successful response |


### Resource Collection Operations  

The only operation that handles resource collections in a standard CRUD model is `GET /resources`. 

There is no standard pattern for creating, updating or deleting resource collections using a single client HTTP call and it is recommended that additional endpoints beyond `GET` for supporting resource collections are not provided because the update of multiple resources is not typically an atomic operation and error management becomes complex when, for example, most resources are updated but some resources failed to update.

The following lists the standards for handling resource collections:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| MRS-001 | Supporting Read a resource collection **SHOULD** be supported |
| MRS-002 | Supporting Create Update Deletion on a resource collection **SHOULD** be avoided |
| MRS-003 | Where support for Create Update Deletion on a resource collection is required the path *SHOULD** use `bulk` as an action keyword |
