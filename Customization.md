---
layout: default
title: Customization
nav_order: 12
---

# Customization
- Custom Fields
{: .no_toc}

This section describes customization capabilities that may be provided with Finastra Open APIs
and includes the following sections:
- TOC
{:toc}

## Custom Fields

Many products provide the capability for Banks to define additional custom fields alongside 
common fields for resources (business objects) such as parties, accounts, loans etc.

The following lists typical terms used to describe these additional
fields:

-   custom fields - this is the term used in this log
-   extension fields
-   additional information fields
-   user defined fields

Custom-field data extension capability is typically provided by back office systems as an 'out of the box' feature developed by
the product R&D team to allow each individual Bank to add their own custom fields e.g. for head office reporting.

For example
```
  { "firstName" : "Luke" , "lastName" : "Skywalker" } 
```
Bank A can define a custom fields named `rating` that is an integer with a minimum value of 0. 
```
  { "firstName" : "Luke" , "lastName" : "Skywalker" , "rating" : 183} 
```
Bank B can define a custom field named `contact` that is a complex object composed of two fields: `email` and `phone_number`.
```
  { "firstName" : "Darth" , "lastName" : "Vader" , "contact" : { "email" : "ldarth.vader@star.com" , "phone_number": "111"}}
```

Custom fields differ from other fields in the API because custom fields are Bank specific and cannot be determined in advance i.e. custom fields are dynamic in nature so the custom fields can only be defined in a static API as an object whereas
Open APIs define a fixed set of well-known and documented fields adhering to the the OpenAPI Specification.

In the example above, the available set of custom fields can be described by a separate Json schema containing the field name, field type and constraints.

The custom fields schema represents a contract for any external developers and as such it has a lifecycle that
must be managed in the same way as APIs i.e. the schema should be versioned and should avoid (where possible) 
breaking changes.

In the OAS standard custom fields can be supported using the `additionalProperties` keyword, however, this support does not allow clients to discover the schema of the custom fields. 

Another constraint is that the custom field names must not collide with the other field names defined in the API.

> Support for custom fields in Finastra APIs is NOT provided by default and is NOT recommended but custom field support **MAY** be provided. The reason for not recommending custom fields within APIs is because of the increased complexity to the developer and the support overheads, however, there may be use cases for specific products where custom fields are a necessary requirement

### Custom Fields Rules Summary

> custom field data extension **MAY** be supported in Finastra APIs by ensuring that clients using the API can obtain details of the expected custom fields for a specific Bank.

> **MUST NOT** provide dedicated Bank specific APIs support data extension customization, use the technique below to avoid duplicating APIs

> Custom fields **MAY** be considered for API request

> When implemented, custom fields **SHOULD** be included in the payload of the associated business object i.e avoid providing a separate endpoint solely for custom-fields

> When implemented, custom fields **MUST** be grouped under the name `custom-fields`

> When implemented, custom fields **SHOULD** use the `additionalProperties` keyword against the `custom-fields` field

> When implemented, custom field schemas **SHOULD** be provided as an endpoint in the associated business object API using the path name `/custom-fields-schema` as e.g. `GET /parties/custom-fields-schema`

> When implemented, custom fields **MUST** be grouped under the name or prefix `custom-fields`

