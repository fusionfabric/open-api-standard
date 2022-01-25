---
layout: default
title: Responses and Errors
nav_order: 10
---

# Responses and Errors
{: .no_toc}

This page contains the following sections:
- TOC
{:toc}

### Response Status Codes

HTTP response status code are defined according to section 6 of
[RFC7231](https://tools.ietf.org/html/rfc7231#section-6).

The following table summarizes a simplified response codes list used in Open APIs:

| status code | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **200**       | **OK** - this code is a standard response for successful HTTP requests. The actual response depends on the request method used. In a `GET` request, the response contains an entity corresponding to the requested resource. In a `POST` request, the response contains an entity describing or containing the result of the action                                                                                                                                                                                                                                                                                           |
| **201**       | **Created** - this code indicates that a request has been fulfilled, resulting in the creation of a new resource                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| **202**       | **Accepted** - this code indicates that the request has been accepted for processing, but the processing has not been completed                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| **204**       | **No content** - this code indicates that the server has successfully fulfilled the request and there is no additional content to send in the response payload body                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| **400**       | **Bad Request** - this code indicates that the server cannot process the request due to an apparent client error such as malformed request syntax, size too large, invalid request message framing, or deceptive request routing                                                                                                                                                                                                                                                                                                                                                                                          |
| **401**       | **Unauthorized** - this code must be used when there is a problem with the client’s credentials. A 401 error response indicates that the client tried to operate on a protected resource without providing the proper authorization. It may have provided the wrong credentials or none at all                                                                                                                                                                                                                                                                                                                            |
| **403**       | **Forbidden** - this code should be used to forbid access regardless of authorization state. A 403 error response indicates that the client’s request is formed correctly, but the REST API refuses to honor it. A 403 response is not a case of insufficient client credentials; that would be 401 (“Unauthorized”). REST APIs use 403 to enforce application-level permissions. For example, a client may be authorized to interact with some, but not all of a REST API’s resources. If the client attempts to get a resource interaction that is outside of its permitted scope, the REST API should respond with 403 |
| **404**       | **Not Found** - this code is used when a client’s URI cannot be mapped to a resource                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| **409**       | **Conflict** - this code indicates that a request could not be completed due to a conflict with the current state of the target resource e.g. when a request to update a resource is based on an earlier version of the resource. This status code is used in situations where the user might be able to resolve the conflict and resubmit the request                                                                                                                                                                                                                                                                    |
| **412**       | **Precondition Failed** - indicates that access to a resource has been denied. This happens with conditional requests when the condition is not met. This is typically used for concurrency checks using an `If-Match` HTTP header containing an `ETag` value representing the state (or version) of the resource that is to be updated - the `ETag` value is typically retrieved using a prior GET operation. See the *Concurrency* section for further details                                                                                                                                                          |
| **428**       | **Precondition Required** - this code indicates that the server expects the request to be conditional e.g. this might be issued when a `PUT` requests is missing an `If-Match` HTTP header                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| **500**       | **Internal Server Error** - this code is a generic error message, given when an unexpected condition was encountered and no other specific message is suitable                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| **501**       | **Not Implemented** - this code indicates that the server does not support the functionality required to fulfill the request                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| **503**       | **Service Unavailable** - this code indicates that the server is currently unable to handle the request as a result of a temporary condition which may be alleviated after some delay                                                                                                                                                                                                                                                                                                                                                                                                                                     |

 

## Responses

Reponses can be expressed in various way in OpenAPI specification, adding the details at each end points, leveraging a data structure of the response body. The prefered way and the most re usable way it to leverage on the reponse dedicated section 


Not recommanded way : 
```
path: 
/customers/{customerId}':
    get:
      parameters:
        type: 
        - $ref: '#/parameters/customerId'
      operationId: get-customers-customerId
      responses:
        '200':
          description: This API retrieves the party details.
          schema:
            type: object
            properties:
              name:
                type: string
                 
         
```

rather doing the way below, especially for common error patern. This allows reusability , consistency and reduce maintenance.
```
path: 
/customers/{customerId}':
    get:
      tags:
        - Party
      parameters:
        - $ref: '#/parameters/partyId'
        - $ref: '#/parameters/X-Request-ID'
      summary: Read party.
      description: Read party.
      operationId: get-parties-partyId
      responses:
        '500':
          $ref: '#/respoonses/500_INTERNAL_ERROR'

responses 
   '500_INTERNAL_ERROR':
    description: Internal Server Error.
    schema:
      $ref: '#/definitions/Error'
```



The following table lists rules for response codes: 

| Rule Identifier  | Description  |
|:-------:|:------------ |
| RSP-003<br>RSP-004<br>RSP-005<br>RSP-006 | Endpoints **MUST** support the following return codes: 400, 401, 404, 500 |
| REB-001 | APIs **MUST NOT** return 200 (OK) when there is a technical or functional error, rather a 4XX code must be returned |
| RSP-001 | `POST` operations used to create a resource **MUST** return 201 or 202 for a successful response |
| RSP-001 | `POST` operations **MUST** return either 200 , 201 or 202 or 204 for a successful response |
| RSP-001 | `POST` operations used to get data with complex queries MAY return 200 or 204 for a successful response |
| RSP-002 | `PUT` or `PATCH` operations **MUST** return 200 or 204 for a successful response |
| RSP-009 | `PUT` or `PATCH` operations **SHOULD** support 412 and 428 responses for concurrency purposes |
| RSP-010 | `GET` and `HEAD` operations **MUST** return 200 or 204 for a successful response |
| RSP-011 | `DELETE` operations **MUST** return 200 or 202 or 204 for a successful response |
| REB-002 | APIs **MAY** use other error codes such as: 403, 409, 501, 503 |
| RSP-007<br>RSP-008 | APIs **MUST NOT** contain response fields when a 204 (No Content) is returned |
| REB-003 | `GET` operations using a filter **MUST NOT** return 404 if there are no resources, rather a 200 must be returned |
| RSP-013 | `PUT` operations SHOULD contain an identifier e.g. PUT /resource/{id} or PUT /resource/{id}/status |
| RSP-014 | tbc |


## Error Message Structure

This section includes the following:

-   details of [RFC 7807](https://tools.ietf.org/html/rfc7807) - this is
    the basis of error message structure
-   details of standards relating to error messages

### RFC 7807

RFC 7807 provides a standard format for returning problem details from
HTTP APIs.

RFC7807 proposes a structure for common pattern of errors whilst also
allowing for extensions. The following text is taken from RFC7807 and
describes the principal fields of the error message structure that APIs adopt:

-   `type` (string) - A URI reference \[RFC3986\] that identifies the
    problem type. This specification encourages that, when dereferenced,
    it provides human-readable documentation for the problem type,
    using, for example, HTML (\[W3C.REC-html5-20141028\]). When this
    member is not present, its value is assumed to be
    `about:blank`.
-   `title` (string) - A short, human-readable summary of the problem
    type. It SHOULD NOT change from occurrence to occurrence of the
    problem, except for purposes of localization using, for example,
    proactive content negotiation - see \[RFC7231\], Section 3.4).
-   `status` (number) - The HTTP response status code (\[RFC7231\],
    Section 6) generated by the origin server for this occurrence of the
    problem.
-   `details` (string) - A human-readable explanation specific to this
    occurrence of the problem.

### Error Message Standards

The following are the Open API error message standards:

| Rule Identifier  | Description  |
|:-------:|:------------ |
| REB-011 | The main error message **MUST** follow RFC7807 structure and names |
| REB-012 | The use of the RFC7807 `instance` keyword is **NOT** mandated but **MAY** be used |
| REB-013 | Error messages **MUST NOT** expose details of the technical implementation |
| REB-014 | `title` and `status` **MUST** be defined in the API; all other fields are optional |
| REB-015 | If a field is not used then it **SHOULD NOT** be included in the API e.g. `causes` is only required if additional business reasons are added |
| REB-016 | `causes` can be used to provide additional detail on an error message or to provide a list and sequence of a chain of error messages from an end to end system |
| REB-017 | The structure of `causes` is not mandated, however, when `causes` is provided it should contain an indication of the field in error |
| REB-018 | Text capable fields **MAY** support multi-language |

The standard error message structure is as follows:

```json
{
    "type": "https://api.finastra.com/validation-error",
    "title": "The request could not be processed due to applicable business validation",
    "status": 400,
    "detail": "Ensure that the request is valid"
}
```

If additional details of one or more error messages are needed then an
array of `causes` can be provided, however, the presence and structure
of `causes` is not mandated.

The following example shows how `causes` might be used:

```json
{
  "type": "https://api.finastra.com/validation-error",
  "title": "The request is invalid",
  "status": 400,
  "detail": "The account does not exist",
  "causes": [
    {
      "title": "The account '1234567890' is dormant",
      "field": "account",
      "fieldValue": "0543123467083"
    }
  ]
}
```
