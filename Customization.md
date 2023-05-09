---
layout: default
title: Customization
nav_order: 14
---

# Customization
- Custom Fields
{: .no_toc}

This section describes customization capabilities that may be provided with Open APIs
and includes the following sections:
- TOC
{:toc}

## Sample

The following sample OAS2 API can be used as a reference API definition showing the customization definitions on this page:
- [Full Custom Field Data model Sample](https://github.com/fusionfabric/open-api-standard/blob/main/customization-sample.yml). 

## Custom Fields

Many products provide the ability for Banks to define additional fields alongside 
common fields for resources (business objects) such as parties, accounts, loans etc.

The following lists typical terms used to describe these additional
fields:

-   custom fields - this is the term used in this section
-   extension fields
-   additional information fields
-   user defined fields

Custom field data support is typically provided by back office systems as an 'out of the box' feature developed by the product R&D team to allow each individual Bank to add their own custom fields e.g. for head office reporting.

Finastra do not publish Bank specific APIs hence a single API for a resource must support custom fields e.g. Finastra 
would expect to provide a single `parties` API that supports custom fields for multiple Banks. 

The following examples show a typical payload which contains common fields and custom fields:

1 - common fields:
```
  { "firstName" : "Luke" , "lastName" : "Skywalker" } 
```
2 - alongside the common fields, Bank A can define a custom field named `rating`: 
```
  { "firstName" : "Luke" , "lastName" : "Skywalker" , "rating" : 183} 
```

3 - alongside the common fields, Bank B can define a custom field named `contact` that is a complex object composed of two fields: `email` and `phone_number`:
```
  { "firstName" : "Darth" , "lastName" : "Vader" , "contact" : { "email" : "darth.vader@star.com" , "phone_number": "111"}}
```

Custom fields differ from other fields in the API because custom fields are Bank specific and cannot be determined in advance i.e. custom fields are dynamic in nature so the custom fields can only be defined in a static API as an object rather than as individual fields, whereas Open APIs typically define a fixed set of well known, documented fields adhering to the the OpenAPI Specification (OAS).

The available set of custom fields can be described by a separate Json schema containing the field name, field type and field constraints. The custom fields schema represents a contract for external developers and as such it has a lifecycle that must be managed in the same way as APIs i.e. the schema should be versioned and should avoid (where possible) 
breaking changes.

Custom fields are supported using the OAS `additionalProperties` keyword and clients must be able to discover 
the associated schema using a new endpoint that allows clients to retrieve a Bank' custom fields schema for a resource. 


### Implementation

The following APIs and endpoints should be provided when implementing custom fields for a resource:

1. The **resource API** must ensure that `POST`, `PUT` and `GET` support custom fields e.g.:
    - `GET /customer/{id}` should allow a client to retrieve a resource's common and custom **data** fields
    - `POST /customer` should allow a client to create a resource with common and custom **data** fields

2. The **resource API** must ensure that a client can `GET` the schema of the "customized part" e.g.:
    -  `GET /customer/custom-fields-schemas` should allow a client to retrieve the **schema** of the custom fields for the specific Bank (tenant)

3. A **resource meta data API** can be optionally provided to support `POST`, `PUT`, `GET`, `DELETE` operations that 
allow clients to update the custom fields schema e.g.:
    - `POST /customer/custom-fields-schemas` may allow a **Bank** to define the custom field **schema** for a business object

#### Resource Definition

In the resource API ensure that `POST`, `PUT` and `GET` operations support custom fields e.g.:
- `GET /parties/{id}` should allow a client to retrieve a resource's common and custom data fields 
- `POST /parties` should allow a client to create a resource with common and custom data fields

Using the example above, Bank A must be able to get a custom field named `rating` and Bank B must be able to get a custom field object named `contact` so that `GET /parties/{id}` returns the following:
```
Bank A
         { "firstName" : "Luke" , "lastName" : "Skywalker" , "rating" : 183} 
```
```
Bank B
         { "firstName" : "Darth" , "lastName" : "Vader" , "contact" : { "email" : "darth.vader@star.com" , "phone_number": "111"}}
```

#### Resource Schema Definition

The following guidelines show how the custom fields are defined in the APIs:
- The `custom-fields` field defines the `additionalProperties` keyword as an object which supports both simple and complex additional properties
- The custom field names, e.g. `rating`, `contact`, `email`, `phone_number`, are NOT defined in the API because they are dynamic fields which vary by Bank
- The custom field names, e.g. `phone_number`, do NOT need to adhere to API field naming standards because they are not explicitly defined in the API, rather they are defined externally e.g. in the Core backend
- For further details of the `additionalProperties` keyword see [JSON schema](https://json-schema.org/understanding-json-schema/reference/object.html#id2)

The following example shows a sample resource API definition for `custom-fields`:

```
  party:
    description: Party
    type: object
    required:
    - lastName

    properties:
      partyId:
        type: string
        description: UUID of the party
        readOnly: true

      lastName:
        type: string
        description: Surname
        example: Skywalker

      firstName:
        type: string
        description: First name
        example: Luke 
  
      custom-fields:
        type: object
        description : Custom fields defined by the financial institution. The schema and
                      documentation can be found using the associated meta data endpoint
        additionalProperties:
          type: object
          example: { "contact" : { "email" : "luke.skywalker@star.com" , "phone_number": "911"}}
```


#### Resource Meta Data Definition

In the resource API ensure that a client can `GET` the schema of the custom fields e.g. 
`GET /parties/custom-fields-schemas` would allow a client to retrieve the schema of the custom fields for the specific Bank.

The following example shows the custom fields schema for Bank B in the example above - this schema would be
returned from the `GET /parties/custom-fields-schemas` endpoint:

```
{
  "$schema": "http://json-schema.org/draft-07/schema",
  "$id": "http://example.com/example.json",
  "type": "object",
  "title": "Bank A customized party schema",
  "properties": {
	 "contact": {
		"$id": "#/properties/custom-fields/contact",
		"type": "object",
		"title": "Contact custom field",
		"description": "Party contact details.",
		"required": [
		  "email"
		],
		"properties": {
		  "email": {
			 "$id": "#/properties/custom-fields/contact/email",
			 "type": "string",
			 "title": "Email custom field",
			 "description": "Party email address."
		  },
		  "phone_number": {
			 "$id": "#/properties/custom-fields/contact/phone_number",
			 "type": "string",
			 "title": "Phone number custom field",
			 "description": "Party phone number.",
			 "maxLength": 12
		  }
		}
	 }
  }
}

```

#### Resource Meta Data Schema, Schema Definition

The Resource API provides the ability to create, read, update and delete the custom fields associated with a resource as well as allowing the client to obtain the custom fields schema. 

The Resource Meta Data API allows a client to maintain the custom field definitions and can be optionally provided to support `POST`, `PUT`, `GET`, `DELETE` operations that allow clients to update the custom fields schema.

The `custom-fields-schemas` resource associated with the Resource Meta Data API is defined as an endpoint and must adhere to the OAS specification as shown in the following example which shows a set of typical attributes of customization schema:

```
  customSchema:
    type: object
    description:  Ths defines the schema of the custom fields that will be returned to the client. The definition is a subset of json schema. Extra properties can be included as required e.g. format, minLength, pattern, etc. In this sample the custom field type and properties are mandatory and the maxLength is included. The example shows how this structure is used 
    required: 
      - type
      - properties
    properties:
      $schema:
        description: Describes which  dialect of JSON Schema this schema was written for - see JSON schema documentation for further details. 
        type: string
      $id:
        description: Used to identify the schema - see JSON schema documentation for further details
        type: string
      type:
        type: string
        description: Defines the type of available fields. Extra field types can be added as required e.g. integer
        enum:
          - object
          - string
          - number      
      description:
        description: Provides a description of the custom fields schema
        type: string
      required:
        description:  Used to state that the custom fields support the 'required' keyword 
        type: array
        items:
          type: string
      maxLength : 
        description:  Used to state that the custom fields support the 'maxLength' keyword
        type : integer
      properties:
        description:  Used to define the custom fields. It has a recursive definition. Note that some fields like $schema should only be used at the top level and not at field level     
        type: object
        additionalProperties:
          $ref: '#/definitions/customSchema
```

### Supporting Multiple Schemas

This page has described scenarios for custom fields whose payload for a Bank can be described 
by a schema which is retrieved using a url similar to the following: 
`GET /parties/custom-fields-schemas`. This custom schema endpoint would be used alongside 
another endpoint e.g: `POST /parties` which allows the client to add and update both common and custom party fields.

There are scenarios where multiple schemas for a specific resource are required, for example if the resource is `loans` then there may be custom fields schemas for different loan types such as `bicycle` or `car` loans.

If having the capability to create a custom schema is possible, it is strongly not recommended to have a `PUT` end point, as it can create incompatibility between the data and the schema.

in this case the endpoints would be : 

| Endpoint Type | Endpoint Example | Endpoint Description |
|---------------|------------------|----------------------|
| Resource Meta Data API|`GET /loans/custom-fields-schemas`|Get all `schemaId` associated with `loans` e.g. `{bicycle}`, `{car}`|
| Resource Meta Data API|`POST /loans/custom-fields-schemas`|Create a new custom schema for e.g. `{bicycle}`, `{car}`. This is an optional endpoint|
| Resource Meta Data API|`GET /loans/custom-fields-schemas/{bicycle}`| Get the `schema` for the specific `schemaId`|
| Resource API|`POST /loans/{bicycle}`|Create a resource following the custom-schema definition `schemaId`| 


### APIs Custom Fields Standards ###

The following is a list of Finastra standards associated with custom fields:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| CUS-001 | Support for custom fields in Finastra APIs is NOT provided by default and is NOT recommended but custom field support **MAY** be provided. The reason for not recommending custom fields within APIs is because of the increased complexity to the developer and the support overheads, however, there may be use cases for specific products where custom fields are a necessary requirement |
| CUS-002 | Bank specific APIs supporting bank specific data extension customization **MUST NOT** be provided, rather, follow the technique described in this section |
| CUS-003 | Custom field data extension **MAY** be supported in Finastra APIs by ensuring that clients using the API can obtain details of the expected custom fields for a specific Bank |
| CUS-004 | When implemented, custom fields schemas **MUST** adhere to [JSON Schema](https://json-schema.org/understanding-json-schema/basics.html) |
| CUS-005 | When implemented, custom fields **SHOULD** be included in the payload of the associated business object i.e avoid providing a separate endpoint solely for custom-fields |
| CUS-006 | When implemented, custom fields **SHOULD** be grouped under the name or prefix `custom-fields` |
| CUS-007 | When implemented, custom fields **SHOULD** use the `additionalProperties` keyword against the `custom-fields` field |
| CUS-008 | When implemented, custom field schemas **SHOULD** be provided as an endpoint in the associated business object API using the path name `/custom-fields-schemas` e.g. `GET /parties/custom-fields-schemas` |
| CUS-009 | When implemented, custom field names **SHOULD NOT** collide with other field names defined in the API |
| CUS-010 | When implemented, custom field schema maintenance **SHOULD** be considered by providing a resource meta data API supporting `POST`, `PUT`, `GET`, `DELETE`, e.g.`POST /parties/custom-fields-schemas`, to allow a Bank to define the custom field schema for a business object. It is anticipated that this endpoint would be included in a separate API and would not typically be required |


## Localization

Localization in the context of APIs typically refers to the ability to return text messages and descriptions 
in the language requested by the caller of the API i.e. by specifying their locale in the request.

APIs **MAY** implement localization, however, note that:
- APIs should be concerned with providing a business capability
to the customer and not with content negotiation. The UI and its
supporting services should be responsible for these concerns
- localisation can be expensive to implement and full consideration must be given 
to which data types support locales e.g. for amount, date and time fields it might be 
to allow the client to manage the locale translation

If adopting localization in an API, then the standard HTTP Header `Accept-Language` informs the service what
languages the client is able to understand, and what locale variant is
preferred. Using content negotiation, the service selects one of the
proposed language and locales, and confirms the selection within the
response header `Content-Language`. Browsers use this header to align
their UI.

The `Accept-Language` header format contains two items: `language` and `culture`.

In the following example, the French language is identified with the two
letter code `FR`; the Swiss culture/locale, which is optional in this
format, is identified by the two-letter code `ch`.

Example: `FR-ch`

For more details, about `Accept-Language` see [HTTP 1.1 RFC:section-5.3.5](https://tools.ietf.org/html/rfc7231#section-5.3.5).

For details on the relationship between localization and internationalization see [Localization vs Internationalization](https://www.w3.org/International/questions/qa-i18n).

### API Localization Standards ###

| Rule Identifier  | Description  |
|:-------:|:------------ |
| LOC-001 | APIs that support localization **MUST** use the `Accept-Language` header to facilitate content negotiation with the client and **MUST** inform the client of the selection in the `Content-Language` header |
