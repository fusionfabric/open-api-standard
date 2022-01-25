---
layout: default
title: Introduction to Open APIs
nav_order: 5
---

# Introduction to Open APIs
{: .no_toc}

Systems provide APIs to allow other systems to interact with them and API contracts define the behavior and content of the system interaction.

Finastra APIs are Open APIs defined using the OpenAPI specification and they are publicly available on the Finastra Developer Portal.

This page contains the following:
- TOC
{:toc}

## Open APIs

From [Wikipedia](https://en.wikipedia.org/wiki/Open_API): *“An open API
(often referred to as a public API) is a publicly available application
programming interface that provides developers with programmatic access
to a proprietary software application or web service”*

## OpenAPI

The most common format to describe Open APIs is the [OpenAPI
Specification (OAS)](https://www.openapis.org/) standard which
describe the structure and syntax of the document, or code, that is used
to describe REST Open APIs.

Swagger is tooling that allows developers to design build and document
Open APIs according to the OpenAPI Specification.

An extensive OSS community provides tools for OAS APIs such as code generator,
editors, and documentation generator.

The Open API definition is agnostic to the server implementation in that
the underlying services can be written in various technical stacks e.g. .NET, Spring,
etc., without change to the Open API. Similarly, the underlying services
are agnostic of clients calling their APIs.

A well written API allows external API developers to use the
contents of the API definition to develop their own client without
recourse to further documentation. This requires the API documentation
to contain full details of data structures, validation rules and
expected behaviors for the operations within the API.

### References

-   [Open API Initiative](https://www.openapis.org/)
-   [OpenAPI
    v2.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)
-   [OpenAPI
    v3.1.0](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.1.0.md)
-   [Swagger Editor](https://editor.swagger.io/)

------------------------------------------------------------------------

## Open APIs

APIs are Open APIs (public) as far as possible and an API should be designed so that:

-   it exposes functionality in a manner that is understood by
    developers with domain knowledge rather than knowledge of the
    underlying system
-   it hides the internal implementations of the system it exposes
-   it is future proofed as far as possible so that it does not require
    significant future changes because once an API has been published it
    becomes difficult to change

The following rules apply:

| Rule Identifier  | Description  |
|:-------:|:------------ |
|EXT-001<br>EXT-002<br>EXT-003<br>EXT-004 | Open APIs **MUST** be defined using the OpenAPI Specification (OAS) |
| PRI-001 | Open APIs **MUST** be well documented |
| PRI-002 | Open APIs **SHOULD** adopt API First and API as a Product strategies |
| PRI-003 | Open APIs **MAY** be be defined using OpenAPI v2 or v3 |

## Open API Principles

The following principles are should apply when designing and developing an  Open APIs.
These principles are aimed at API designers and developers so that they 
focus on the quality attributes of the API definition and the underlying
API implementation.

The Open API principles are:

-  Useful
-  Keep it Simple
-  Easy to Use
-  Consistent
-  Backward Compatible

It is assumed that an **API First** approach is adopted to deliver **APIs as Products**.

### Useful

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PRI-010 | Open APIs must be useful |

*Rationale*

Open APIs MUST be designed based on client requirements. They
should contain as much information as necessary but as little as
possible. They can support multiple related use cases.

*Implications*

Open API designers must ensure that the API’s target audience
and use cases are well known so that they can fulfill product
requirements.

Open API designers should adopt an iterative approach to design
ensuring that design fails fast, feedback is obtained and the API is
continually improved until the API is deemed useful and of production
quality.

### Keep it Simple

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PRI-020 | Open APIs must be designed to be as simple as possible |

*Rationale*

Open APIs are used by external API developers. The successful adoption of
APIs is determined by API developers' experience. The more simple the more adoption. 


*Implications*

Open API designers MUST assume that the API audience are:

-   NOT product experts - do NOT use product specific
    vocabulary.
-   NOT Business experts - do use known Business terminology.
-   NOT Technical experts - do NOT use technical jargon.

### Easy to Use

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PRI-030 | Open APIs must be designed to be (1) easy to use and (2) hard to misuse |

*Rationale*

Open APIs are not adopted if they pose any barriers to API
developers.

*Implications*

Open API designers MUST ensure that the API is:

-   Clear
-   Concise
-   Coherent
-   Unambiguous
-   Intuitive to use
-   Consistent

### Consistent

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PRI-040 | All Open APIs MUST be consistent in vocabulary and behavior |

*Rationale*

Open APIs must be professional:

-   They MUST be production-quality ready.
-   They MUST have a look and feel that is consistent across all  Open APIs.

*Implications*

Open API designers and developers MUST ensure that:

-   standards are followed.
-   Common definitions are reused where appropriate.
-   Open APIs are created using linters that use Swagger rules
    to validate.
-   Open APIs are validated as part of CI/CD using Swagger
    rules.
-   Open APIs are approved for publication by the Open API team.

### Backward Compatible

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PRI-050 |All Open APIs must be backward compatible between releases |

*Rationale*

Once a client has adopted a Open API the client expects that
API to function correctly on all future releases unless the client is
explicitly told that the API is being deprecated and obsoleted

*Implications*

Finastra Open API designers MUST ensure that:

-   APIs are versioned
-   Breaking changes to an API are not introduced

## REST API Maturity Levels

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
    
The following  rules apply:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| PRI-060 | Open APIs **SHOULD** target level 2: HTTP Verbs |
| PRI-061 | Open APIs **MAY** target level 3: Hypermedia Controls |
