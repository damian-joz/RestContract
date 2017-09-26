# Discovery Search Endpoints
[Back to the list of all defined endpoints](endpoints.md)

## Search Endpoint
**/api/discover/search**   

This endpoint provides the functionality of the Discovery search screen: https://wiki.duraspace.org/display/DSDOC6x/Discovery.
It will provide detailed information on the Discovery search configuration that can be used to build complex searches.

It supports the following parameters:
* `scope`: UUID of a specific DSpace container (site, community or collection) to which the search will to be limited. If the scope has a specific DSpace configuration defined in `config/spring/api/discovery.xml`[https://github.com/DSpace/DSpace/blob/master/dspace/config/spring/api/discovery.xml#L28] that configuration will be returned. Otherwise the default configuration will be returned.
* `configuration`: The name of a Discovery configuration that should be used by this search. If the provided scope already has a specific Discovery configuration defined, than this parameter will be ignored.

The JSON response document is as follow
```json
{
  "filters": [
    {
      "filter" : "title",
      "operators": [
        {
          "operator" : "contains",
        },
        {
          "operator" : "notcontains",
        },
        {
          "operator" : "authority",
        }
      ]
    },
    {
      "filter" : "author",
      "operators": [
        {
          "operator" : "contains",
        },
        {
          "operator" : "notcontains",
        },
        {
          "operator" : "authority",
        }
      ]
    },
    {
      "filter" : "type",
      "operators": [
        {
          "operator" : "contains",
        },
        {
          "operator" : "notcontains",
        },
        {
          "operator" : "authority",
        }
      ]
    }
  ],
  "sortOptions": [
    {
      "name": "title",
      "metadata": "dc.title"
    },
    {
      "name": "dateissued",
      "metadata": "dc.date.issued"
    },
    {
      "name": "dateaccessioned",
      "metadata": "dc.date.accessioned"
    }
  ],
}
```

* filters: Provides a list of advanced search filters that can be used to limit the result set as configured in https://github.com/DSpace/DSpace/blob/master/dspace/config/spring/api/discovery.xml#L97
* operators: A list of supported operators that can be combined on each search filter.
* sortOptions: The sort options available for this query type as configured in https://github.com/DSpace/DSpace/blob/master/dspace/config/spring/api/discovery.xml#L112

Exposed links:
* objects: link to get the actual list of objects that match the search query
* facets: link to get the list of facet values and counts associated with this search query as configured in https://github.com/DSpace/DSpace/blob/master/dspace/config/spring/api/discovery.xml#L86

### Matching DSpace objects search results
**/api/discover/search/objects**

This endpoint returns a list of DSpace Objects that match the given type. The result can be refined using the following parameters:
* `query`: The discovery search string to will be used to match records.
* `dsoType`: Limit the search to a specific DSpace Object type:
     * all: Execute a query over all available DSO types.
     * item: Only search the DSpace items.
     * community: Search within the DSpace community records.
     * collection: Limit the search to DSpace collection records.
* `scope`: UUID of a specific DSpace container (site, community or collection) to which the search has to be limited, e.g. `scope=9076bd16-e69a-48d6-9e41-0238cb40d863`.
* `configuration`: The name of a Discovery configuration that should be used by this search. If the provided scope already has a specific Discovery configuration defined, than this parameter will be ignored.
* `f.<:filter-name>=<:filter-value>,<:filter-operator>`: Advanced search filter that has to be used to filter the result set. The `filter-name` and `filter-operator` must match a value returned by parent search endpoint (see above). For example `f.author=5df05073-3be7-410d-8166-e254369e4166,authority` or `f.title=rainbows,notcontains`.
* `page`, `size` & `sort` [see pagination](README.md#Pagination): the sort name must match a value returned by the parent search endpoint (see above) or *default*, followed by a comma and the order direction. For example `sort=default,asc` or `sort=dateissued,desc`.

Example: TODO

The returned JSON response will be like:

```json
{
  "query":"my query",
  "scope":"9076bd16-e69a-48d6-9e41-0238cb40d863",
  "appliedFilters": [
      {
        "filter" : "title",
        "operator" : "notcontains",
        "value" : "abcd",
        "label" : "abcd"
      },
      {
        "filter" : "author",
        "operator" : "authority",
        "value" : "1234",
        "label" : "Smith, Donald"
      }
  ],
  "sort" : {
    "by" : "dateissued",
    "order" : "asc"
  },
  "page": {
    	"size": 5,
    	"totalElements": 14,
    	"totalPages": 3,
    	"number": 0
  },
  "_embedded" : {
    "searchResults" : [
      {
        "hitHighlights": {
          "dc.description.abstract" : "This is the <em>very cool</em> abstract of this item",
          "dc.publisher" : "My <em>very cool</em> publisher",
        },
        "_links" : {
          "dspaceObject" : {
            "href": "/api/core/items/9f3288b2-f2ad-454f-9f4c-70325646dcee"
          }
        },
        "_embedded" : {
          "dspaceObject" : {
            "uuid": "9f3288b2-f2ad-454f-9f4c-70325646dcee",
            "name": "Test Webpage",
            "handle": "10673/4"
          }
        }
      },
      {
        "hitHighlights": { },
        "_links" : {
          "dspaceObject" : {
            "href": "/api/core/items/ff7ec3a4-0aab-418b-94fc-d0e8189084db"
          }
        },
        "_embedded" : {
          "dspaceObject" : {
            "uuid": "ff7ec3a4-0aab-418b-94fc-d0e8189084db",
            "name": "Test Item with no hit highlights",
            "handle": "10673/5"
          }
        }
      }
    ],
    "facets" : [
      {
        "name" : "author",
        "_embedded" : {
          "values" : [
              {
                "value" : "Smith, Donald 2",
                "count" : 100,
                "_links": {
                  "search" : "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&f.author=Smith,+Donald+2,equals"
                }
              },
              {
                "value" : "Smith, Donald 1",
                "count" : 80,
                "_links": {
                  "search" : "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&f.author=Smith,+Donald+1,equals"
                }
              },
              {
                "value" : "Smith, Donald 3",
                "count" : 10,
                "_links": {
                  "search" : "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&f.author=Smith,+Donald+3,equals"
                }
              }
          ]
        },
        "_links" : {
          "self": {
            "href": "/api/discover/facets/author?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&page=0&size=5"
          }
        }
      },
      {
        "name" : "subject",
        "_embedded" : {
          "values" : [
              {
                "value" : "Java",
                "count" : 100,
                "_links": {
                  "search" : "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&subject.equals=Java"
                }
              },
              {
                "value" : "SQL",
                "count" : 80,
                "_links": {
                  "search" : "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&subject.equals=Java"
                }
              },
              {
                "value" : "CSS",
                "count" : 10,
                "_links": {
                  "search" : "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&subject.equals=Java"
                }
              }
          ]
        },
        "_links" : {
          "self": {
            "href": "/api/discover/facets/subject?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&page=0&size=5"
          }
        }
      }
    ]
  },
  "_links": {
      "first": {
        "href": "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&page=0&size=5"
      },
      "self": {
        "href": "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&page=0&size=5"
      },
      "next": {
        "href": "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&page=1&size=5"
      },
      "last": {
        "href": "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&page=2&size=5"
      }
  }
}
```

### Matching facet search results
**/api/discover/facets**

Provide access to the Discovery facet system (SOLR based). This endpoint returns the list of available facet fields that can be queried. The endpoint supports the following parameters:
* `scope`: UUID of a specific DSpace container (site, community or collection) to which the search will to be limited. If the scope has a specific DSpace configuration defined in `config/spring/api/discovery.xml`[https://github.com/DSpace/DSpace/blob/master/dspace/config/spring/api/discovery.xml#L28] that configuration will be returned. Otherwise the default configuration will be returned.
* `configuration`: The name of a Discovery configuration that should be used by this search. If the provided scope already has a specific Discovery configuration defined, than this parameter will be ignored.

The list of returned facet fields will depend on the Discovery configuration: https://github.com/DSpace/DSpace/blob/master/dspace/config/spring/api/discovery.xml#L86

The JSON response document is as follow
```json
{
  "facets": [
    {
      "name" : "author",
      "type" : "string"
    },
    {
      "name" : "dateissued",
      "type" : "date"
    },
    {
      "name" : "subject",
      "type" : "string"
    }
  ]
}
```

### List values of a certain facet
**/api/discover/facets/<:facet-name>**

This endpoint returns a list of values that correspond to the given facet name. The result can be refined using the following parameters:
* `query`: The discovery search string to will be used to match records.
* `scope`: UUID of a specific DSpace container (site, community or collection) to which the search has to be limited, e.g. `scope=9076bd16-e69a-48d6-9e41-0238cb40d863`.
* `f.<:filter-name>=<:filter-value>,<:filter-operator>`: Advanced search filter that has to be used to filter the result set. The `filter-name` and `filter-operator` must match a value returned by parent search endpoint (see above). For example `f.author=5df05073-3be7-410d-8166-e254369e4166,authority` or `f.title=rainbows,notcontains`.
* `page`, `size` & `sort` [see pagination](README.md#Pagination): the sort name be "count" (results ordered descending by the number of matching records) or "index" (results order alphabetically).

Example: TODO

The returned JSON response will be like:

```json
{
  "query":"my query",
  "scope":"9076bd16-e69a-48d6-9e41-0238cb40d863",
  "appliedFilters": [
      {
        "filter" : "title",
        "operator" : "notcontains",
        "value" : "abcd",
        "label" : "abcd"
      },
      {
        "filter" : "author",
        "operator" : "authority",
        "value" : "1234",
        "label" : "Smith, Donald"
      }
  ],
  "sort" : {
    "by" : "index"
  },
  "page" : {
    	"size": 5,
    	"totalElements": 14,
    	"totalPages": 3,
    	"number": 0
  },
  "name" : "author",
  "type" : "string",
  "_embedded" : {
    "values" : [
        {
          "value" : "Smith, Donald 2",
          "count" : 100,
          "_links": {
            "search" : "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&f.author=Smith,+Donald+2,equals"
          }
        },
        {
          "value" : "Smith, Donald 1",
          "count" : 80,
          "_links": {
            "search" : "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&f.author=Smith,+Donald+1,equals"
          }
        },
        {
          "value" : "Smith, Donald 3",
          "count" : 10,
          "_links": {
            "search" : "/api/discover/search/objects?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&f.author=Smith,+Donald+3,equals"
          }
        }
    ]
  },
  "_links": {
      "first": {
        "href": "/api/discover/facets/author?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&page=0&size=5"
      },
      "self": {
        "href": "/api/discover/facets/author?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&page=0&size=5"
      },
      "next": {
        "href": "/api/discover/facets/author?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&page=1&size=5"
      },
      "last": {
        "href": "/api/discover/facets/author?query=my+query&scope=9076bd16-e69a-48d6-9e41-0238cb40d863&f.title=abcd,notcontains&f.author=1234,authority&page=3&size=5"
      }
  }
}
```