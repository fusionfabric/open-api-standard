---
layout: default
title: Collections and Pagination
nav_order: 9
---
# Collections and Pagination
{: .no_toc}

This section details the following topics regarding collection handling
and pagination:

- TOC
{:toc}

## Sample

The following link shows a sample of an OAS2 API that contains an example of collection and pagination endpoint - the `GET /parties` - which can be used as a reference for this section:

- [Sample OAS2 Pagination API](https://github.com/fusionfabric/open-api-standard/blob/main/pagination-sample.yml) 

## Collections

APIs typically provide Create, Read, Update, Delete (CRUD) capabilities against a business resource e.g. CRUD accounts.

The R(ead) business resource should contain the following two endpoints : 
- `GET /resources/{resourceId}` => return a unique instance of the designated resource
- `GET /resources`   => return a collection of items

The second endpoint can be enriched by adding query parameters to filter the resource collection based on the content (search) or the size of the result (pagination).

When a Finastra API provides a `GET` endpoint that returns a collection
e.g. `GET /accounts`, the API should provide a response object that is
extensible, hence, a collection should be returned as an object
containing an array of items e.g.:

```
{
  "items": [
    {
      "id": "0001123467001",
      "name": "Current GBP",
      "uri": "/accounts/0001123467001"
    },
    {
      "id": "0001123467002",
      "name": "Current EUR",
      "uri": "/accounts/0001123467002"
    },
    {
      "id": "0001123467003",
      "name": "Current USD",
      "uri": "/accounts/0001123467003"
    }
  ]
}
```

### Finastra API Collections Standards ###

> Finastra APIs supporting `GET` collections **SHOULD** support extensibility by returning the
collection as an object containing an array of `items` because this approach can be extended
to support additional data without imposing breaking changes on the API

> `GET` collections **SHOULD** name the array as `items`

> Finastra APIs supporting `GET` collections **MUST** return a `200` (not a '404') response code when a `GET` query returns an empty collection. Note that a `200` response is preferred to a `204` response for an empty collection


## Searching and Filtering

When a Finastra API provides a `GET` endpoint that returns a collection
the API should provide search and filter capabilities to avoid
performance issues when a potentially large collection is returned.

Search and filter capabilities can be supported using:

1 - basic mechanisms 

- where query parameters are specified in the url which contains the resource's filter field name and filter field value
- where well known commonly used searches have a dedicated endpoint 

2 - advanced mechanism - this is similar to the basic mechanism but additionally
implements a query language to allow more advanced search capabilities
e.g. using query languages similar to:

-   [RSQL](https://github.com/jirutka/rsql-parser)
-   [FIQL](https://tools.ietf.org/html/draft-nottingham-atompub-fiql-00)
-   [GraphQL](https://graphql.org/)
-   [OData](https://www.odata.org/)

Examples:
|  **Request**                          | **Description**                   |
|-------------------------------------------|-----------------------------------|
| `GET /accounts?name=tom&lastname=smith` | Basic search using query parameter|
| `GET /accounts/search/mostActive`       | Well known search|
| `GET /accounts/search/balance=lt=0 `    | using FIQL|


### Finastra API Search and Filter Standards ###

> Support for searching and filtering **SHOULD** be provided using `GET`
> and query parameters to allow searches to be bookmarked - this is the recommended approach

> when a query parameter is considered as classified data (personally identifiable information - PII), `POST` **SHOULD** be used instead of `GET` so that the query parameters are contained in the `POST` body rather than the url path

> RSQL, FIQL syntax **MAY** be used for the `GET` on the search resource

> The choice of technology **MUST** be based on functional requirements

> GraphQL, and OData **SHOULD NOT** be the default choice for API style


## Sorting

When a Finastra API provides a `GET` endpoint that returns a collection
the API should provide sort capabilities as described in this section.

**Examples**

-   Sort by `id`, in ascending order: 
    -  `GET /accounts?sort=id`
-   Sort by `openDate`, in descending order:
     - `GET /accounts?sort=openDate+desc`
-   Sort by `openDate`, in descending order, and then by `name`, in ascending order:
     - `GET /accounts?sort=openDate+desc,name+asc`

### Finastra API Sorting Standards ###

> Finastra APIs containing resource collections **SHOULD** support sort
> capability.

> Finastra APIs supporting sort **MUST** use the **sort** path keyword
> with `asc` and `desc` parameters where `desc` means reverse order and
> `asc` means ascending order, for example: `($propertyname,)+[asc|desc]`

> Finastra APIs supporting sort **MUST** specify the default sort
> sequence in API documentation

> Finastra APIs supporting sort **MUST** specify the maximum number of
> sort terms


## Pagination

Finastra APIs should use pagination to reduce the volume of data returned from a collection request.

For example if 'GET /accounts' were to return millions of records then this would 
most likely result in an SLA risk, a security risk, a resiliency
concern etc., hence, implementing pagination will allow 
the server to return one page of data at a time based on the client's request

Pagination should be introduced early in an API’s lifecycle because
pagination typically introduces breaking changes. 

When a Finastra API provides a `GET` endpoint that returns a collection
the API should provide capabilities to allow the client to page through
the collection.

There are two commonly used pagination techniques:

-   Offset-Limit Based pagination - this uses a numeric offset to
    identify the first item in a set of results - this is the most
    widely used pagination mechanism and is the recommended pagination
    technique for Finastra APIs

-   Cursor Based (Key-Based) pagination - this uses a unique key to
    identify the first item in a set of results - this is typically used
    for data that changes frequently

In Offset-Limit Based pagination:

-   the `limit` parameter controls the maximum number of items that
    may be returned for a single request. The `limit` value depends on
    the nature of the resource so there is not a default recommendation

-   the `offset` parameter controls the starting point within the
    collection of results, for example, if a collection of 15 items is
    to be retrieved from a resource and a limit=5 is specified then the
    set of results can be retrieved in 3 requests by varying the offset
    value: `offset=0`, `offset=5`, and `offset=10`

Examples:
|  **Request**                          | **Description**     |
|-------------------------------|-------------------|
| `GET /accounts?limit=100` | return the 100 first accounts |
| `GET /accounts?offset=100`     | return accounts from 101 |

### Finastra API Pagination Request Standards ###

> Finastra APIs with resource collections **SHOULD** support pagination
> in all but exceptional circumstances

> Finastra APIs **SHOULD** implement pagination using offset-limit

> Finastra APIs supporting pagination **SHOULD** be implemented using
> the `limit` and `offset` request keywords

> Finastra APIs implementing pagination **MUST** specify a `default` value for the `limit` parameter

## Pagination Response

This section provides details on the contents of the response object for
a collection of items and includes the following:

-   Response Items - as an Object containing an Array of items
-   Response Metadata - with pagination navigation information
-   Response Links - with associated relevant links

### Response Items

When a Finastra API provides a `GET` endpoint that returns a collection
the API should provide a response object that is
extensible, hence, a collection should be returned as an object
containing an array of items e.g.:

```json
{
  "items": [
    {},
    {}
  ],
  "_meta": {},
  "_links": {}
}
```

### Response Metadata

By returning a collection's `items` as an object, the response can be extended without
breaking changes to the API, for example, an object named `_meta`
containing metadata associated with the collection can be added to the response.

The following example shows the standard Finastra pagination metadata
response fields:

```json
{
  "_meta": {
    "limit": 5,
    "offset": 60,
    "itemCount": 3,
    "totalCount": 63
  }
}
```

where:
-    `limit` is the maximum number of items returned  
-    `offset`is the starting point of items returned 
-    `itemCount` is the  number of items returned
-    `totalCount` is the total number of items 

The `_meta` object can be extended to include any relevant metadata
as required e.g. computation time etc.

The following example shows `items` and `_meta` in a typical response
object:

`GET /accounts?limit=5&offset=60`

```json
{
  "items": [
    {
      "id": "0543123467009",
      "name": "Current GBP",
      "uri": "/accounts/0543123467009"
    },
    {
      "id": "0543123467010",
      "name": "Current EUR",
      "uri": "/accounts/0543123467010"
    },
    {
      "id": "0543123467083",
      "name": "Savings GBP",
      "uri": "/accounts/0543123467083"
    }
  ],
  "_meta": {
    "limit": 5,
    "offset": 60,
    "itemCount": 3,
    "totalCount": 63
  }
}
```

### Response Links ###

When a Finastra API provides a `GET` endpoint that returns a collection
the API should provide the client with the following links to navigate
the collection:

-   `self` - this returns the current set of items in the
    collection
-   `next` - this link returns the set of items in the collection
    after the last entry of the current set of items
-   `prev` - this link returns the set of items in the collection
    before the first item of the current set of items
-   `first` - this link returns the first set of items in the
    collection
-   `last` - this link returns the last set of items in the
    collection

All items are based on the requested `limit` and `offset` parameters.

Note that links can also be provided for `GET` operations that return a
single item rather than a collection - see the *HATEOAS and HAL* section
for further details.

The following example shows a Swagger snippet containing the standard
definition of links:

```yaml
  PaginationLinks:
    type: object
    required:
      - self
    properties:
      next:
        $ref: '#/definitions/GenericLink'
      last:
        $ref: '#/definitions/GenericLink'
      prev:
        $ref: '#/definitions/GenericLink'
      self:
        $ref: '#/definitions/GenericLink'
      first:
        $ref: '#/definitions/GenericLink'

  GenericLink:
    type: object
    description: Hypertext reference
    required:
      - href
    properties:
      href:
        type: string
        description: URI to be used
```

The following example shows how *items* and `_meta` and `_links`
can be used together:

`GET /accounts?limit=5&offset=60`

```json
{
  "items": [
    {
      "id": "0543123467009",
      "name": "Current GBP",
      "uri": "/accounts/0543123467009"
    },
    {
      "id": "0543123467010",
      "name": "Current EUR",
      "uri": "/accounts/0543123467010"
    },
    {
      "id": "0543123467083",
      "name": "Savings GBP",
      "uri": "/accounts/0543123467083"
    }
  ],
  "_meta": {
    "limit": 5,
    "offset": 60,
    "itemCount": 3,
    "totalCount": 63
  },
  "_links": [
    {
      "self": "/accounts?limit=5&offset=60"
    },
    {
      "first": "/accounts?limit=5&offset=0"
    },
    {
      "previous": "/accounts?limit=5&offset=55"
    },
    {
      "last": "/accounts?limit=5&offset=60"
    }
  ]
}
```

Note that in the example, since the last 3 items are shown based on the
request parameters then the `next` link is omitted from the response.


### Finastra API Pagination Response Standards ###

> Finastra APIs supporting `GET` collections **SHOULD** support the
> provision of metadata using the `_meta` keyword

> `_meta` object **MAY** be used in others context than pagination. 

> `_meta` object **MAY** be extended with additional meta data 


> Finastra APIs supporting `GET` collections **SHOULD** support navigation
> using the `_links` keyword
