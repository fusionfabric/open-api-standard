---
layout: default
title: Paths and Resources
nav_order: 7
---

# Paths and Resources
{: .no_toc}

This section provides details on resources and fields and provides the associated naming conventions and standards.

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

```
POST     /accounts                  < - - create a single account
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
| SCM-002 | Open APIs **MUST** use HTTPS and TLS i.e. RFC2660 and RFC2818 are enforced |
| SCM-003 | Open APIs **SHOULD NOT** define `host` or `basepath`  or `servers` and `url` values because these values are defined in external configuration files |


## Resources

This section provides guidelines on defining resources and identifiers.

### Be Consistent

Resource names **MUST** be defined consistently both within an API for each `GET`, `POST`, `PUT`, `DELETE` method and across APIs.

When defining APIs and paths, it is recommended that the name of the API is the same as the principal resource within the API, for example, an account API should be named Accounts and have at least one endpoint using a `/accounts` resource. 

API designers SHOULD avoid prefixing the `/accounts` resource in the Accounts API with parent resources e.g. **avoid** `/customer/{customerId}/accounts` unless necessary

### Prefer Single Root Resource

In most scenarios the following paths which use a resource at root level can be used. This approach is used when the resource is not a sub-resource and the identifier is unique for all instances of the resource:
- `POST /resources`
- `GET /resources`
- `GET /resources/{resourceId}`
- `PUT /resources/{resourceId}`
- `DELETE /resources/{resourceId}`

The following rule applies:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PAR-001 | Using a single root resource with a unique identifier in the path **SHOULD** be adopted – it is the preferred approach, for example, use: `GET /resource/{resourceId}` rather than: `GET /parent-resource/{parentId}/resource/{resourceId}` for a resource with a unique identifier across all resource instances |

 
### Defining Sub-Resources

There are scenarios where multiple path segments and sub-resources can be used.

Sub-resources can be used when they are related to and dependant on the associated parent resource, for example:
 `GET /clients/{clientId}/ratings/{ratingId}`
is used rather than `GET /ratings/{ratingId}` when a `rating` cannot be created in isolation. 

If the `rating` resource is not dependant on the `client` resource then `GET /ratings/{ratingId}` should be used.

The following rule apply:

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
| PAR-033 | Compound keys with delimiters such as a slash or hyphen **SHOULD NOT** be used to define individual compound key identifiers because they are not easily extensible if the compound key implementation differs between providers of the API e.g. one provider might use two identifiers to ensure uniqueness of the compound key whereas another provider might require three identifiers to ensure uniqueness of the compound key: <br>- MUST NOT use - `GET /resources/{parentId}/{resourceId}` <br>- MUST NOT use - `GET /resources/{parentId}-{resourceId}` |
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

## Resource Identifiers 

An identifier is the unique value or key that is used to identify a resource, for example: 

```
GET /accounts/{accountId}
{ 
    accountId = "2c76539b-c793-41aa-8082-87621b4e574b"
}
```
The following rules apply to resource identifiers:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PAR-035 | Resource identifiers **MUST** be a unique technical identifier of a resource and should be a string|
| PAR-036 | Resource identifiers **SHOULD** be named as `<resource-name>Id` where `<resource-name>` is the name of the resource e.g. the resource identifier for a resource named `accounts` will be `accountId` |
| DEF-027<br>IDS-001 | Resource identifiers **SHOULD NOT** be named `{id}` or `{Identifier}` |
| PAR-037 | Resource identifiers **MUST NOT** use personally identifiable information (PII) for the resource identifier |
| PAR-038 | Resource identifiers **SHOULD NOT** be suffixed with `Number` because the suffix `Number` is typically associated with fields containing personally identifiable information (PII) e.g. `accountNumber`, `customerNumber`, `taxIdentificationNumber` |
| PAR-039 | The resource payload **SHOULD** contain a *read only* field named `<resource-name>Id` |


## Standards for Paths and Resources

The following lists the standards for paths and resources:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PAR-040 | **MUST** define *useful* resources - see the *Finastra Open API Principles* section |
| PAR-041 | **MUST** use common business object names where available |
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
