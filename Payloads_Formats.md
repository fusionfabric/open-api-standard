---
layout: default
title: Payload Formats
nav_order: 6
---

# Payload Formats
{: .no_toc}

Open APIs have request and response payloads (media types) that typically use industry standard data such as JSON or XML.

This section provides details of common media types associated with API payloads:
- TOC
{:toc}

Additional details of specific data types can be found in the *Naming Conventions* section.

## Media Types 

The Open API specification supports multiple media types as defined by [RFC6838](https://datatracker.ietf.org/doc/html/rfc6838).

The following are examples of media types that may be used in APIs:
- `application/json` - for Json payloads - this is used by most APIs
- `application/xml` - for XML payloads
- `application/pdf` - for PDF documents
- `multipart/form-data` - for payloads combining fields and files
- `image/jpeg`
- `image/tiff`
- `image/png`
- `image/gif`
- `text/plain`
- `application/json+problem`

Most of the time an API produces and consumes the same media type ( application/json) so when using OAS2 specification , it is recommended to leverage it at the top part of the file. If an endpoint requires a dedicated media type it is then added at the endpoint level but it should apply for specific case only.  

OAS2 , Not recommended way : 
```
/users:
  post:
    description: 'create user'
    consumes: 
       - application/json
    produces:
      - application/json
```

OAS2 recommended way : 
```
swagger: '2.0'
info:
  {...}
basePath: /v1
schemes:
  - https
consumes:
  - application/json
produces
  - application/json
path 
  /users:
    post:
      description: 'create user'
  /users/{userId}/picture:
    get:
      description: 'return user's picture '
    produces
      - image/gif
```

The following table shows the associated rules:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| FMB-001 | APIs **SHOULD** use `application/json` as default `content-type` |
| FMB-002 | The default encoding of the payload is `charset=utf-8` by convention. The encoding **MAY** be specified in the section `consumes`/`produces` (OAS2) or `content` (in OAS3) as `application/json; charset=utf-8` |
| MIM-001<br>MIM-002<br>MIM-003<br>MIM-004 | **SHOULD** limit the content types to the following: `application/json`, `application/xml`, `application/pdf`, `multipart/form-data`, `image/jpeg`, `image/tiff`, `image/png`, `image/gif`, `text/plain`, `application/json+problem` |
| PPM-011 | OAS2 APIs **SHOULD NOT** use `consumes` against `GET` operations |
| PPM-012 | OAS2 APIs **SHOULD** defines produces and consumes at top level |


### JSON

JSON, pronounced *jason*, is a lightweight data interchange format; an
overview of JSON can be found here: [www.json.org](www.json.org).

JSON format is described in [RFC
7159](https://tools.ietf.org/html/rfc7159) which provides a high level
description of the syntax and data types.

JSON has replaced XML as the *de facto* standard for data format, and
has broad support across all languages and technology stacks.

The JSON specification does not dictate the order in which
properties are serialized, hence, the API and/or calling client must not
assume the order of properties within a JSON payload.

### XML

XML is a common data format standard and may still be required for
integration.

The following rules apply to xml formats:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| FMB-003 | APIs **MAY** use XML **if and ONLY if** it follows industry data format standards
| FMB-004 | Non-JSON standards such as FpML **SHOULD NOT** be converted to JSON, rather use the content-type associated with the standard e.g. use XML to define FpML content

## Files

Open APIs may need to POST or GET attachments such as images, CSV files etc. to or from a resource.

The Open API specification allows for this using a mime type of `multipart/form-data` as shown in the following example in which:
-   the HTTP `multipart/form-data` is defined as the media type
-   the payload is defined as a `formData` parameter using a type of `file`

```yaml
/import:
  post:
    description: 'To import files into the system '
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
