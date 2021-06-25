---
nav_exclude: true
---

# Welcome

This document is the **Finastra Open API Design Guide** and the intended
audience are designers, developers, or consumer of Finastra Open APIs platform (https://developer.fusionfabric.cloud/ )  .

This document introduces the concepts of Finastra RESTful Open APIs and
details associated Open API development standards.

Finastra strategy is **API as a Product** and **API First** and these
approaches lead to successful implementation of a Finastra Open API
as a product.

<span class="text-title">**Document Conventions**</span>

-   The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”,
    “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in
    Finastra standards are to be interpreted as described in [RFC
    2119](https://www.ietf.org/rfc/rfc2119.txt)

-   When a Finastra Open API standard is highlighted within the
    sections, the standard is shown in the following format:

> Finastra **SHOULD** follow this document however there is not legal guarantee.



Finastra Open API Design Guide
================
**Fusion**Fabric.cloud Documentation Team
31 May 2021


**Design Guidelines**
-   [Building Blocks](./Building_Blocks.md)
-   [Introduction to Open APIs](./Introduction_to_Open_APIs.md)
    -   [Open APIs and OpenAPI](./Introduction_to_Open_APIs.md#open-apis-and-openapi)
    -   [Finastra Open API Principles](./Introduction_to_Open_APIs.md#finastra-open-api-principles)
    -   [API Maturity Levels](./Introduction_to_Open_APIs.md#api-maturity-levels)
    -   [Introduction to REST](./Introduction_to_Open_APIs.md#REST)
-   [HTTP](./Http.md)
    -   [HTTP Transport](./Http.md#rest-and-http-transport)
    -   [HTTP Methods](./Http.md#rest-and-http-methods)
    -   [HTTP Headers](./Http.md#rest-and-http-headers)
-   [Payloads](./Payloads_and_Formats.md)
    -   [Content type](./Payloads_and_Formats.md#Content-type)
    -   [Localization and Internationalization](./Payloads_and_Formats.md#Localization-,-Internationalization)
-   [Responses and Errors](./Responses_and_Errors.md)
    -   [HTTP Response code](./Responses_and_Errors#http-response-code)
    -   [Responses](./Responses_and_Errors#responses)
    -   [Error Message Structure](./Responses_and_Errors.md#finastra-error-message-structure)
-   [Naming Conventions](./Naming_Conventions.md)
    -   [Resources](./Naming_Conventions.md#resources)
    -   [Field name, query parameter name](./Naming_Conventions.md#Field-name,-query-parameter-name)
    -   [Field type, enum, format (date/time)](./Naming_Conventions.md#Field-type-and-format)
-   [Collections and Pagination](./Collections_and_Pagination.md)
    -   [Collections](./Collections_and_Pagination.md#collections)
    -   [Searching and Filtering](./Collections_and_Pagination.md#searching-and-filtering)
    -   [Sorting](./Collections_and_Pagination.md#sorting)
    -   [Pagination Request](./Collections_and_Pagination.md#pagination-request)
    -   [Pagination Response](./Collections_and_Pagination.md#pagination-response)
-   [Security](./Security.md)
-   [Versioning](./Versioning.md)
    - [Semantic versioning](./Versioning.md#Semantic-versioning)
	 - [API Breaking Changes](./Versioning.md#api-breaking-changes)
	 - [API Maturity Levels](./Versioning.md#maturity-levels)
	 - [API Backward Compatibility](./Versioning.md#api-backward-compatibility)
	 - [API Lifecycle](./Versioning.md#api-lifecycle)
-   [Customization](./Customization.md)
    -   [Custom Fields](./Customization.md#introduction#introduction)
    -   [Problematic ](./Customization.md#introduction#problematic)
    -   [Rules Summary](./Customization.md#introduction#Rules-Summary)
    -   [Implementation](./Customization.md#introduction#implementation)  ​
-   [Concurrency](./Concurrency.md)
-   [Idempotency](./Idempotency.md)


**Appendix**
-   [Glossary](./Glossary.md)
-   [Useful Resources](./Useful_Resources.md)
-   [Document History](./Document_History.md)

