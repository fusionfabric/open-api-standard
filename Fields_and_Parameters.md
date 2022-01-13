---
layout: default
title: Fields and Parameters
nav_order: 9
---

# Fields and Parameters
{: .no_toc}

This section provides details on fields and parameters and the associated naming conventions and standards.

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

Fields can be nested within objects (or arrays) as per the following OAS2 example:
```
  object1:
    type: object
    properties:
      object2:
        $ref: '#/definitions/object2'
  object2:
    type: object
    properties:
      string1:
        type: string
      string2:
        type: string
```
Whilst APIs support multiple nested fields, as per the above example, the use of `$ref` is mandated so that any individual structure does not contain more than two levels, hence, the following example would be deemed INVALID and should be refactored as per the above example:
```
  object1:
    type: object
    properties:
      object2:
        type: object
        properties:
          string1:
            type: string
          string2:
            type: string
```
Similarly, when using `allOf`, `anyOf` or `oneOf`, $ref must be used to reference allowed fields, for example, the following code shows both a valid and invalid definition: 

```
# Valid structure       
FieldBlock:
  allOf:
    - $ref: '#/definitions/CommonFields'
    - $ref: '#/definitions/SpecificFields'
    
# Invalid structure - it should use a $ref instead of properties:
FieldBlock:
 allOf:
 - $ref: '#/definitions/CommonFields'
 - properties:
     field1:
       type: number
     field2:
       type: string 
```
The following lists the standards for defining structures:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| DEF-017<br>DEF-018<br>DEF-019 | **MUST** not define nested fields - use a reference field with `$ref` to define the nested fields  |
| PPM-003 | **MUST** not define nested fields for parameter schemas - use a reference field with `$ref` to define the nested fields  |
| DEF-020 | **MUST** use a reference field, `$ref`, with allOf anyOf or oneOf  |

## Field and Parameter Standards

The following lists the standards for field and parameter names:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| FPB-001 | **MUST** align with the accepted business domain terminology |
| FPB-002 | **MUST** be named in a clear, concise and unambiguous manner |
| FPB-003 | **MUST** be meaningful by obviously declaring the field's purpose; a simple guideline is to ensure that Google shows relevant links on the first returned page |
| DEF-002<br>DEF-010<br>DEF-013 | **MUST** define field names in lower `camelCase` using only the characters: `a-z` and `0-9` and hyphen
| IDS-002<br>IDS-003<br>PEF-002 | **MUST** define query and path parameter names in lower `camelCase` using only the characters: `a-z` and `0-9`,<br> for example: - valid: `inputDate` -   invalid: `InputDate`, `Input_Date` |
| DEF-024<br>DEF-025<br>DEF-026<br>PPM-009 | **MUST** adhere to an API data dictionary where possible e.g. `country`; where further clarity is required a prefix can be used e.g. `taxCountry` |
| FPB-005 | **SHOULD NOT** use word abbreviations e.g. use `transaction` rather than `txn` unless the abbreviation is commonly used |
| FPB-006 | **MAY** use commonly used abbreviations or acronyms e.g. `memo`, `email`, `uri`, `IBAN`, `SEPA`, etc. |
| FPB-007 | **MUST** only use commonly accepted abbreviations |
| FPB-008 | **MUST NOT** introduce new terminology where existing equivalents are available |
| FPB-009 | **MUST NOT** use terms that relate to a specific product / implementation |
| FPB-010 | **MUST NOT** be technical in nature because Open APIs target business rather than technical domains |
| FPB-011 | **MUST NOT** use terms that relate to a specific product e.g. use the agnostic term `region` rather than `processingArea` 
| FPB-012 | **SHOULD NOT** use terms that relate to a specific geography e.g. consider using the agnostic term `alternateAccountId` rather than `micr` |
| FPB-013 | **SHOULD** define the resource's unique identifier with a UUID `{id}` |
| DEF-027<br>  IDS-001 | **MUST NOT** define field or parameter names that end in case sensitive 'ID' |
| FPB-014 | **MUST** list parameters in a specific order: required, followed by optional |
| FPB-015 | **MUST** ensure that array names are plural |
| FPB-016 | **SHOULD** ensure that a `GET` request for a collection defines the array name as `items` |
| FPB-017 | **SHOULD** specify relevant defaults where applicable |
| FPB-018 | **SHOULD** be as precise as possible with field and parameter definitions |
| DEF-012<br>PEF-012 | **SHOULD** have `maxLength` for `string` data types |
| DEF-035 | **SHOULD NOT** have a `maxLength` value of 1 for `string` fields - consider using a `boolea`n type instead of `string` |
| FPB-019 | **SHOULD** have `minLength` and `pattern` for `string` data types |
| FPB-020 | **MUST NOT** specify a default for a required parameter |
| FPB-021 | **SHOULD** use empty string to remove a field's value |
| FPB-022 | **SHOULD NOT** use `allowEmptyValue: true` |
| PEF-009<br>DEF-016 | **MUST NOT** define fields or parameters that are not used |
| DEF-004<br>DEF-009 | **MUST** define the keyword `type` against all fields and parameters unless using $ref, allOf, oneOf or anyOf |
| DEF-021<br>DEF-022 | **MUST NOT** define the keyword `type` against all fields when using $ref, allOf, oneOf or anyOf |
| DEF-007<br>PPM-004 | **MUST** define a `description` against each field or parameter |
| INF-003 | **MUST** define a `description` in the `info` section |
| DEF-008 | **MUST** define a `title` against each field or parameter |
| INF-004<br>INF-015 | **MUST** define a `title` that is 200 characters or less in the `info` section |
| DEF-006<br>INF-002<br>PEF-004<br>PEF-013 | **MUST** not have the text 'todo' or 'tbd' in descriptions |
| DEF-029 | **SHOULD** not have the text 'todo' or 'tbd' in *examples* |
| DEF-011<br>DEF-031<br>INF-005<br>INF-006<br>PEF-003<br>PEF-015<br>PTH-002<br>DEF-003<br>DEF-030<br>PEF-001<br>PEF-014 | **MUST** only use characters in the ASCII  character set for all descriptions |
| DEF-015 | **MUST** ensure that fields in the `required` section are defined  |
| PEF-006 | **SHOULD** ensure that the parameter reference matches the parameter name - this is for readability, for example: <br>X-Request-ID:<br>&nbsp;&nbsp;in: header<br>&nbsp;&nbsp;name: X-Request-ID<br>&nbsp;&nbsp;type: string  |
| PPM-010 | **MUST NOT** use a body parameter with a `GET` operation  |


