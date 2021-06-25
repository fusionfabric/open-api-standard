---
layout: default
title: Collections and Pagination
nav_order: 8
---
- TOC
{:toc}

# Collections and Pagination

This section details the following topics regarding collection handling
and pagination:

-   Collections
-   Searching and Filtering
-   Sorting
-   Pagination Request
-   Pagination Response

## Collections

as part of a CRUD API the R(ead) part of it should contain 2 endpoints : 
- GET /resources/{resourceId} return a unique instance of the designated resource. 
- GET /resources   => returning a collection of items

The second endpoint can be enriched by adding query parameter to filter the resource collection.
Usually this is about filtering based on the content (search) , or the size of the result (pagination)


When a Finastra API provides a `GET` endpoint that returns a collection
e.g. GET /accounts, the API should provide a response object that is
extensible, hence, a collection should be returned as an object
containing an array of items - for example:

``` notoggle
GET /accounts 
```

``` notoggle
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

> Finastra APIs supporting `GET` collections **SHOULD** return the
> collection as an Object containing an Array of items because this
> approach can extend to support meta data without breaking changes

>  GET collections **SHOULD** support extensibility using an object with the `items` keyword.

>  items **SHOULD** be an 'Object' to ensure extensibility of the model.

> When 'GET' query does return no elements, server **MUST** return a `200` response code with an empty collection. (NB : not a `404`) 



## Searching and Filtering

When a Finastra API provides a `GET` endpoint that returns a collection
the API should provide search and filter capabilities to avoid
performance issues when a potentially large collection are returned.

Search and filter capabilities can be supported using:

1 - basic mechanism - with query parameters specified in the url which
contain the resource’s filter field name and filter field value

2 - advanced mechanism - as per the basic mechanism but additionally
implementing a query language to allow more advanced search capabilities
e.g. using query languages similar to:

-   [RSQL](https://github.com/jirutka/rsql-parser)
-   [FIQL](https://tools.ietf.org/html/draft-nottingham-atompub-fiql-00)
-   [GraphQL](https://graphql.org/)
-   [OData](https://www.odata.org/)

**Examples**

``` notoggle
* GET /accounts?name=tom&lastname=smith     = > basic using query parameter
* GET /accounts/search/mostActive           = > well known search
* GET /accounts/search/balance=lt=0         = > using FIQL
```

**Finastra API Search and Filter Standards**

> Support for searching and filtering **SHOULD** be provided using GET
> and query parameters to allow searches to be bookmarked. It is the recommended approach

> RSQL, FIQL syntax **MAY** be used for the GET on the search
> resource

> The choice of technology **MUST** be based on functional requirements.

> GraphQL, and OData  **SHOULD NOT** be the default choice for API style

> if a query parameter is considered as a PII, a POST **SHOULD** be used instead. 



## Sorting

When a Finastra API provides a `GET` endpoint that returns a collection
the API should provide sort capabilities as described in this section.

> Finastra APIs containing resource collections **SHOULD** support sort
> capability.

> Finastra APIs supporting sort **MUST** use the **sort** path keyword
> with `asc` and `desc` parameters where `desc` means reverse order and
> `asc` means ascending order, for example:

``` notoggle
($propertyname,)+[asc|desc]
```

> Finastra APIs supporting sort **MUST** specify the default sort
> sequence in API documentation.

> Finastra APIs supporting sort **MUST** specify the maximum number of
> sort terms.

**Examples**

-   Sort by `id`, in ascending order:

``` notoggle
GET /accounts?sort=id  
```

-   Sort by `openDate`, in descending order:

``` notoggle
GET /accounts?sort=openDate+desc
```

-   Sort by `openDate`, in descending order, and then by `name`, in
    ascending order

``` notoggle
GET /accounts?sort=openDate+desc,name+asc
```

## Pagination Request

Pagination is about reducing the amount of data returned by creating chunk of data , ie page. 
Pagination requires so a dedicated protocol to navigate and define pages


Pagination should be introduced early in an API’s lifecycle because
pagination typically introduces breaking changes. In addition, if
pagination is not introduced the API may list an entire collection of a
resource which may represent an SLA risk, a security risk, a resiliency
concern etc.

> Finastra APIs with resource collections **SHOULD** support pagination
> in all but exceptional circumstances.


When a Finastra API provides a `GET` endpoint that returns a collection
the API should provide capabilities to allow the client to page through
the collection.

There are two commonly used pagination techniques:

-   Offset-Limit Based pagination - this uses a numeric offset to
    identify the first item in a set of results - this is the most
    widely used pagination mechanism

-   Cursor Based (Key-Based) pagination - this uses a unique key to
    identify the first item in a set of results - this is typically used
    for data that changes frequently

> pagination **MUST** be implemented by offset / limit.

In Offset-Limit Based pagination:

-   the **limit** parameter controls the maximum number of items that
    may be returned for a single request

-   the **offset** parameter controls the starting point within the
    collection of results, for example, if a collection of 15 items is
    to be retrieved from a resource and a limit=5 is specified then the
    set of results can be retrieved in 3 requests by varying the offset
    value: `offset=0`, `offset=5`, and `offset=10`

As limit value depends on the nature of the resource, three is no by default global recommendation. 

> limit value **SHOULD** document a default value.


**Examples**

``` notoggle
* `GET /accounts?limit=`100`  => return the 100 first accounts 
* `GET /accounts?offset=100`  => return accounts from 101 
```

