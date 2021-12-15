---
layout: default
title: Introduction to REST
nav_order: 3
---

# Introduction to REST
{: .no_toc}

This section provides an overview of REST and the considerations for APIs:

- TOC
{:toc}

## REST

REST stands for [REpresentation State Transfer](https://en.wikipedia.org/wiki/Representational_state_transfer).

Finastra’s technical strategy is to adopt a REST(ful) architectural
style by publishing Open APIs that use HTTP for communication to access
or update resources, such as accounts, loans, deposits etc.

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

REST is an *architectural style* for providing *interoperability*
between systems but REST is not a definitive standard hence 
this document provides standards related to REST Open APIs.

### REST Resources

*The key abstraction of information in REST is a resource* - **Roy
Fielding**.

Any information that can be named can be a resource, for example:

-   a document
-   an image
-   a bank account
-   a collection of other resources
-   a non-virtual object e.g. a person
-   etc.

REST resources should reflect the relevant business domain and should have
unchanging identifiers (keys).

Finastra APIs define resources aligned with the Banking business domain
e.g. currencies, accounts etc.


# HTTP and RESTful APIs

This section provides an overview of the main components of REST APIs:

-   HTTP Transport
-   HTTP Methods
-   HTTP Headers

## HTTP Transport

REST architecture supports the creation and maintenance of resources
using HTTP and associated Uniform Resource Identifiers (URIs).

HTTP is a protocol used for communication and provides capabilities for
transport versioning, content encoding, extensibility, upgradeability,
and security.

One of the goals of RESTful architecture is to support the gradual deployment of changes within an already deployed architecture
which is why REST and HTTP fit well together.

> Finastra Open APIs use HTTP 1.1
([RFC7231](https://tools.ietf.org/html/rfc7231)) as a reference
implementation.

> Finastra Open APIs may use HTTP 2
([RFC7540](https://tools.ietf.org/html/rfc7540)).

Transport Layer Security (TLS), formerly Secure Socket Layer (SSL),
secures message communication over a computer network by encrypting the
message contents. An HTTPS transfer or API call is an HTTP call over a
connection secured by TLS.

> Finastra Open APIs **MUST** use HTTPS and TLS
> i.e. [RFC2660](https://tools.ietf.org/html/rfc2660) and
> [RFC2818](https://tools.ietf.org/html/rfc2818) are enforced

<!-- ## HTTP Methods -->

## HTTP Methods

This section details the HTTP methods used by Finastra Open APIs.

A key requirement for REST APIs is the use of a resource with
appropriate HTTP verbs.

The HTTP verbs are defined in section 4.3 of
[RFC7231](https://tools.ietf.org/html/rfc7231#section-4.3). and those
relevant to Finastra are shown in the table below.

### Summary of HTTP Methods

The following table shows the HTTP methods and their expected actions on
resources:

| HTTP Methods | Action                                                                                          |
|--------------|-------------------------------------------------------------------------------------------------|
| **POST**     | Creates a new resource                                                                          |
| **GET**      | Reads existing resource(s) - does not change the state of the resource                          |
| **PUT**      | Updates a resource                                                                              |
| **PATCH**    | Partially updates a resource (typically avoided)                                                |
| **DELETE**   | Deletes a resource                                                                              |
| **HEAD**     | Identical to **GET**, except that the server **must not** return a message body in the response |
| **OPTIONS**  | Allows the client to determine the options and/or requirements associated with a resource       |

Most Finastra APIs use the POST, GET, PUT and DELETE HTTP methods
to maintain business domain resources.

The remainder of this section details each HTTP method with reference to
the following associated characteristics which should be carefully
considered when designing the Finastra API:

-   Request Payload: specifies whether a request payload is required
-   Successful response payload: specifies whether a response payload is
    required
-   Safe: specifies whether an operation is safe; a safe operation does
    not change a resource’s representation on the server e.g. GET is
    considered safe
-   Idempotent: specifies whether an operation is idempotent; an
    operation is idempotent if it can be called several times with the
    same outcome
-   Cacheable: specifies whether an operation is cacheable; cached data
    may be used as the response to the same subsequent request

### GET

RESTful APIs use the `GET` method to obtain data from a resource. `GET` is typically included in most Finastra APIs to retrieve resource data.

**Examples**

-   `GET /customers/{id}` returns the customer identified with `{id}`.
-   `GET /customers` returns a list of all customers.

> As per the examples, Finastra APIs **SHOULD** implement `GET` methods
> against both an individual resource and its collection

> `GET` methods against a collection **SHOULD** implement pagination and
> search capabilities

**Characteristics**

-   Request Payload: No
-   Successful Response Payload: Yes
-   Safe: Yes
-   Idempotent: Yes
-   Cacheable: Yes

**Typical response status codes**

-   `200` - OK - returned when the client call is successful
-   `400` - Bad Request
-   `404` - Not Found

**Others characteristics of GET**

-   can be cached
-   saved in the browser history
-   can be bookmarked
-   has length restrictions (usually 2K)
-   should be used only to retrieve data

### POST

The `POST` method is used to create new resources. Depending on the
specific API context this can mean many things e.g. creating a
resource, executing an action, submitting long running operations,
adding an item to a list.

> **MUST** respond to POST requests with the identifier of new resources

**Example**

`POST /customers` with `{payload}`


**Characteristics**

-   Request Payload: Yes
-   Successful response payload: Yes
-   Safe: No
-   Idempotent: No
-   **POST** requests are never cached

**Typical response status code**

-   `200` - OK - returned when the client call is successful - 201 is
    preferred when resources are created using `POST`
-   `201` - Created - this is returned when a resource is created, 
    the created resource identifier is returned in the response payload.
     The resource data can optionally be returned in the response payload.
-   `400` - Bad Request - this is used for both technical and
    functional errors that did not result in a successful response
-   `409` - Conflict - this is returned when a request to create a
    resource is made and the resource already exists

### PUT

The `PUT` method is used to replace the state of an existing resource
with the new state provided in the request. It is a *full replacement*.

**Examples**

* `PUT /customers/123` + `{payload}` - this requests an update to the resource
* `PUT /customers/` + `{payload}` - this requests a bulk update


> Bulk requests **SHOULD** be avoided unless absolutely necessary;
> consider alternative designs

**Characteristics**

-   Request Payload: Yes
-   Successful response payload: No
-   Safe: No
-   Idempotent: Yes
-   Cacheable: No

**Typical response status codes**

-   `200` - OK - this **MUST** be returned when the client call is
    successful
-   `400` - Bad Request - this is used for both technical and
    functional errors that did not result in a successful response
-   `404` - Not Found

### PATCH

PATCH is used to apply partial updates to a resource.

PATCH is different to PUT in that PUT is used to entirely replace a resource whereas PATCH is used to perform a partial update to a resource.

In many scenarios PATCH may look to be a good option e.g. to update a single field in a large payload, however, be aware that:
- PATCH is not idempotent, unlike PUT
- PATCH requires a description in the requested payload of how an update should take place

> PATCH **SHOULD** be avoided as far as possible

The payload of a PATCH request describes the updates in a declarative manner and the following RFC documents describe different PATCH request formats:
- RFC 7396 (JSON Merge Patch)[https://datatracker.ietf.org/doc/html/rfc7386]
- RFC 6902 (JSON Patch)[https://tools.ietf.org/html/rfc6902] 

#### JSON Merge Patch - RFC 7396

The example in the RFC document states that given the following resource document:
```
{
	"a": "b",
	"c": {
		"d": "e",
		"f": "g"
	}
}
```
Then if the following PATCH request is received:
```
{
	"a":"z",
	"c": {
		"f": null
	}
}
```
Then this will change the value of "a" to "z" and will delete the "f" key.

JSON Merge Patch is intuitive since the payloads of the resource and the PATCH requests are similar, however, there are drawbacks to the simplicity of this approach:
- array elements cannot be modified individually, rather, the entire array is replaced
- the PATCH request payload does not have a corresponding schema that can be validated against, so malformed requests will not be rejected

> **SHOULD** use JSON Merge Patch for PATCH requests

#### JSON Patch - RFC 6902

The JSON Patch request payload format is more complex than the JSON Merge Patch format and contains a set of instructions describing how to update a resource payload. The following example taken from the RFC document shows a set of operations ("op") requested for application against a specific path ("path") in the JSON respource payload:
```
   [
     { "op": "remove", "path": "/a/b/c" },
     { "op": "add", "path": "/a/b/c", "value": [ "foo", "bar" ] },
     { "op": "replace", "path": "/a/b/c", "value": 42 },
     { "op": "move", "from": "/a/b/c", "path": "/a/b/d" },
     { "op": "copy", "from": "/a/b/d", "path": "/a/b/e" }
   ]
```
> **MAY** use JSON Merge Patch for PATCH requests

**Characteristics**

-   Request Payload: Yes
-   Successful response payload: Yes
-   Safe: No
-   Idempotent: No
-   Cacheable: No

**Typical response status codes**

-   `200` - OK - this **MUST** be returned when the client call is
    successful
-   `400` - Bad Request - this is used for both technical and
    functional errors that did not result in a successful response
-   `404` - Not Found

### DELETE

The `DELETE` method is used to remove a resource. Depending on the
context of the resource this can have different meanings e.g.:

-   remove the resource
-   flag a resource for deletion
-   remove an item from a list
-   cancel a long running operation
-   etc.

**Example**

`DELETE /customers/123467`


**Characteristics**

-   Request Payload: No - typically
-   Successful response Payload: No
-   Safe: No
-   Idempotent: Yes
-   Cacheable: No

When N DELETE requests are invoked for the same resource, the first
request deletes the resource and the response is 200 (OK) or
204 (No Content). The subsequent N-1 requests return 404 (Not
Found), hence, the N responses differ which might be seen as
non-idempotent, however, because there is no change of state for any
resource on the server side and because the original resource is
deleted by the first request, then DELETE can be deemed to be
idempotent.

**Typical response status code**

-   `200` - OK - this **MUST** be returned when the client call is
    successful
-   `204` - No content - this **MAY** be used when there is no resource
    to delete
-   `400` - Bad Request - this is used for both technical and
    functional errors that did not result in a successful response
-   `404` - Not Found

### HEAD

The `HEAD` method may be used to retrieve headers which describe a
resource or resource collection. The response contains only the headers,
it does not contain resource information. An example of the usage of a
`HEAD` request is to inform on the size of a resource before download.

**Characteristics**

-   Request Payload: No
-   Successful response Payload: No
-   Safe: Yes
-   Idempotent: Yes
-   Cacheable: Yes

### OPTIONS

The `OPTIONS` method may be used to query the resource service to obtain
a list of the allowed HTTP communication options. This method may be
used to discover which HTTP operations are supported by an API.

**Characteristics**

-   Request Payload: No
-   Successful response Payload: Yes
-   Safe: Yes
-   Idempotent: Yes
-   Cacheable: No

### Other Actions

Actions are business capabilities that do not directly map to an
existing HTTP method.

These may be anything from a simple stateless
function, without a resource representation, to complex workflows which
interact with multiple resources, for example: `POST /accounts/{id}/activate`.


### Finastra HTTP Methods Standards

The following Finastra API standards apply to HTTP methods: 

>   **MUST** use the standard GET, POST, PUT, DELETE methods against a resource
    
>   **MUST** fully consider relevant and necessary resources when using
    HTTP methods
    
>   **MUST NOT** use POST if GET can be used

>   **MUST** handle a single resource {id} in the path

>   **SHOULD NOT** use verbs within paths
    e.g. `POST /resources/{resourceId} {"status" : "active"}` rather
    than `POST /resources/{resourceId}/activate`

> **SHOULD** avoid resource-based actions where possible by using a relevant
 resource e.g. use `POST accounts/{id}` with a payload of `{ status : closed }`    

>   **SHOULD NOT** use constructs such as `GET /accounts?id=12` except
    for filtering - prefer `GET /accounts/12` for retrieval of a single
    resource
    
>   **SHOULD** ensure API operations on resources are complete in their
    CRUD scope
    
>   **SHOULD** ensure that non RESTful actions are avoided

>   **MAY** use HEAD and OPTIONS methods

## HTTP Headers

This section provides details on the standard and custom HTTP headers
that may be used by clients using Finastra Open APIs.

### Standard HTTP Headers

| Header           | Description                                                                                                                                                                                                                                                                                            |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Content-Type`   | Represents the format of the payload returned in the response. <BR>Content-type MUST be `application/json`, as a content header in response to requests that return a HTTP body i.e. all POST and GET requests.  |                                                                                     
| `Accept`         | Represents the format of the payload as input.<BR>Accept SHOULD be `application/json`.                                                                                                                                                                                                                 
| `Accept-Language`         | Represents the language and locale that a client can understand. |                                                                                                                                                                                                                
| `Authorization`  | Allows Credentials to be provided to the Authorization / Resource Server depending on the type of resource being requested.<BR> For OAuth 2.0 / OIDC, this comprises of either the Basic / Bearer Authentication Schemes. Bearer token MUST follow the [RFC6570](https://tools.ietf.org/html/rfc6750). |
| `Content-Language`         | Represents the language and locale that are returned by a server. |                                                                                                                                                                                                                
| `ETag`           | The value of this header is returned by GET methods and represents the state or version of the resource. It is used for Concurrency validation for resource updates - see the *Concurrency* section for further details.|                                                                              
| `If-Match`       | The value of this header is passed by clients on PUT methods to allow the server to perform Concurrency validation. Its value is typically an ETag value obtained from a GET method - see the *Concurrency* section for further details.                                                               |
|`Idempotency-Key` |  The value of this header is passed by clients on POST methods to allow the server to perform Idempotency validation. Its value is typically an UUID - see the *Idempotency* section for further details.|

Finastra APIs do not explicitly define `Content-Type`, `Accept` or `Authorization` as HTTP headers in the API contracts.


### Custom HTTP Headers

Custom headers **MAY** be used to add extra functionality which is
exposed via HTTP. 

All custom header usage should be confirmed with the Finastra API team who will determine
the name of the custom header based on [RFC6648](https://datatracker.ietf.org/doc/html/rfc6648) recommendations 
regarding the use of `X-` prefix. It is anticipated that most of the Finastra API custom headers will be specific to Finastra and should be prefixed with `X-`. 
The prefix `X-finastra` should not be used.


### Finastra Rules for HTTP Headers

The following Finastra standards apply to HTTP headers:

>   **MAY** use standard HTTP headers

>   **SHOULD** use Train-Case for HTTP custom header fields

>   **SHOULD**: use `ETag` with `If-Match` Header for concurrency validation

>   **SHOULD** use `Idempotency-Key` for POST requests

>   **MAY** use `X-Request-ID` for tracking requests

>   **MAY** use `X-External-Context-ID` to specify context associated with an API

>   **MAY** define a content-type for `GET` requests even though the content type is not used ​- this is for compatibility with tooling

>   **MUST** not prefix custom headers with `X-Finastra`