## Field and Parameter Type and Format Standards

The following lists the standards for field types and formats:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| DEF-014<br>PEF-007 | **MUST** ensure that `number` and `integer types` have an associated `format`|
| FPB-022 | **MUST NOT** define binary content as string |
| DEF-028<br>PEF-010 | **SHOULD** use a `string` data type and a `date` or `date-time` format for date and/or time fields |
| FPB-023 | **SHOULD** define enumerations as a **closed** set of allowed field values<br> e.g. the following shows a set of possible values for an item's status: `PENDING`, `APPROVED`, `COMPLETE`|
| ENM-001 | **SHOULD** ensure that enumerations are lowercase a-z and/or upper case A-Z with hyphens, for example: `SPOT-RATE` or `spot-rate` or `Spot-Rate`|
| ENM-001 | Enumerations **MUST NOT** include spaces or special characters e.g. underscore |
| FPB-024 | Boolean fields **MUST** be unambiguous hence `isCurrency` is preferred to `isCurrencyorCountry`|

## Date & Time Fields

Date and time are defined by the OAS2 specification as [RFC3339](https://datatracker.ietf.org/doc/html/rfc3339) which is a profile of [ISO8601](https://en.wikipedia.org/wiki/ISO_8601). Both OAS2 and OAS3 define date and time as a JSON string with a format defined by the JSON schema [format extension](https://datatracker.ietf.org/doc/html/draft-bhutton-json-schema-validation-00#section-7.3).

The following table shows the mapping between the RFC and the JSON schema format extension. Note that `time` is not defined in the OAS2 specification but is accepted as an extension.

| Format in OAS specification | RFC 3339 | Example  |
|-----------------------------|----------|----------|
|  date                       | full-date|`2010-01-26`       |
|  date-time                  | date-time| `1985-04-12T23:20:50.52Z` or `1996-12-19T16:39:57-08:00` |
|  time                       | full-time| `23:20:50.52Z` or `1996-12-19T16:39:57-08:00`        |
|  partial-time               | partial-time| `23:20:50`  i.e. with no time zone information| 

The following table shows the associated rules:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| DEF-028<br>PEF-010 | **SHOULD** use a `string` data type and a `date` or `date-time` format for date and/or time fields|
| FPB-025 | **MAY** use the the format `time` with UTC format (23:20:50.52Z) not the time zone specification|
| FPB-026 | **SHOULD** avoid the format `partial-time`|

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

| Rule Identifier  | Description  |
|:-------:|:------------ |
| FPB-027 |  **MUST** use ISO 8601/RFC 3339 for dates and times|
| FPB-028 |  **MUST** use ISO 3166 for countries|
| FPB-029 |  **MUST** use ISO 4217 for currencies|


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

## Null Field Values

The use of `null` as a field value in an API request or response must be avoided in most scenarios because `null` represents an unknown or undefined value and implies ambiguity.

For example:
(1) the client should not need to send a request or receive a response using `null` unless using PATCH to remove a field value 
(2) the client or server can infer that a field value is `null`, i.e. undefined, if the payload does not contain the field key

The following table shows the associated rules:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| FPB-030 |    **MUST NOT** define API fields as `nullable` |
| FPB-031 |    **MUST NOT** send responses to clients with `null` as a field's value e.g. return {"a":"value"} rather than {"a":"value","b":"null"} |
