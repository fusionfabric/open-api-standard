---
layout: default
title: Payload Data
nav_order: 6
---

# Payload Data

Open APIs have request and response payloads (media types) that typically use industry standard data such as JSON or XML.

## Content-type 

The Open API specification supports multiple media types as defined by [RFC6838](https://datatracker.ietf.org/doc/html/rfc6838).

The following are examples of media types used in Finastra APIs:
- application/json - for Json payloads - this is used by most APIs
- application/xml - for XML payloads
- application/pdf - for PDF documents
- multipart/form-data - for payloads combining fields and files

### JSON

JSON, pronounced *jason*, is a lightweight data interchange format; an
overview of JSON can be found here: [www.json.org](www.json.org).

JSON format is described in [RFC
7159](https://tools.ietf.org/html/rfc7159) which provides a high level
description of the syntax and data types.

JSON has replaced XML as the *de facto* standard for data format, and
has broad support across all languages and technology stacks that
Finastra support.

The JSON specification does not dictate the order in which
properties are serialized, hence, the API and/or calling client must not
assume the order of properties within a JSON payload.

> APIs **SHOULD** use application/json as default `content-type`


### XML

XML is a common data format standard and may still be required for
integration.

The following Finastra rules apply to xml formats:

> APIs **MAY** use XML **if and ONLY if** it follows industry data
> format standards.

> Non-JSON standards such as FpML **SHOULD NOT** be converted to JSON,
> rather use the content-type associated with the standard e.g. XML for
> FpML.

## Files

Open APIs may need to POST or GET attachments such as images, CSV files etc. to or from a resource.

The Open API specification allows for this using a mime type of `multipart/form-data` as shown in the following example in which:
-   the HTTP `multipart/form-data` is defined as the media type
-   the payload is defined as a `formData` parameter using a type of `file`

``` notoggle
"/import": {
      "post": {
        "description": "To import files into the system ",
         "produces": [
          "application/json"
        ],
        "consumes": [
          "multipart/form-data"
        ],
          "parameters": [
            {
              "type" : "string",
              "name" : "File meta data"
              "in" : "formData"
            }
            ,
          {
            "type": "file",
            "description":"File to upload",
            "name": "document",
            "in": "formData",
            "required": true
          }
        ],
```

## Localization , Internationalization 

> The difference between these two topics is out of scope for this
> guide. For details, see [Localisation
> vs. Internationalization](https://www.w3.org/International/questions/qa-i18n)
> article by W3C.

Localization and internationalization are expensive to implement
correctly. APIs should be concerned with providing a business capability
to the customer and not with content negotiation. The UI and its
supporting services should be responsible for these concerns.

> APIs **SHOULD NOT** be concerned with either localization or
> internationalization.

Out of scope for APIs:

-   Localization
    -   Translations
    -   Right To Left and other orientations
-   Internationalization
    -   Decimal separators
    -   Currency

In scope for APIs:

-   Date
-   Date-Time
-   Time

> APIs that support either localization or internationalization **MUST**
> understand the `Accept-Language` header to facilitate content
> negotiation with the client and inform the client of the selection in
> the `Content-Language` header.

The standard HTTP Header `Accept-Language` informs the service what
languages the client is able to understand, and what locale variant is
preferred. Using content negotiation, the service selects one of the
proposed language and locales, and confirms the selection within the
response header `Content-Language`. Browsers use this header to align
their UI.

The `Accept-Language` header format contains two items: language,
culture.

In the following example, the French language is identified with the two
letter code `FR`; the Swiss culture/locale, which is optional in this
format, is identified by the two-letter code `ch`.

Example:

``` notoggle
FR-ch
```
For more details, about `Accept-Language` [HTTP 1.1 RFC:section-5.3.5](https://tools.ietf.org/html/rfc7231#section-5.3.5).

> The default encoding MUST be `charset=utf-8` of the payload by convention. It **MAY** be specified in the section  consumes/ produces (OAS2) or content (in OAS3) as `application/json; charset=utf-8`


