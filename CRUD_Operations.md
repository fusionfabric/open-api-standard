---
layout: default
title: CRUD Operations 
nav_order: 7
---

# CRUD - Create Read Update Delete - Operations
{: .no_toc}

This section provides details on Create Read Update Delete (CRUD) operations on resources.

- TOC
{:toc}

## Introduction

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

## Create a Resource

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


## Read Resources

There are two REST paths to define read operations in a CRUD model:

| Operation  | Path                    | Purpose     |
|:----------:|:------------------------|------------ |
| GET        | /resources              | Read a collection of resources. Can be filtered |
| GET        | /resources/{resourceId} | Read details of a unique resource |

### Read a Resource Collection 

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


### Read a Single Resource

The following HTTP operation and path are used to read a single resource in a system: 

- `GET /resources/{resourceId}`

The following lists the standards when reading a single resource:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| RSP-010 | `GET` operations used to read a single resource **MUST** return a `200` with the content of the resource |
| REA-001 | `GET` operations used to read a single resource **MUST** return a  a `404` if the resource is not accessible  |
| REA-002 | `GET` operations used to read a single resource **MUST** return a `403` if the resource exists but user is not allowed to access it it |
| PPM-005<br>RSP-016 | `GET` operations used to read a single resource **SHOULD** return an `ETag` header if an update or delete operation is available in the resource API - for more information [Concurrency](https://fusionfabric.github.io/open-api-standard/Concurrency.html) |

## Update a Single Resource 

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


## Delete a Single Resource

The following HTTP operation and path are used to delete a single resource in a system: 

- `DELETE /resources/{resourceId}`

`DELETE` can be used to delete, or cancel, or close a resource or a session. Once a `DELETE` has taken place, a subsequent `GET` with the given resource identifier should return a `404`.

When deleting a resource full consideration must be given to concurrency issues - for further information see [this section](https://fusionfabric.github.io/open-api-standard/Concurrency.html).

The following lists the standards when updating a resource:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| RSP-011 | DELETE operations **MUST** return 200 or 202 or 204 for a successful response |


## Resource Collection Operations  

The only operation that handles resource collections in a standard CRUD model is `GET /resources`. 

There is no standard pattern for creating, updating or deleting resource collections using a single client HTTP call and it is recommended that additional endpoints beyond `GET` for supporting resource collections are not provided because the update of multiple resources is not typically an atomic operation and error management becomes complex when, for example, most resources are updated but some resources failed to update.

The following lists the standards for handling resource collections:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| MRS-001 | Supporting Read a resource collection **SHOULD** be supported |
| MRS-002 | Supporting Create Update Deletion on a resource collection **SHOULD** be avoided |
| MRS-003 | Where support for Create Update Deletion on a resource collection is required the path **SHOULD** use `bulk` as an action keyword |
