GET IIIF v2 Manifest
--------------------

```
GET https://adore.ugent.be/IIIF/manifests/$ID
```

e.g. https://adore.ugent.be/IIIF/manifests/archive.ugent.be%3AE437A0E4-A5BD-11EA-A14F-3834AA36FAF6

Path parameters:

* **$ID**: url encoded identifier of the manifest

On success returns:

* IIIF v2 IIIF manifest

* HTTP response fields are set as folows:

  * Content-Type for linked data json

  * Cross Origin Resource Request fields to make CORS possible

e.g.

```
[..]
Content-Type: application/ld+json;profile="http://iiif.io/api/presentation/2/context.json";charset=utf-8
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: accept, origin, x-requested-with, content-type, x-transmission-session-id, authorization, cookie
Access-Control-Expose-Headers: X-Transmission-Session-Id
Access-Control-Allow-Methods: GET, POST, OPTIONS, X-Transmission-Session-Id
Access-Control-Allow-Credentials: true
```

On error returns:

* status 404 when not found. Content should be ignored.

* status 500 when an internal server error occurred

See also [IIIF v2 manifest](https://iiif.io/api/presentation/2.1/#manifest)

SEARCH WITHIN IIIF v2 Manifest
------------------------------

```
GET https://adore.ugent.be/IIIF/manifests/$ID/search?[$QUERY_PARAMETERS]
```

e.g. https://adore.ugent.be/IIIF/manifests/archive.ugent.be%3A3C37B88E-0BE3-11E6-9682-B54FF264C48D/search?q=vrede

Path parameters:

* **$ID**: url encoded identifier of the manifest

Query parameters:

* **q**: A space separated list of search terms

Notes:

* other query parameters like `user`, `date` or `motivation` are ignored.

* this search uses OCR data if any

On success returns:

* json-ld response of `@type` `sc:AnnotationList`

See also [IIIF Content Search API 1.0](https://iiif.io/api/search/1.0/)

GET IIIF v2 Collection
----------------------

```
GET https://adore.ugent.be/IIIF/collections/$ID
```

e.g. https://adore.ugent.be/IIIF/collections/RUG01-001643403

Path parameters:

* **$ID**: url encoded identifier of the collection

On success returns:

* IIIF v2 IIIF Collection

* HTTP response fields are set as folows:

  * Content-Type for linked data json

  * Cross Origin Resource Request fields to make CORS possible

See also [IIIF v2 collection](https://iiif.io/api/presentation/2.1/#collection)

Notes:

* the attribute `manifests` returns a static list of tuples (with `@id`, `@type`, `label` and sometimes `navDate`)

* this static list is internally managed at http://libadmin.ugent.be/settings/iiif_collection

GET IIIF v2 Collection of set
-----------------------------

```
GET https://adore.ugent.be/IIIF/sets/$SET
```

e.g. https://adore.ugent.be/IIIF/sets/eu\_topo

Path parameters:

* **$SET**: url encoded identifier of the collection

On success returns:

* IIIF v2 IIIF Collection.

Notes:

* Difference with the previous type of collection is that the results are split into pages.
  The first response does not contain the attribute `manifests`, but does report the `total` number of results, and
  adds a link `first` to the first page of results. That first page is a usable IIIF v2 Collection for viewers.
  From then on you can navigate through the results with attributes `prev` and `next`.

  cf. https://iiif.io/api/presentation/2.1/#paging

* this kind of collection is NOT meant for display in a IIIF viewer.
  It should be used as the source from which one can **discover** a set
  of manifests. Compare it to the sets in OAI-PMH.

* this kind of collection is managed at a different location as it is the result of a query against the metadata
  of the manifests. We make sure that the regular collections (above) are kept small enough (e.g. 500), but we cannot
  control the size of these kind of collections, so that is why we split it into pages. Beware that viewers
  to this day expect all manifests to be present in attribute `manifests`. Other pages will therefore never
  be visited. Use this collection to collect manifests, not for display in a viewer.


Discover manifests by search
---------------------------

```
GET https://adore.ugent.be/IIIF/manifests?[$QUERY_PARAMETERS]
```

e.g. https://adore.ugent.be/IIIF/manifests?q=belgie

Query parameters:

* **q**: space separated list of search terms

Returns:

* IIIF v2 Collection, split into pages (like previous)

Notes:

* this search is done, NOT by searching on the manifests metadata, but on its OCR data, if any.
