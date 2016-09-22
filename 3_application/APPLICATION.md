# Quiz Part 3: Application

Using a framework of your choice (we'd prefer angular or React), create a minimal
search page. You will hit our API to do a search for spaces and display a list of
building results on the page.

You can think of this as a mini version of the [search page on RealMassive](https://www.realmassive.com/search).
A user should be able to update filters and get new results.

Please create this as if it were going to be dropped into our production
application, so use current best practices and include tests!

Specifications:

- The app loads a list of buildings, including information about their title,
address, and cover photo
- The list can be paginated
- The list can be updated by using two filters (see **Filtering**)

## The RealMassive API

The API endpoint you will be interacting with is:

`https://api.realmassive.com/`

In order to construct the listing of buildings, you will need to pull buildings
and their images. Our schema and API uses three different entity types to represent
this information:

- Building - an entity representing buildings
- Media - an entity representing some file (image, video, pdf) that can be displayed
- Attachments - an entity that sits between Media and other entity types (for this
app, Buildings), and contains a category field. In order to find a building's
cover photo, you will need to check for attachments that have a category attribute
of "cover".

So to get information about a building and its associated cover photos, you must
request the building, then that building's attachments, and then those attachments'
media entities. There are shortcuts to make this process require fewer API calls.

Requesting entities with our API in a search-style is as simple as making a GET
request to these endpoints:

- Buildings: `https://api.realmassive.com/buildings`
- Media: `https://api.realmassive.com/media`
- Attachments: `https://api.realmassive.com/attachments`

Here's an example payload for a building:

```
{
    "data": [
        {
          "attributes": {
            "address": {
              "city": "Austin",
              "county": "Travis",
              "latitude": 30.3708516,
              "longitude": -97.7384657,
              "state": "TX",
              "street": "8500 Shoal Creek",
              "zipcode": "78757"
            },
            "air_conditioned": false,
            "building_class": "A",
            "building_size": {
              "units": "sqft",
              "value": "1766.000000"
            },
            "building_type": "office",
            "created": "2014-12-01T21:26:25.184390+00:00",
            "deleted": false,
            "leed_rating": "none",
            "sprinkler": "No",
            "title": "Shoal Creek Office Condo IV",
            "updated": "2016-07-02T10:02:34.588700+00:00"
          },
          "id": "1295036113995433905",
          "relationships": {
            "attachments": {
              "links": {
                "related": "/buildings/1295036113995433905/attachments"
              }
            },
            "spaces": {
              "links": {
                "related": "/buildings/1295036113995433905/spaces"
              }
            }
          },
          "type": "buildings"
        },
        ...
    ],
    "meta": {
    	"count": <number of responses>
	}
}
```

In order to request an entities' associated entities in the same request as the
request for the original entities, you can use the `include` GET parameter. For
instance, the following GET request would return a response that includes the
attachments associated with a building:

`https://api.realmassive.com/buildings?include=attachments`

The response would look like:

```
{
    "data": [
        {
          "attributes": {
            "address": {
              "city": "Austin",
              "county": "Travis",
              "latitude": 30.3708516,
              "longitude": -97.7384657,
              "state": "TX",
              "street": "8500 Shoal Creek",
              "zipcode": "78757"
            },
            "air_conditioned": false,
            "building_class": "A",
            "building_size": {
              "units": "sqft",
              "value": "1766.000000"
            },
            "building_type": "office",
            "created": "2014-12-01T21:26:25.184390+00:00",
            "deleted": false,
            "leed_rating": "none",
            "sprinkler": "No",
            "title": "Shoal Creek Office Condo IV",
            "updated": "2016-07-02T10:02:34.588700+00:00"
          },
          "id": "1295036113995433905",
          "relationships": {
            "attachments": {
              "data": [
                {
                  "id": "1295036602447299981",
                  "type": "attachments"
                }
              ],
              "links": {
                "related": "/buildings/1295036113995433905/attachments"
              }
            },
            "spaces": {
              "links": {
                "related": "/buildings/1295036113995433905/spaces"
              }
            }
          },
          "type": "buildings"
        },
        ...
    ],
    "included": [
        {
          "attributes": {
            "created": "2016-07-15T16:33:35.041552+00:00",
            "deleted": false,
            "updated": "2016-07-15T16:33:35.041552+00:00"
          },
          "id": "1295036602447299981",
          "type": "attachments"
        },
        ...
    ],
    "meta": {
	    "count": <number of responses left (does not address included)>
	}
}
```

So with one request, you now have all of the building entities and all of the
attachment entities. You cannot yet do a "nested include" to get the media at
the same type, so you will need to make another request for the specific attachments
(whose IDs you now have) and use the `include` filter to get those attachments'
related media entities.

You can request entities with specific IDs by using the `filter[where][id]` GET
parameter:

`https://api.realmassive.com/attachments?filter[where][id]=1295036602447299981,1295036602447299982,1295036602447299984&include=media`

You can include as many IDs will fit in the valid max length for a URL request.

### Filtering

We just saw an example of a filter.

Your application needs to include two filters:

 - building_size.value - space available SF (range)(default - None)
 - building_type - building type(s) (comma delimited list)(valid values: 'industrial', 'office', 'retail')

These filters can be added in a similar way, using the following syntax:

- Direct attribute value match: `filter[where][<parameter>]=value`
- Numerical comparison match: `filter[where][<parameter>][gt|lt|ge|le|eq]=value`

You can use the `filter` param multiple times. For instance, to only request attachments
that have a `category` property that is `"cover"`, you can add `filter[where][category]="cover"`:

`https://api.realmassive.com/attachments?filter[where][category]=cover&filter[where][id]=1295036602447299981,1295036602447299982,1295036602447299984&include=media`

You can also specify a range for a numerical value by using the numerical comparison -
use both `filter[where][<parameter>][gt]` and `filter[where][<parameter>][lt]`.

### Pagination

To offer **pagination** you will append a `page[offset]` and `page[limit]` to the url. The example below gets the next 100 items, starting at the 100th item in the database:

```
https://api.realmassive.com/buildings?page[offset]=100&page[limit]=100
```

Note that the `meta.count` property will update to include the number of entities
left _after_ the offset you provided. So if on a plain request to `/buildings`,
the `meta.count` is `5500`, a request with `?page[offset]=100` will return a
`meta.count` of `5400`. Note that the `meta.count` being the number of entities
_after_ the provided offset means that the count also includes the entities you
receive on the current request.