> When implemented, custom fields schemas **MUST** adhere to [JSON Schema](https://json-schema.org/understanding-json-schema/basics.html)


### Implementation

The following APIs and endpoints must be fully considered when implementing custom fields for a resource:

1. The **resource API** must ensure that `POST`, `PUT` and `GET` support custom fields e.g `GET /customer/{id}` allows a **client** to retrieve common and 
   custom **data** fields and `POST /customer` creates common and custom fields
2. The **resource API** must ensure that a client can `GET` the schema of the "customized part" e.g. `GET /customer/custom-fields-schema` allows a **client** to retrieve the **schema** of the custom fields for the specific Bank (tenant)
3. In a **resource meta data API** consider supporting `POST`, `PUT`, `GET`, `DELETE` e.g. `POST /customer/custom-fields-schema` allows a **Bank** to define the custom field **schema** for a business object

#### Resource Definition

>In the resource API ensure that `POST`, `PUT` and `GET` operations support custom fields e.g (1) `GET /parties/{id}` 
allow a client to retrieve both common and custom data fields and (2) `POST /parties` creates common and custom fields

Given the previous example here is an instance of the payload retrieved for Bank A
** GET /parties/{id}**
```
{
   "firstName" : "Luke" ,
   "lastName" : "Skywalker" ,
   "custom-fields": {
      "rating": 183
    }
}
```
same query for a different implementation at Bank B would return 
**GET /parties/{id}** 
```
{
   "firstName" : "Darth" ,
   "lastName" : "Vader" ,
   "custom-fields": {
      "contact" : {
          "email" : "darth.vader@star.com" ,
          "phone_number": "111"
      } 
    }
}
```

#### Resource Schema Definition

>- The `custom-fields` field defines the `additionalProperties` keyword as an object which supports both simple and complex additional properties
>- The custom field names `rating`, `contact`, `email`, phone_number are NOT defined in the API because they are dynamic fields
>- The custom field names, e.g. `phone_number`, do NOT need to adhere to Finastra API field naming standards because they are not explicitly defined in the API, rather they are defined externally e.g. in the Core backend
>- For further details of the `additionalProperties` keyword see [JSON schema](https://json-schema.org/understanding-json-schema/reference/object.html#id2)

```
customer:
  type: object
  required:
  - lastName
  properties:
    lastName:
      type: string
      example: Skywalker
    firstName:
      type: string
      example: Luke 
 
    custom-fields:
      type: object
      description : additional fields as defined by the financial institution, schema and
                    documentation can be found using the associated meta data endpoint
      additionalProperties:
        type: object
        example: { "custom-field-name": "custom-field-value" }
```

#### Resource Meta Data Schema Definition

>In the resource API ensure that a client can GET the schema of the "customized part" e.g. 
> ```GET /parties/custom-fields-schema``` allows a client to retrieve the schema of the custom fields for the specific Bank

> Custom Schema payload **MUST** be a valid Json schema.

**GET /parties/custom-fields-schema** 
```
{
  "$schema": "http://json-schema.org/draft-07/schema",
  "$id": "http://example.com/example.json",
  "type": "object",
  "title": "Bank B customization of customer schema",
	"properties": {
		"contact": {
			"$id": "#/properties/custom-fields/properties/contact",
			"type": "object",
			"title": "The contact schema",
			"description": "This represents the contact schema as a custom extension.",
			"required": [
				"email",
			],
			"properties": {
				"email": {
					"$id": "#/properties/custom-fields/properties/contact/properties/email",
					"type": "string",
					"title": "The email schema",
				},
				"phone_number": {
					"$id": "#/properties/custom-fields/properties/contact/properties/phone_number",
					"type": "string",
					"title": "The phone_number schema",
          "description": "This is the phone_number. this custom fields supports constrains like maxLength",
          "maxLength" : 12
				}
			}
		}
	}
}
```

#### Resource Meta Data Schema, Schema Definition

The custom-fields-schema is defined as an endpoint and must adhere to the OAS specification 
as shown in the following example which shows a smaller set of the capabilities of customization
e.g. it does not include the `minLength` attribute:

```
  customSchema:
    type: object
    description:  this is the meta schema representing a subset of json schema. This example contains  e.g. format, minLength is not available. Type and properties are mandatory. It is complex to read but short story is that it represents a json schema. 
    required: 
      - type
      - properties
    properties:
      $schema:
        description: schema fields according to json-schema 
        type: string
      $id:
        description: id field according to json-schema
        type: string
      type:
        type: string
        description: subset of json schema specification, here integer is not available. must be object for the top level. 
        enum:
          - object
          - string
          - number      
      description:
        description: a description of the custom schema or a custom field
        type: string
      title:
        description: a title  of the custom schema or a custom field
        type: string
      required:
        description:  defines which custom fields are required
        type: array
        items:
          type: string
      maxLength : 
        description:  supports the maxLength constraint - only available at field level
        type : integer
      properties:
        description:  stores the fields and uses a recursive definition - some fields like $schema should be used only at top level and not at field level     
        type: object
        additionalProperties:
          $ref: '#/definitions/customSchema'

```

>In a resource meta data API consider supporting ```POST, PUT, GET, DELETE```
> e.g.```POST /parties/custom-fields-schema``` to allow a Bank to define 
> the custom field schema for a business object

It is anticipated that this endpoint would be included in a separate API and would not typically be required.

> [Full Custom Field Data model Sample](./customization.yml)

### Supporting Multiple Schemas

This page has described scenarios for custom fields whose payload for a Bank can be described 
by a schema which is retrieved using a url similar to the following: 
```GET /parties/custom-fields-schema```, where this endpoint would be used alongside 
another endpoint e.g:  ```POST /parties``` to post commons and customs customer fields.

There are scenarios where multiple schemas for a specific resource resource are required ```resources="party"``` and ```{schemaId}="corporate"``` . This it need to be taken with particular care. Here is typically a bad example as it would have been better to properly have a proper model for  a corporate party. This would be may be more relevant for a ``commercial product`` like a loan package, that marketing could create at on the way , for instance bicycle loan. 

If having the capability to create a custom schema is possible, it is strongly not recommended to have a `PUT` end point, as it can create incompatibility between the data and the schema.

in this case the endpoints would be : 

| Endpoint Format                                      |      Endpoint description                                     |  Example                                | Example description |
|----------                                            |:-------------:                                                |------:                                  | ------:           |
| `POST /resources/custom-field-schema`                                | create a custom schema | `POST /loans/custom-field-schema`                    | create an instance of the new loan for instance bicycle|
| `GET /resources/custom-field-schema`            |  get all `schemaIds` associated with resources            | `GET /loans/custom-field-schema`         |get all `schemaIds` so all loan kind [car, bicycle ] |
| `GET /resources/custom-fields-schema/{schemaId}` |  get the `schema``` for the specific schemaId               | `GET /loans/custom-fields-schema/{bicycle}`  |get the schema for `{schemaId}=bicycle` |
| `POST /resources/{schemaId}` | create an object instance following the custom-schema definition `schemaIds`| `POST /loans/{bicycle}` |create a loan for `{schemaId}=bicycle` with all the constrains defined in the schema  `/loans/custom-fields-schema/{bicycle}` | 

## Localization

Localization in the context of APIs typically refers to the ability to return text messages and descriptions 
in the language requested by the caller of the API i.e. by specifying their locale in the request.

Finastra APIs **MAY** implement localization, however, note that:
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

For details on the relationship between localization and customization see [Localization vs Internationalization](https://www.w3.org/International/questions/qa-i18n).

### Finastra API Localization Standards ###

> APIs that support localization **MUST** use the `Accept-Language` header to facilitate content
> negotiation with the client and **MUST** inform the client of the selection in the `Content-Language` header


