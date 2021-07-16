---
layout: default
title: Resource and Field Naming Conventions
nav_order: 8
---

# Resource and Field Naming Conventions
{: .no_toc}

This section provides details on resources and fields and provides the associated Finastra naming conventions and standards.

- TOC
{:toc}

## Resources

A key principle of REST involves separating the API into logical
resources that are aligned with the business domain.

Business domain resources are manipulated using HTTP methods as
required by the business and resources should be addressable via a URI that is 
DNS compatible with [RFC1035](https://tools.ietf.org/html/rfc1035).

A business domain resource **MUST**:
-   be a concrete concept e.g. `accounts`
-   have a unique identifier and URI

Example:
```
paths:
  /accounts:
    "GET"   < return a list
    "POST"  > create a single entity (no matter if the resources is plural)
```

### Finastra Standards for Resource Names

The following lists the Finastra standards for resource names:

>   **MUST** define *useful* resources - see the *Finastra Open API
    Principles* section

>   **MUST** use Finastra common business object names where available

>   **MUST** name resources with nouns

>   **SHOULD** name resources as plural - this applies to `GET` requests against both an individual resource and the associated resource collection e.g. `GET /accounts/{accountId}` and `GET /accounts`

>   **SHOULD NOT** use verbs or actions in the path, rather use resources where possible.
If a verb is used it should be in the present tense

>   **MUST** name resources in *kebab case* using the characters a-z, 0-9 and hyphens
    e.g. a **deposit products** resource should be mapped to `/deposit-products` rather than
 `/deposit_products` or `/depositProducts` or `/DepositProducts`

>   **MUST NOT** use characters that require URL encoding e.g. spaces

>   **MUST NOT** use an underscore character

> Actions and commands **MAY** use a present tense verb

> **MUST** not provide Personally Identifiable Information (PII) in the path

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


## Field and Parameters Names

The following lists the Finastra standards for field and parameter naming:

>   **MUST** align with the accepted business domain terminology

>   ** MUST** be named in a clear, concise and unambiguous manner.

>   **MUST** be meaningful by obviously declaring the field's purpose; a
    simple guideline is to ensure that Google shows relevant links on
    the first returned page

>   **MUST** align with the accepted business domain terminology

>   **MUST** adhere to the Finastra Open API data dictionary where
    possible e.g. `country`; where further clarity is required a prefix
    can be used e.g. `taxCountry`

>   **MUST** only use commonly accepted abbreviations

>   **MUST NOT** introduce new terminology where existing equivalents
    are available

>   **MUST NOT** use terms that relate to a specific product / implementation. 

>   **MUST NOT** be technical in nature because Open APIs target
    business rather than technical domains

>   **MUST NOT** use terms that relate to a specific product e.g. use
    the agnostic term `region` rather than `processingArea`

>   **SHOULD NOT** use terms that relate to a specific geography e.g. consider using 
the agnostic term `alternateAccountId` rather than `micr`

>   **MUST NOT** use abbreviations e.g. `txn`, however, commonly used, well known
abbreviations **MAY** be used e.g. `uri`, `IBAN`, `SEPA`, etc.

>  **MUST** define query parameters, field name in lower `camelCase` restricted to
    characters a-z and 0-9

For example:

-   valid: `inputDate`
-   invalid: `InputDate`, `Input_Date`
    
>   **MUST** define the resource's unique identifier with a UUID `{id}`

>   **MUST** list parameters in a specific order: required, followed by
    optional
        
>   **MUST** ensure that array names are plural 
    
>   **SHOULD** specify relevant defaults where applicable

>   **MUST NOT** specify a default for a required parameter
 
>   **SHOULD** use empty string to remove a field's value

>   **SHOULD NOT** use `allowEmptyValue`


## Field Type and Format

The following lists the Finastra standards for field types and formats:

>   **MUST** ensure that `number` and `integer types` have an associated
    `format`

>   **MUST NOT** define binary content as string

>  Enumerations **SHOULD** be used to define a closed set of allowed field values
e.g. the following shows a set of possible values for an item’s status:
`PENDING`, `APPROVED`, `COMPLETE`.

>  **SHOULD** ensure that enumerations are lowercase a-z and/or upper
    case A-Z with hyphens are permitted to separate words

>  Enumeration **MUST NOT** include spaces or special characters e.g. underscores

> Boolean fields **MUST** be unambiguous hence `isCurrency` is preferred to `isCurrencyorCountry`.


## Date & Time

Date and time are defined by the OAS2 specification as [RFC3339](https://datatracker.ietf.org/doc/html/rfc3339). Both OAS2 and OAS3 define date and time as a JSON string with a format defined by the JSON schema [format extension](https://datatracker.ietf.org/doc/html/draft-bhutton-json-schema-validation-00#section-7.3).

The following table shows the mapping between the RFC and the JSON schema format extension. Note that `time` is not defined in the OAS2 specification but is accepted as an extension.

| Format in OAS specification | RFC 3339 | Example  |
|-----------------------------|----------|----------|
|  date                       | full-date|2010-01-26       |
|  time                       | full-time| 23:20:50.52Z or 1996-12-19T16:39:57-08:00        |
|  date-time                  | date-time| 1985-04-12T23:20:50.52Z or 1996-12-19T16:39:57-08:00 |
|  partial-time               | partial-time| 23:20:50  i.e. with no time zone information| 


> When using the format `time`, the UTC format (23:20:50.52Z) and not the time zone specification **SHOULD** be used

> The format `partial-time` **SHOULD** be avoided 

Example : 

```yaml
   fromDate:
    name: fromDate
    in: query
    description: First date of the date range. Date range can be up to 13 months
    required: true
    type: string
    format: date
    example : 2010-01-26
```

## Fields with ISO Values

>   **MUST** adhere to standards for common business objects
    e.g. country - ISO 3166, currency - ISO 4217, amounts, etc.

