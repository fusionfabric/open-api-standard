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
  partyId:
    name: partyId
    in: path
    required: true
    type: string
    minLength: 1
    maxLength: 10
    description: The unique ID that identifies the Party.
```
## Objects and Arrays

Fields can be nested within objects (or arrays) as per the following OAS2 example.

Note that whilst Finastra APIs support multiple nested fields, the use of `$ref` is mandated so that any individual structure does not contain more than two levels.
```
  nestedObject1:
    description: Nested object
    type: object
    properties:
      nestedlevel2:
        $ref: '#/definitions/nestedObject2'
  nestedObject2:
    type: object
    description: Nested object level 2
    properties:
      nestedlevel3a:
        type: string
        description: Nested field level 3
      nestedlevel3b:
        type: string
        description: Nested field level 3
```

## Finastra Field and Parameter Naming Conventions

The following lists the Finastra standards for field and parameter names:

>   **MUST** align with the accepted business domain terminology

>   **MUST** be named in a clear, concise and unambiguous manner.

>   **MUST** be meaningful by obviously declaring the field's purpose; a
    simple guideline is to ensure that Google shows relevant links on
    the first returned page

>  **MUST** define query parameter names and field names in lower `camelCase` using only the characters: 
    `a-z` and `0-9`, for example:
> -   valid: `inputDate`
> -   invalid: `InputDate`, `Input_Date`

>   **MUST** adhere to the Finastra Open API data dictionary where
    possible e.g. `country`; where further clarity is required a prefix
    can be used e.g. `taxCountry`

>   **SHOULD NOT** use word abbreviations e.g. use `transaction` rather than `txn` unless
the abbreviation is commonly used

>   **MAY** use commonly used abbreviations or acronyms e.g. `memo`, `email`, `uri`, `IBAN`, `SEPA`, etc.

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
    
>   **SHOULD** define the resource's unique identifier with a UUID `{id}`

>   **MUST** list parameters in a specific order: required, followed by
    optional
        
>   **MUST** ensure that array names are plural 
    
>   **SHOULD** specify relevant defaults where applicable

> **SHOULD** be as precise as possible with field and parameter definitions

>  **MUST** have `maxLength` for `string` data types

>  **SHOULD** have `minLength` and `pattern` for `string` data types

>   **MUST NOT** specify a default for a required parameter
 
>   **SHOULD** use empty string to remove a field's value

>   **SHOULD NOT** use `allowEmptyValue`


## Finastra Field and Parameter Type and Format Standards

The following lists the Finastra standards for field types and formats:

>   **MUST** ensure that `number` and `integer types` have an associated
    `format`

>   **MUST NOT** define binary content as string

>  **SHOULD** use a `string` data type and a `date` or `date-time` format for date and/or time fields

>  **SHOULD** define enumerations as a **closed** set of allowed field values
e.g. the following shows a set of possible values for an item's status:
`PENDING`, `APPROVED`, `COMPLETE`.

>  **SHOULD** ensure that enumerations are lowercase a-z and/or upper
    case A-Z with hyphens, for example: `SPOT-RATE` or `spot-rate` or `Spot-Rate`

>  Enumerations **MUST NOT** include spaces or special characters e.g. underscores

>  Boolean fields **MUST** be unambiguous hence `isCurrency` is preferred to `isCurrencyorCountry`.


## Date & Time Fields

Date and time are defined by the OAS2 specification as [RFC3339](https://datatracker.ietf.org/doc/html/rfc3339) which is a profile of [ISO8601](https://en.wikipedia.org/wiki/ISO_8601). Both OAS2 and OAS3 define date and time as a JSON string with a format defined by the JSON schema [format extension](https://datatracker.ietf.org/doc/html/draft-bhutton-json-schema-validation-00#section-7.3).

The following table shows the mapping between the RFC and the JSON schema format extension. Note that `time` is not defined in the OAS2 specification but is accepted as an extension.

| Format in OAS specification | RFC 3339 | Example  |
|-----------------------------|----------|----------|
|  date                       | full-date|`2010-01-26`       |
|  date-time                  | date-time| `1985-04-12T23:20:50.52Z` or `1996-12-19T16:39:57-08:00` |
|  time                       | full-time| `23:20:50.52Z` or `1996-12-19T16:39:57-08:00`        |
|  partial-time               | partial-time| `23:20:50`  i.e. with no time zone information| 



>  **SHOULD** use a `string` data type and a `date` or `date-time` format for date and/or time fields

>  **MAY** use the the format `time` with UTC format (23:20:50.52Z) not the time zone specification

> **SHOULD** avoid the format `partial-time`

Example : 

```
   fromDate:
    name: fromDate
    in: query
    description: First date of the date range. Date range can be up to 13 months
    required: true
    type: string
    format: date
    example : 2010-01-26
```

## ISO Fields

ISO standards must be used for the following common business objects:

>   **MUST** use ISO 8601/RFC 3339 for dates and times

>   **MUST** use ISO 3166 for countries

>   **MUST** use ISO 4217 for currencies


## Amount Fields

The following is a snippets of an OAS2 definition for an amount field. This definition is recommended rather than mandated.

```
MonetaryAmount:
  type: object
  description: A monetary amount in a given currency supporting an amount format of 18.3 - a decimal point is expected for currencies that support them e.g. 1.00 GBP
  required:
    - amount
    - currency
  properties: 
    amount:
      type: string
      description: tbc
      pattern: '-?[0-9]{1,18}(\.[0-9]{1,3})?'
      example: '-5877.78'
    currency:
      description: ISO 4217 Alpha 3 currency code
      type: string
      pattern: '[A-Z]{3}'
      example: EUR
```