> Finastra APIs supporting pagination **SHOULD** be implemented using
> the **limit** and **offset** request keywords

## Pagination Response

This section provides details on the contents of the response object for
a collection of items and includes the following:

-   Return Items - as an Object containing an Array of items
-   Return Metadata - with pagination navigation information
-   Return Links - with associated relevant links


``` notoggle
{
  items : [ {} , {} ] 
  "_meta" : {}
  "_links" : {}
}
```


### Return Metadata

By returning *items* as an object the response can be extended, without
breaking changes to the API, to return an object named `_meta`
containing metadata associated with the collection.

The following example shows the standard Finastra pagination metadata
response fields:

``` notoggle
{
"_meta" :
  {
    "limit" : 5         // maximum number of items returned  
    "offset" : 60       // starting point of items returned 
    "itemCount" : 3     // total number of items returned
    "totalCount" : 63   // total number of items 
  }
}
```

The `_meta` object can be extended to include any relevant metadata
as required e.g. computation time etc.

The following example shows *items* and `_meta` in a typical response
object:

``` notoggle
GET /accounts?limit=5&offset=60
```

``` notoggle
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

> Finastra APIs supporting GET collections **SHOULD** support the
> provision of metadata using the `_meta` keyword

> `_meta` object **MAY** be used in others context than pagination. 

> `_meta` object **MAY** be extended with additional meta data 

### Return Links

When a Finastra API provides a `GET` endpoint that returns a collection
the API should provide the client with the following links to navigate
the collection:

-   **self** - this returns the current set of items in the
    collection
-   **next** - this link returns the set of items in the collection
    after the last entry of the current set of items
-   **prev** - this link returns the set of items in the collection
    before the first item of the current set of items
-   **first** - this link returns the first set of items in the
    collection
-   **last** - this link returns the last set of items in the
    collection

All items are based on the passed limit and offset parameters.

Note that links can also be provided for GET operations that return a
single item rather than a collection - see the *HATEOAS and HAL* section
for further details.

The following example shows a Swagger snippet containing the standard
definition of links:

``` notoggle
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

``` notoggle
GET /accounts?limit=5&offset=60
```

``` notoggle
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
  "_links": {
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
  }
}
```

Note that in the example, since the last 3 items are shown based on the
request parameters then the “next” link is omitted from the response.

> Finastra APIs supporting GET collections **SHOULD** support navigation
> using the `_links` keyword

