---
layout: default
title: Payload Formats
nav_order: 6
---

# Payload Formats
{: .no_toc}

Open APIs have request and response payloads (media types) that typically use industry standard data such as JSON or XML.

This section provides details of common media types associated with Finastra API payloads:
- TOC
{:toc}

Additional details of specific data types can be found in the *Naming Conventions* section.

## Media Types 

The Open API specification supports multiple media types as defined by [RFC6838](https://datatracker.ietf.org/doc/html/rfc6838).

The following are examples of media types used in Finastra APIs:
- `application/json` - for Json payloads - this is used by most APIs
- `application/xml` - for XML payloads
- `application/pdf` - for PDF documents
- `multipart/form-data` - for payloads combining fields and files

> APIs **SHOULD** use `application/json` as default `content-type`

> The default encoding of the payload is `charset=utf-8` by convention. The encoding **MAY** be specified in the section `consumes`/`produces` (OAS2) or `content` (in OAS3) as `application/json; charset=utf-8`

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

### XML

XML is a common data format standard and may still be required for
integration.

The following Finastra rules apply to xml formats:

> APIs **MAY** use XML **if and ONLY if** it follows industry data
> format standards.

> Non-JSON standards such as FpML **SHOULD NOT** be converted to JSON,
> rather use the content-type associated with the standard e.g. use XML 
> to define FpML content.

## Files

Open APIs may need to POST or GET attachments such as images, CSV files etc. to or from a resource.

The Open API specification allows for this using a mime type of `multipart/form-data` as shown in the following example in which:
-   the HTTP `multipart/form-data` is defined as the media type
-   the payload is defined as a `formData` parameter using a type of `file`

```yaml
/import:
  post:
    description: 'To import files into the system '
    produces:
      - application/json
    consumes:
      - multipart/form-data
    parameters:
      - type: string
        name: File meta data
        in: formData
      - type: file
        description: File to upload
        name: document
        in: formData
        required: true
```
