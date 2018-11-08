This specification is a *Working Draft*. Issues and pull requests very much welcome.  A [discussion paper](https://docs.google.com/document/d/14Zb5ZMYlLhhjf0c5JqhI7GxZ0umaa2qtdvTweGDmUik/edit#heading=h.2jf16ci1nr0r) is available and any additional comments are also welcome.

Open Contracting Data Standard API specification
================================================

The [Open Contracting Data Standard](http://standard.open-contracting.org) (OCDS) provides schema for publishing releases and records about contracting processes. It supports publication of multiple releases and records in bulk ‘packages’, or as individual files, accessible at their own URIs.

Version 1.0 of OCDS provided [basic guidance](http://standard.open-contracting.org/latest/en/implementation/hosting/) on how to host access to data, but did not specify a detailed API.

In order to make OCDS data more accessible than supplying large files of release/record packages, some publishers may want to produce an API of their data. However, if each publisher produces their own unique API then it will be difficult for the data consumers to gather data from various sources.  


Core Principles
---------------

* Make the API as simple as possible to create for publishers.
* For the simplest of cases allow publishing release/record packages as an acceptable minimal API.
* For large files make sure the data is accessible in smaller chunks.
* Give standard approaches for doing basic filtering and sorting.
* Try and make urls readable as possible.
* Try and reuse as many techniques from other API standards.


Definitions
-----------

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED",  "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://tools.ietf.org/html/rfc2119).

* **BaseURL** The base URL of the API. This will be the web address where the API lives. All API calls are below that point. e.g http://www.example.com/ocds-api
* **Base Files** The files/endpoints that live at **BaseURL/releases.json** or **BaseURL/records.json**. One of these MUST be present in all APIs.


Single File API type (publishing an individual package)
---------------------------------------------------

A publisher has just a single release/record package file. The releases and records in these packages SHOULD be ordered by last modified date descending.

These Base Files MUST be hosted at:

* **BaseURL/releases.json**  i.e [Release Single file](https://raw.githubusercontent.com/open-contracting/api-specification/master/single-file-api/releases.json)
* **BaseURL/records.json**   i.e [Record Single File](https://raw.githubusercontent.com/open-contracting/api-specification/master/single-file-api/records.json)

They MUST contain valid OCDS release/record packages.

The above urls link to real examples.  So an example Single File API can be found at https://github.com/open-contracting/api-specification/tree/master/single-file-api



Multiple File API type (publishing many packages)
-------------------------------------------------

This will be used if a publisher wants to host many package files but wants to host them statically (without the need for any dynamic program hosting of data).

A releases.json or a records.json MUST exist at the root of the API:

* **BaseURL/releases.json**  i.e [Release Multiple file](https://raw.githubusercontent.com/open-contracting/api-specification/master/multiple-file-api-all/releases.json)
* **BaseURL/records.json**

The above urls link to real examples. So an example Multiple File API can be found at https://github.com/open-contracting/api-specification/tree/master/multiple-file-api-all/

These Base Files MUST contain an root object with a "links" object which MUST contain either an "all" property OR a "next" property. The "links" property is very similar to what is outlined in the [JSON API standard on pagination](http://jsonapi.org/format/#fetching-pagination).

For example, a Base File with an "all" property:

```
{ "links":
  {"all": ["https://raw.githubusercontent.com/open-contracting/api-specification/master/multiple-file-api-all/releases-2015.json",
           "https://raw.githubusercontent.com/open-contracting/api-specification/master/multiple-file-api-all/releases-2014.json",
           "https://raw.githubusercontent.com/open-contracting/api-specification/master/multiple-file-api-all/releases-2013.json"]}
}
```

or with a "next" property:
```
{ "links":
  {"next": "https://raw.githubusercontent.com/open-contracting/api-specification/master/multiple-file-api-next/releases-2015.json"}
}
```
An example Multiple File API using the next property can be found at https://github.com/open-contracting/api-specification/tree/master/multiple-file-api-next/

The "all" property MUST be a list and MUST contain a list of all release/record package URLs published.

The "next" property MUST be a string and MUST contain a URL of another release/record package.

The BASE File can contain its own releases/records and if so MUST conform to the OCDS release/record package specification.

When using the "next" property the package this is pointing to SHOULD also contain its own "links" object with its own "next" property to point to another package if it exists and not be there if it does not.

When using the "next" property every release/record package published MUST be discoverable by looking at the next properties in sequence in each of the release/record packages.

When using the "all" property every release/record package published MUST be listed in the Base Files.

The package files apart from Base Files MUST NOT have an "all" property in the links object.


Full API type (publishing a programmatic API)
--------------------------------------------

This is defined by a [Swagger](http://swagger.io/specification/) definition file which will be maintained [in this repository in ocds_api_specification.json](https://github.com/open-contracting/api-specification/tree/master/ocds_api_specification.json). At the moment it has only the very basic options.

To help edit the spec or view the specification more clearly you can open it in the [Swagger Editor](http://editor.swagger.io/#/?import=https://raw.githubusercontent.com/open-contracting/api-specification/master/ocds_api_specification.json)

A publisher SHOULD publish their own Swagger definition file at BaseURL/ocds_api.json


All methods in this definition file are optional apart from either releases.json method or records.json method. For these methods the following applies:

All data returned MUST be valid JSON and have a root object.

All data returned MUST have a links object in the root object.

All data returned MUST have a releases or records list in the root object.

All data returned in the releases or records list must be valid OCDS releases/records.

All published data MUST be discoverable by following "next" links in the links from either BaseURL/releases.json or BaseURL/records.json


Other Considerations
--------------------

Some APIs will contain data from many sources. When this is the case the OCDS package metadata (i.e license or publisher) may be different in each release/record.  In this case the package metadata SHOULD be embeded in the release/records themselves.  

API extension
-------------

There is an [extension available](https://github.com/open-contracting/api_extension/) which SHOULD be applied when producing an API which contains data from many sources. This extension formalizes how the package metadata should be embeded in a release/record and gives a definition of what the links object contains. Read its readme for more informaiton.
