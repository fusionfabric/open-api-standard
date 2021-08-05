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

## Paths and Resources

A key principle of REST involves separating APIs into logical
resources that are aligned with the business domain.

A business domain resource must be a concrete concept e.g. `accounts` and 
must have a unique identifier and URI.

Business domain resources are manipulated using HTTP methods as
required by the business and resources should be addressable via a URI that is 
DNS compatible with [RFC1035](https://tools.ietf.org/html/rfc1035).

The following is a pseudo-OAS example of a set of CRUD endpoints against an `accounts` resource:
```
paths:
  /accounts:
    POST          < - - create a single account
    GET           < - - return a list of accounts
    GET/{id}      < - - return a single account
    PUT/{id}      < - - update a single account
    DELETE/{id}   < - - delete a single account
```

## Defining Resources and Identifiers

This section provides guidelines on defining resources and identifiers.

### Be Consistent

Resource names **MUST** be defined consistently both within an API for each `GET`, `POST`, `PUT`, `DELETE` method
 and across APIs.

When defining Finastra APIs and paths, it is recommended that the name of the API is the same as the principal resource within the API, for example, an account API should be named Accounts and have at least one endpoint using a `/accounts` resource. API designers SHOULD avoid prefixing the `/accounts` resource in the Accounts API with parent resources e.g. **avoid** `/customer/{customerId}/accounts` unless necessary

### Prefer Single Root Resource

In most scenarios the following paths which use a resource at root level can be used. This approach is used when the resource is not a sub-resource and the identifier is unique for all instances of the resource:
- `POST /resource`
- `GET /resource/{id}`
- `PUT /resource/{id}`
- `DELETE /resource/{id}`

> using a single root resource with a unique identifier in the path **SHOULD** be adopted – it is the preferred approach, for example, use: `GET /resource/{id}` rather than: `GET /parent-resource/{parentId}/resource/{id}` for a resource with a unique identifier across all resource instances

 
### Defining Sub-Resources

There are scenarios where multiple path segments and sub-resources can be used.

Sub-resources can be used when they are clearly associated with the parent resource, for example:
- `GET /clients/{clientId}/ratings/{ratingId}`
is preferred because it provides context to the ratings resource and is more easily understood, unlike the following:
- `GET /ratings/{ratingId}`

> sub-resources **MAY** be used when the sub-resource has a life
cycle related to the parent resource, for example: `clients/{clientId}/ratings/{ratingId}`

> when using sub-resources, where possible, the number of path segments **SHOULD** be limited to four to reduce the complexity of the API

> when using sub-resources, be aware of the API context, for example, the following endpoint **MAY** be defined in a customer API BUT it **SHOULD NOT** be used in an account API: `GET /clients/{clientId}/accounts/{accountId}`

> when using sub-resources, endpoints **MUST** be unambiguous, for example, the sub-resource endpoints should not be provided as both a root resource endpoint e.g. `GET /accounts/{accountId}` and a sub-resource endpoint e.g. `GET /clients/{clientId}/accounts/{accountId}`


### Defining Resources with Non-Unique Identifiers

When a resource identifier is NOT unique across all instances of a resource then there are the following options for defining the path:

**Sub-resources** can be used, for example, the following path handles scenarios where the `accountId` is not unique across all accounts, but is unique for a given `clientId`:
- `GET /clients/{clientId}/accounts/{accountId}`
note that the following path would not support the same scenario because `accountId` is not unique across all accounts: `GET accounts/{accountId}`

> when using sub-resources to handle resources with non-unique identifiers the number of path segments SHOULD not exceed four – see the compound keys section for further details 

> when using sub-resources to handle resources with non-unique identifiers the relationship, e.g. 1 to many, between the parent resource and the sub-resource SHOULD be unchanging otherwise the endpoint may need to change

 **Compound keys** can be used when more than four path segments would be needed, for example, use:
- `GET /resource/{id}`
rather than
- `GET /resource1/{1id}/resource2/{2id}/resource3/{3id}`

> when using compound keys it is expected that the external developer using the API contract **SHOULD NOT** need to know how the compound key is constructed i.e. they would only be aware of the unique identifier not its individual components

> the API contract **SHOULD NOT** leak implementation details of the compound keys 

> when using compound keys the implementation **SHOULD** take care to ensure that conflicts with the compound key identifier and any compound key values in the body of a request are handled appropriately and consistently e.g. so that the constituent parts of a compound key are not modified by a PUT operation

> compound keys with delimeters such as a slash or hyphen **SHOULD NOT** be used to define individual compound key identifiers because they are not easily extensible if the compound key implementation differs between providers of the API e.g. one provider might use two identifiers to ensure uniqueness of the compound key whereas another provider might require three identifiers to ensure uniqueness of the compound key:
- MUST NOT use - `GET /resource/{parentId}/{id}`
- MUST NOT use - `GET /resource/{parentId}-{id}`

> query parameters **SHOULD NOT** be used to define individual compound key identifiers:
- MUST NOT use – GET /resource/{parentId}?id={id}

### Defining Resource Actions

Use cases may arise where the HTTP method is not adequate to perform an
action on a resource.

In these cases an action, named a *resource controller*, **MAY** be
used against a resource, for example:

-   `POST /accounts/{accountId}/transactions/{transId}/confirm` would
    confirm an existing transaction

Note that the same result can be achieved with:

-   `POST /accounts/{accountId}/transactions/{transId}/confirmation`
    with a payload of `{"confirmation-status" : "complete" }` that would create a confirmation.

> Note that the *resource controller* approach is **NOT** recommended
> and alternative designs should be explored


### Finastra Standards for Paths and Resources

The following lists the Finastra standards for paths and resources:

> **MUST** define *useful* resources - see the *Finastra Open API
    Principles* section

> **MUST** use Finastra common business object names where available

> **MUST** name resources with nouns

> **SHOULD** name resources as plural - this applies to `GET` requests against both an individual resource and the associated resource collection e.g. `GET /accounts/{accountId}` and `GET /accounts`

> **SHOULD NOT** use verbs or actions in the path, rather use resources where possible.
If a verb is used it should be in the present tense

> Where a verb or action is used in the path it **SHOULD** use a present tense verb e.g. `/accounts/{id}/activate`

> **MUST** name resources in *kebab case* using the characters a-z, 0-9 and hyphens
    e.g. a **deposit products** resource should be mapped to `/deposit-products` rather than
 `/deposit_products` or `/depositProducts` or `/DepositProducts`

> **MUST NOT** use characters that require URL encoding e.g. spaces

> **MUST NOT** use an underscore character

> **MUST** not provide Personally Identifiable Information (PII) in the path
