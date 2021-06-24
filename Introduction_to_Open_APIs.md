---
layout: default
title: Introduction to Open APIs
nav_order: 3
---

# Introduction to Open APIs

Systems expose APIs to allow other systems to interact with them and APIs are the contracts which act as glue between systems.

## Open APIs and OpenAPI

<span class="text-title">**Open API**</span>

From [Wikipedia](https://en.wikipedia.org/wiki/Open_API): *“An open API
(often referred to as a public API) is a publicly available application
programming interface that provides developers with programmatic access
to a proprietary software application or web service”*

<span class="text-title">**OpenAPI**</span>

The most common format to describe Open APIs is the [OpenAPI
Specification (OAS)](https://www.openapis.org/) standard which
describe the structure and syntax of the document, or code, that is used
to describe REST Open APIs.

Swagger is tooling that allows developers to design build and document
Open APIs according to the OpenAPI Specification.

An extensive OSS community provides tools for OAS APIs such as code generator,
editors, and documentation generator.

The Open API definition is agnostic to the server implementation in that
the underlying services can be written in various stacks - .NET, Spring,
etc., without change to the Open API. Similarly, the underlying services
must be agnostic of clients calling their APIs.

A well written API allows external API developers to use the
contents of the API definition to develop their own client without
recourse to further documentation. This requires the API documentation
to contain full details of data structures, validation rules and
expected behaviors for the operations within the API.

**References**

-   [Open API Initiative](https://www.openapis.org/)
-   [OpenAPI
    v2.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)
-   [OpenAPI
    v3.0.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md)
-   [Swagger Editor](https://editor.swagger.io/)

------------------------------------------------------------------------

## Finastra Open APIs

FInastra APIs are Open APIs as far as possible and a Finastra API should be designed so that:

-   it exposes functionality in a manner that is understood by
    developers with domain knowledge rather than knowledge of the
    underlying system
-   it is future proofed as far as possible so that it does not require
    significant future changes because once an API has been published it
    becomes difficult to change
-   it hides the internal implementations of the system it exposes

Within Finastra the following rules apply:

> Finastra Open APIs **MUST** be defined using the OpenAPI Specification
> (OAS)

> Finastra Open APIs **MUST** be well documented

> Finastra Open APIs **SHOULD** adopt API First and API as a Product
> strategies

> Finastra Open APIs **MAY** be be defined using OpenAPI v2 or v3

## Finastra Open API Principles

The following principles are specific to the Finastra Open API
initiative and reflect the strategic goals when designing and developing
Finastra Open APIs.

The audience for these principles is API designers and developers with
focus on the quality attributes of the API definition and the underlying
API implementation.

The Open API initiative adheres to the broader organisation architecture
principles.

The Open API principles are:

-   [Useful](#openapi-principle-useful)
-   [Keep it Simple](#openapi-principle-keepitsimple)
-   [Easy to Use](#openapi-principle-easytouse)
-   [Consistent](#openapi-principle-consistent)
-   [Backward Compatible](#openapi-principle-backwardcompatible)

It is assumed that an **API First** approach is adopted to deliver
**APIs as Products**.

### Useful

> Finastra Open APIs must be useful.

**Rationale**

Finastra Open APIs MUST be designed based on client requirements. They
should contain as much information as necessary but as little as
possible. They can support multiple related use cases.

**Implications**

Finastra Open API designers must ensure that the API’s target audience
and use cases are well known so that they can fulfill product
requirements.

Finastra Open API designers should adopt an iterative approach to design
ensuring that design fails fast, feedback is obtained and the API is
continually improved until the API is deemed useful and of production
quality.

### Keep it Simple

> Finastra Open APIs must be designed to be as simple as possible.

**Rationale**

Finastra Open APIs are publicly accessible and are used
primarily by external API developers. The successful adoption of
Finastra APIs is determined by API developers' experience.

API developers adopt and promote Finastra Open APIs that are
simple.

**Implications**

Finastra Open API designers MUST assume that the audience are:

-   NOT Finastra product experts - do NOT use product specific
    vocabulary.
-   NOT Banking experts - do use known Banking terminology.
-   NOT Technical experts - do NOT use technical jargon.

### Easy to Use

> Finastra Open APIs must be designed to be (1) easy to use and (2) hard
> to misuse.

**Rationale**

Finastra Open APIs are not adopted if they pose any barriers to API
developers.

**Implications**

Finastra Open API designers MUST ensure that the API is:

-   Clear
-   Concise
-   Coherent
-   Unambiguous
-   Intuitive to use
-   Consistent

### Consistent

> All Finastra Open APIs must be consistent in vocabulary and behavior.

**Rationale**

Finastra Open APIs must be professional:

-   They MUST be production-quality ready.
-   They MUST have a look and feel that is consistent across all
    Finastra Open APIs.

**Implications**

Finastra Open API designers and developers MUST ensure that:

-   Finastra standards are followed.
-   Common definitions are reused where appropriate.
-   Open APIs are created using linters that use Finastra Swagger rules
    to validate.
-   Open APIs are validated as part of CI/CD using Finastra Swagger
    rules.
-   Open APIs are approved for publication by the Open API team.

### Backward Compatible

> All Finastra Open APIs must be backward compatible between releases.

**Rationale**

Once a client has adopted a Finastra Open API the client expects that
API to function correctly on all future releases unless the client is
explicitly told that the API is being deprecated and obsoleted

**Implications**

Finastra Open API designers MUST ensure that:

-   APIs are versioned
-   Breaking changes to an API are not introduced

## API Maturity Levels

The **[Richardson Maturity
Model](https://www.martinfowler.com/articles/richardsonMaturityModel.html).**
is a way to score an API according to the constraints of the RESTful
architectural style.

An API with a score of 0 is not deemed RESTful whereas an API with a
score of 3 is a truly RESTful API.

The following table describes the Richardson Maturity Model:

| Level | Name                | Description                                                                                                                                                                                          |
|-------|---------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 0     | Swamp of POX        | Uses HTTP as a transport protocol only without using other HTTP capabilities e.g. SOAP would typically be see as level 0 <BR> (POX = ‘Plain Old XML’)                                                |
| 1     | Resources           | An endpoint per resource type                                                                                                                                                                        |
| 2     | HTTP Verbs          | Use HTTP properties to deal with scalability and faults e.g. HTTP methods, HTTP response codes                                                                                                       |
| 3     | Hypermedia controls | Use link elements in the response to address what may be done with further requests. <BR> This level of compliance is more commonly known as Hypermedia As The Engine Of Application State (HATEOAS) |

> Finastra Open APIs **SHOULD** target level 2: HTTP Verbs

> Finastra Open APIs **MAY** target level 3: Hypermedia Controls

## REST

REST stands for [REpresentation State Transfer

Finastra’s technical strategy is to adopt a REST(ful) architectural
style by publishing Open APIs that use HTTP for communication to access
or update resources, such as accounts, loans, deposits etc.


[(REST) wikipedia definition](https://en.wikipedia.org/wiki/Representational_state_transfer).

REST is an *architectural style* for providing *interoperability*
between systems.

REST is a concept and not a definitive standard. This guide is about moving from a concept to a standard, that can be implemented, and validated. 

REST APIs allow API consumers to execute a predefined set of stateless
operations on resources e.g. create, read, update or delete (CRUD) an
account

The following six guidelines (constraints) are used to define a RESTful
system:

-   Uniform Interface - based on resources
-   Client–Server architecture
-   Stateless
-   Layered
-   Cacheable
-   Code on Demand (optional)

### REST Resources

*The key abstraction of information in REST is a resource* - **Roy
Fielding**.

Any information that can be named can be a resource, for example:

-   a document
-   an image
-   a bank account
-   a temporal service e.g. “today’s weather in Los Angeles”
-   a collection of other resources
-   a non-virtual object e.g. a person
-   etc.

REST resources should reflect the relevant business domain and should have
identifiers (keys) that change as infrequently as possible.

Finastra APIs define resources aligned with the Banking business domain
e.g. currencies, accounts etc.
