---
layout: default
title: Fields and Parameters
nav_order: 8
---

# Fields and Parameters
{: .no_toc}

This section provides details on fields and parameters and the associated Finastra naming conventions and standards.

- TOC
{:toc}

## Field and Parameter Definitions

Fields are defined in OAS2 using the [definitions object](https://github.com/OAI/OpenAPI-Specification/blob/main/versions/2.0.md#definitionsObject) - an example is shown below where the name of the field is `partyId`. At OAS3 fields are defined in a similar manner.
```
definitions:
  partyId:
    description: The unique ID that identifies the Party.
    type: string
    minLength: 1
    maxLength: 10
    example: "PTY-005432"
```

Parameters are defined either against an operation in the [paths object](https://github.com/OAI/OpenAPI-Specification/blob/main/versions/2.0.md#pathsObject) as per the OAS2 example below and they can also be defined as a [parameter definitions object](https://github.com/OAI/OpenAPI-Specification/blob/main/versions/2.0.md#parametersDefinitionsObject). At OAS3 parameters are defined in a similar manner. 
```
parameters:
  - in: body
      name: Party Payload
      description: Party Payload.
      required: true
      schema:
        $ref: '#/definitions/Party'
```
## Finastra Field and Parameter Naming Conventions

The following lists the Finastra standards for field and parameter names:

>   **MUST** align with the accepted business domain terminology

>   **MUST** be named in a clear, concise and unambiguous manner.

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


>  **SHOULD** have a maxLength for string data types

>   **MUST NOT** specify a default for a required parameter
 
>   **SHOULD** use empty string to remove a field's value

>   **SHOULD NOT** use `allowEmptyValue`


## Finastra Field and Parameter Type and Format STandards

The following lists the Finastra standards for field types and formats:

>   **MUST** ensure that `number` and `integer types` have an associated
    `format`

>   **MUST NOT** define binary content as string

>  **SHOULD** use string type and date or date-time format for date and/or time fields

>  Enumerations **SHOULD** be used to define a closed set of allowed field values
e.g. the following shows a set of possible values for an item’s status:
`PENDING`, `APPROVED`, `COMPLETE`.

>  **SHOULD** ensure that enumerations are lowercase a-z and/or upper
    case A-Z with hyphens are permitted to separate words

>  Enumeration **MUST NOT** include spaces or special characters e.g. underscores

>  Boolean fields **MUST** be unambiguous hence `isCurrency` is preferred to `isCurrencyorCountry`.


## Date & Time Fields

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

