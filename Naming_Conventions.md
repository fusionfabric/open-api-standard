---
layout: default
title: Naming Conventions
nav_order: 8
---

# Naming Conventions
{: .no_toc}

This section details the naming conventions and standards associated with Finastra APIs.

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
-   be defined as a plural noun - for example, when defining an API for an `Account` resource the
convention is to use `accounts` rather than `account` for all HTTP operations defined within the API. This aligns with common industry convention. 

Example:
```
paths:
  /accounts:
    "GET"   < return a list
    "POST"  > create a single entity (no matter if the resources is plural)
```

## Finastra Standards for Resource Names

>   **MUST** define *useful* resources - see the *Finastra Open API
    Principles* section

>   **MUST** use Finastra common business object names where available

>   **MUST** name resources with nouns

>   **SHOULD** name resources as plural - this applies to `GET` requests against both an individual resource and the associated resource collection e.g. `GET /accounts/{accountId}` and `GET /accounts`

>   **SHOULD** use a noun as the resource name

>   **SHOULD NOT** use verbs or actions in the path - use resources

>   **MUST** name resources in *kebab case* using the characters a-z, 0-9 and hyphens
    e.g.`/shipment-orders` e.g. a **Big Car** resource should be mapped to
> `/big-car` rather than `/BigCar` or `/big_car` or `/Big-Car`

>   **MUST NOT** use characters that require URL encoding e.g. spaces

>   **MUST NOT** use an underscore character

>   **MUST** use lowercase separate words with hyphens for all path
    segments e.g. `/shipment-orders/{shipment-order-id}`


### URI for Dedicated Action

Use cases may arise where the HTTP method is not adequate to perform an
action on the resource.

In these cases an action, named a *resource controller*, **MAY** be
used against a resource, for example:

-   `POST /accounts/{accountId}/transactions/{transId}/confirm` would
    confirm an existing transaction but the same result can be achieved
    with:

-   `POST /accounts/{accountId}/transactions/{transId}/confirmation`
    that would create a confirmation.

> Note that the *resource controller* approach is **NOT** recommended
> and alternative designs should be explored

Finastra API Standards:

> CRUD operations against a resource **SHOULD** use standard HTTP verbs

> Actions and commands **MAY** use a present tense verb

### Resource Identifier

> Resource identifiers **SHOULD** be defined within the path and not as
> a the path parameter

For example:

-   `GET /accounts/{identifier}` MUST be used to identify the resource
    to be retrieved

-   `GET /accounts?id={identifier}` can be used to retrieve a resource
    but only as a search filter, hence, it should be avoided

> **SHOULD** not expose implementation details inside the
> `key/identifier` path e.g. incremental identifiers may provide
> guessable identifiers

In some cases the resource identifier represents the concatenation of
one or more fields of the resource. In these cases a composite key
**MUST** use hyphen as a separator, for example:
```json
{
    key1 : ABC
    key2 : 123
    key3 : A
    ...
}
```
is addressable as `GET /resource/ABC-123-A`.

> It is recommended that an alternative approach is adopted if possible. In any case the combination of keys **MUST** not provide Personally Identifiable Information (PII).

### Child Resources

Child resources **MAY** be used when the child resource has a life
cycle related to the parent resource, for example: `/accounts/{id}/transactions`

The number of nested child resources **MUST** be limited to a maximum of
three to reduce the complexity of the API



### Field name, query parameter name 

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

>   **MUST NOT** use terms that relate to a specific geography e.g. use
    the agnostic term `alternateAccountId` rather than `micr`

>   Abbreviations (e.g. txn) **MUST NOT** be used, to avoid ambiguity. Commonly used well known
abbreviations still **SHOULD** be used e.g. URL, IBAN, SEPA etc.


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


### Field type and format 

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

> for query parameter when type is `Array' 

### Date & Time

Date and time are defined by the OAS2 specification as [RFC3339](https://datatracker.ietf.org/doc/html/rfc3339). Both OAS2 and OAS3 define date and time as a JSON string with a format defined by the JSON schema [format extension](https://datatracker.ietf.org/doc/html/draft-bhutton-json-schema-validation-00#section-7.3).

The following table shows the mapping between the RFC and the JSON schema format extension. Note that `time` is not defined in the OAS2 specification but is accepted as an extension 

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

### Fields values

>   **MUST** adhere to standards for common business objects
    e.g. country - ISO 3166, currency - ISO 4217, amounts, etc.

