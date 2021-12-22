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

## Paths

A REST API's path consists of an HTTP method, a base path and one or more resources and identifiers.

This section is concerned with the resources and identifiers.

A resource is a concrete concept related to the business domain e.g. `accounts`.

An identifier is the unique value or key that is used to identify a resource.

Business domain resources are manipulated using HTTP methods as
required by the business and resources should be addressable via a URI that is 
compatible with [RFC1035](https://tools.ietf.org/html/rfc1035).

The following is an example of a set of CRUD endpoints against an `accounts` resource with an identifier of `{id}`:
```
    POST     /accounts           < - - create a single account
    GET      /accounts           < - - return a list of accounts
    GET      /accounts/{id}      < - - return a single account
    PUT      /accounts/{id}      < - - update a single account
    DELETE   /accounts/{id}      < - - delete a single account
```

## Defining Resources and Identifiers

This section provides guidelines on defining resources and identifiers.

### Be Consistent

Resource names **MUST** be defined consistently both within an API for each `GET`, `POST`, `PUT`, `DELETE` method
 and across APIs.

When defining Finastra APIs and paths, it is recommended that the name of the API is the same as the principal resource within the API, for example, an account API should be named Accounts and have at least one endpoint using a `/accounts` resource. 

API designers SHOULD avoid prefixing the `/accounts` resource in the Accounts API with parent resources e.g. **avoid** `/customer/{customerId}/accounts` unless necessary

### Prefer Single Root Resource

In most scenarios the following paths which use a resource at root level can be used. This approach is used when the resource is not a sub-resource and the identifier is unique for all instances of the resource:
- `POST /resource`
- `GET /resource`
- `GET /resource/{id}`
- `PUT /resource/{id}`
- `DELETE /resource/{id}`

The following Finastra rule applies:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PAR-001 | Using a single root resource with a unique identifier in the path **SHOULD** be adopted – it is the preferred approach, for example, use: `GET /resource/{id}` rather than: `GET /parent-resource/{parentId}/resource/{id}` for a resource with a unique identifier across all resource instances |

 
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
- `GET /resource/{id}`
rather than
- `GET /resource1/{1id}/resource2/{2id}/resource3/{3id}`

The following rules apply:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PAR-030 | When using compound keys it is expected that the external developer using the API contract **SHOULD NOT** need to know how the compound key is constructed i.e. they would only be aware of the unique identifier not its individual components |
| PAR-031 | The API contract **SHOULD NOT** leak implementation details of the compound keys |
| PAR-032 | When using compound keys the implementation **SHOULD** take care to ensure that conflicts with the compound key identifier and any compound key values in the body of a request are handled appropriately and consistently e.g. so that the constituent parts of a compound key are not modified by a `PUT` operation |
| PAR-033 | Compound keys with delimeters such as a slash or hyphen **SHOULD NOT** be used to define individual compound key identifiers because they are not easily extensible if the compound key implementation differs between providers of the API e.g. one provider might use two identifiers to ensure uniqueness of the compound key whereas another provider might require three identifiers to ensure uniqueness of the compound key: <br>- MUST NOT use - `GET /resource/{parentId}/{id}` <br>- MUST NOT use - `GET /resource/{parentId}-{id}` |
| PAR-034 | Query parameters **SHOULD NOT** be used to define individual compound key identifiers:<br> - MUST NOT use – GET /resource/{parentId}?id={id} |

### Defining Resource Actions

Use cases may arise where the HTTP method is not adequate to perform an
action on a resource.

In these cases an action, named a *resource controller*, may be
used against a resource, for example:

-   `POST /accounts/{accountId}/transactions/{transId}/confirm` would
    confirm an existing transaction

Note that the *resource controller* approach is **NOT** recommended
and alternative designs should be explored, for example, the same result can be achieved with:
`POST /accounts/{accountId}/transactions/{transId}/confirmation` with a payload of `{"confirmation-status" : "complete" }` 

### Finastra Standards for Paths and Resources

The following lists the Finastra standards for paths and resources:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PAR-040 | **MUST** define *useful* resources - see the *Finastra Open API Principles* section |
| PAR-041 | **MUST** use Finastra common business object names where available |
| PAR-042 | **MUST** name resources with nouns |
| PAR-043 | **SHOULD** name resources as plural - this applies to `GET` requests against both an individual resource and the associated resource collection e.g. `GET /accounts/{accountId}` and `GET /accounts` |
| PTH-003 | **SHOULD NOT** use verbs or actions in the path, rather use resources where possible |
| PAR-044 | Where a verb or action is used in the path it **SHOULD** use a present tense verb e.g. `/accounts/{id}/activate` |
| RES-001<br>RES-002 | **MUST** name resources in *kebab case* using the characters a-z, 0-9 and hyphens e.g. a **deposit products** resource should be mapped to `/deposit-products` rather than  `/deposit_products` or `/depositProducts` or `/DepositProducts` |
| RES-001<br>RES-002 | **MUST NOT** use characters that require URL encoding e.g. spaces |
| RES-001<br>RES-002 | **MUST NOT** use an underscore character |
| PAR-045 | **MUST** not use Personally Identifiable Information (PII) in the path |
