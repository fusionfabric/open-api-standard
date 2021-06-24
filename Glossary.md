---
layout: default
title: Glossary
nav_order: 16
---

# Glossary

This section describes the terms used within this guide with emphasis on
their meaning within Finastra.

**API**

An
[API](https://en.wikipedia.org/wiki/Application_programming_interface)
is a set of definitions and communication protocols that allow
communication between components of a system.

Finastra products publish many different types of API, such as JMS APIs,
SOAP APIs, File APIs, Websockets APIs etc., however, the focus of this
guide is Open APIs that use HTTP as a communication protocol and adhere
to RESTful concepts.

**CRUD**

CRUD refers to **C**reate, **R**ead, **U**pdate, **D**elete and is used
in the context of operations that can be performed against a resource or
business object.

The CRUD operations are comparable to the following REST API operations:
POST, GET, PUT, and DELETE.

Many Finastra products publish CRUD API operations to allow clients to
capture and retrieve data stored in the product data store.

CRUD operations may not be relevant to products that adopt a [Command
and Query Responsibility Sgeregation
(CQRS)](https://martinfowler.com/bliki/CQRS.html) pattern.

**HAL**

[HAL](https://en.wikipedia.org/wiki/Hypertext_Application_Language)
refers to Hypertext Application Language and it is a standard convention
for defining links to external resources within JSON or XML code, for
example, if a client requests a resource such as a loan then the
response might return a set of links in HAL format specifying how to
obtain additional details of the resource.

**HATEOS**

[HATEOS](https://en.wikipedia.org/wiki/HATEOAS) refers to Hypermedia as
the Engine of Application State which is a style whereby API responses
provide links that are related to the API requested by a client.

For example, a HATEOS-driven site provides information to clients that
allow them to navigate a site’s resources dynamically by including
hypermedia links with the responses.

**JSON**

JSON refers to [JavaScript Object Notation](https://www.json.org/) which
is a lightweight syntax that can be used to define business objects. It
is seen as a more lightweight version of XML. JSON (or YAML) can be used
to define Finastra Open APIs aligned with the OpenAPI specification.

**Open API** (with space)

An [Open API](https://en.wikipedia.org/wiki/Open_API) refers to an API
that is exposed to developers by a platform such as
[fusionfabric.cloud](https://www.fusionfabric.cloud/).

When an organisation publishes a set of Open APIs they typically
have the following attributes:
[Useful](#FinastraOpenAPIs-Principles-Useful) [Keep it
Simple](#FinastraOpenAPIs-Principles-KeepitSimple) [Easy to
Use](#FinastraOpenAPIs-Principles-EasytoUse)
[Consistent](#FinastraOpenAPIs-Principles-Consistent) [Backward
Compatible](#FinastraOpenAPIs-Principles-BackwardCompatible)

**OpenAPI** (without space)

An [OpenAPI](https://en.wikipedia.org/wiki/OpenAPI_Specification) refers
to an API that adheres to the [OpenAPI
Specification](https://www.openapis.org/) standard, or Swagger
Specification standard.

These OpenAPI standards describe the structure and syntax of the
document, or code, that is used to describe RESTful APIs.

Finastra APIs are compliant with [OAS
2](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)
or [OAS
3](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md)
where OAS is an abbreviation for ‘OpenAPI Specification’.

**Operation** **(or** **Endpoint)**

The operations, or endpoints, define the exposed capabilities of the API
as operations against resources, such as:

``` notoggle
*   GET /trades : to list all trades
*   POST /trades : to create a new trade
*   GET /trades/{id} : to read a trade
```

In this example, there are 3 endpoints, 2 operations named GET and POST,
and 1 resource named trades

**Partner API** **(or Private API or Product API)**

A Partner API refers to an API that is exposed to partners primarily for
the purposes of integrating systems.

Partner APIs are also referred to as Private APIs or Product APIs.

Typically a Partner API is coarser grained than an Open API since
it is targeted at more generic integration use cases that typically
provide a large set of business object fields as the API payload .

Partner APIs are not part of Finastra’s Open API strategy.

Partner APIs defined as a Swagger file are subject to the same Swagger
Standards detailed in this guide.

**Resource**

A resource in a REST API is a business object associated with the
business domain, such as trades, loans data or function.

**REST**

REST is short for [Representational State
Transfer](https://en.wikipedia.org/wiki/Representational_state_transfer)
and is an architectural concept for the provision of web services.

**REST API**

REST APIs are APIs that use HTTP protocol and adhere to RESTful
principles.

**Swagger file**

A Swagger file is a description, in JSON or YAML format, of a REST API
and its operations. For example an API might be named *Micro-Loans* and
have operations such as *Create Micro-Loan*, *Update Micro-Loan*,
*Delete Micro-Loan*.

Swagger file syntax aligns with the [OpenAPI
Specification](https://www.openapis.org/) which is available at [OAS
2](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)
or [OAS
3](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.2.md)
where OAS is an abbreviation for ‘OpenAPI Specification’.

**YAML**

[YAML](https://yaml.org/) [](https://yaml.org/spec/1.2/spec.html)refers
to “YAML Ain’t Markup Language”. YAML is a human-readable,
space-sensitive syntax. YAML (or JSON) can be used to define Finastra
Open APIs aligned with the OpenAPI specification.
