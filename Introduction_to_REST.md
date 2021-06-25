---
layout: default
title: Introduction to REST
nav_order: 2
---

Finastra's technical strategy is to adopt a REST(ful) architectural style by publishing Open APIs that use HTTP for communication to access or update resources, such as accounts, loans, deposits etc.

This section provides an overview of REST APIs:
- TOC
{:toc}


## REST

REST stands for [REpresentation State Transfer](https://en.wikipedia.org/wiki/Representational_state_transfer).

Finastra’s technical strategy is to adopt a REST(ful) architectural
style by publishing Open APIs that use HTTP for communication to access
or update resources, such as accounts, loans, deposits etc.



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
