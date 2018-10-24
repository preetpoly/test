![](media/e16309b5e01c825d98f9f4c2d0e38bc6.png)

*REST API Reference Guide*

*Table of Contents*

[Table of Contents 2](#_Toc528150844)

[Chapter 1 Overview 3](#overview)

[Chapter 2 API Concepts 4](#api-concepts)

[2.1 REST Based API 4](#rest-based-api)

[2.2 Versioning 4](#versioning)

[2.3 Base URL 4](#base-url)

[2.4 Authentication 4](#authentication)

[2.5 Transport Security 4](#transport-security)

[2.6 Client Request Context 5](#client-request-context)

[2.7 Pagination 5](#pagination)

[2.8 Errors 5](#errors)

[2.9 Request Debugging 6](#request-debugging)

[Chapter 3 Terminology 7](#terminology)

[Chapter 4 Use Cases 8](#use-cases)

[4.1 Endpoint/Node Information and Management
8](#endpointnode-information-and-management)

[4.1.1 Get Endpoint Node Info 8](#get-endpoint-node-info)

[4.1.2 Get Node by host_identifier 9](#get-node-by-host_identifier)

[4.1.3 Assign a Config to a node 11](#assign-a-config-to-a-node)

[4.1.4 Remove a Config from a node 11](#remove-a-config-from-a-node)

[4.1.5 Table Name: chocolatey_packages 12](#table-name-chocolatey_packages)

[4.1.6 Table Name: python_packages 12](#table-name-python_packages)

 Overview
=========

The PolyLogyx endpoint platform is a combination of endpoint agents, an endpoint
fleet manager and an openc2 actuator.

PolyLogyx REST API allows developers to use a programming language of their
choice to integrate with the headless PolyLogyx server. The REST APIs provide
the means to configure and query the data from the fleet manager. The APIs also
allow an openc2 orchestrator to get visibility into endpoint data and
activities, which can then be used to craft an openc2 action.

All payloads are exchanged over REST and use the JSON schema.

API Concepts
============

REST Based API 
---------------

-   Makes use of standard HTTP verbs like GET, POST, and DELETE.

-   Uses standard HTTP error responses to describe errors

-   Authentication provided using API keys in the HTTP Authorization header

-   Requests and responses are in JSON format.

 Versioning
-----------

The PolyLogyx API is a versioned API. We reserve the right to add new
parameters, properties, or resources to the API without advance notice. These
updates are considered non-breaking and the compatibility rules below should be
followed to ensure your application does not break.

Breaking changes such as removing or renaming an attribute will be released as a
new version of the API. PolyLogyx will provide a migration path for new versions
of APIs and will communicate timelines for end-of-life when deprecating APIs. Do
not consume any API unless it is formally documented. All undocumented endpoints
should be considered private, subject to change without notice, and not covered
by any agreements.

The API version is currently v0.

All API requests must use the https scheme.

Base URL
--------

API calls are made to a URL to identify the location from which the data is
accessed. You must replace the placeholders \<server IP\> and \<port\> with
actual details for your PolyLogyx server. The Base URL follows this template:
https://\<server\>:\<port\>/services/api/v0/

Authentication
--------------

The PolyLogyx API requires all requests to present a valid API key
(x-access-token: API Key) specified in the HTTP Authorization header for every
HTTP request. If the API key is missing or invalid, a 401 unauthorized response
code is returned.

The API key (token) has the privileges associated with an administrator account
and does not automatically expire. If you believe your API key is compromised,
you can generate a new one. This ensures that the older API key can no longer be
used to authenticate to the server.

Transport Security
------------------

HTTP over TLS v1.2 is enforced for all API calls. Any non-secure calls will be
rejected by the server.

Client Request Context
----------------------

PolyLogyx will derive client request context directly from the HTTP request
headers and client TCP socket. Request context is used to evaluate policies and
provide client information for troubleshooting and auditing purposes.

-   User Agent - PolyLogyx supports the standard User-Agent HTTP header to
    identify the client application. Always send a User-Agent string to uniquely
    identify your client application and version such as SOC Application/1.1.

-   IP Address - The IP address of your application will be automatically used
    as the client IP address for your request.

Pagination
----------

Requests that return a list of resources may support paging. Pagination is based
on a cursor and not on page number.

Errors
------

All requests on success will return a 200 status if there is content to return
or a 204 status if there is no content to return. HTTP response codes are used
to indicate API errors.

| **Code** | **Description**                                                     |
|----------|---------------------------------------------------------------------|
| 400      | Malformed or bad JSON Request                                       |
| 401      | API access without authentication or invalid API key                |
| 404      | Resource not found                                                  |
| 422      | Request can be parsed. But, has invalid content                     |
| 429      | Too many requests. Server has encountered rate limits               |
| 200      | Success                                                             |
| 201      | Created. Returns after a successful POST when a resource is created |
| 500      | Internal server error                                               |
| 503      | Service currently unavailable                                       |

Request Debugging
-----------------

The request ID will always be present in every API response and can be used for
debugging. The following header is set in each response:

X-PolyLogyx-Request-Id - The unique identifier for the API request

HTTP/1.1 200 OK

X-PolyLogyx-Request-Id: reqVy8wsvmBQN27h4soUE3ZEnA

Terminology
===========

Here is a description of the various terms used in this document.

| **Term**        | **Description**                                                                                                                                                                                                                                                                                              |
|-----------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Fleet           | Set of endpoints running the PolyLogyx agent and managed by the PolyLogyx server                                                                                                                                                                                                                             |
| Node            | A specific endpoint that is actively monitored                                                                                                                                                                                                                                                               |
| Config          | PolyLogyx osquery based agent derives its behavior from its configuration. The config is a JSON describing the various options used to instrument the agent behavior as well as the queries scheduled on the agent. Config is applied at a node level. Refer the product guide for supported configurations. |
| Options         | Options (or flags) are the set of parameters the agent uses to effect its behavior. A list of all the flags supported can be found at *https://osquery.readthedocs.io/en/stable/installation/cli-flags/* Options can be retrieved as part of config.                                                         |
| Tag             | A mechanism to logically group/associate elements such as nodes, packs etc.                                                                                                                                                                                                                                  |
| Scheduled Query | Queries that run on a specified scheduled on an endpoint                                                                                                                                                                                                                                                     |
| Query Pack      | Grouping of scheduled queries                                                                                                                                                                                                                                                                                |
| Ad Hoc Query    | A live, on-demand query that is targeted at an endpoint or a set of endpoints. Also referred to as a distributed query.                                                                                                                                                                                      |
| Alerts          | Rules can be applied to results of scheduled queries. When events match with a rule, the PolyLogyx server can generate an alert with the event information for proactive analysis by the SOC analyst.                                                                                                        |
| Active Response | Actions that be taken on affected endpoint(s) as part of Incident Response activity.                                                                                                                                                                                                                         |

Use Cases
=========

Endpoint/Node Information and Management
----------------------------------------

### Get Endpoint Node Info

Lists all endpoint nodes managed by the PolyLogyx server and their properties.

URL: https://\<Base URL\>**/nodes**

Request Type: GET

Response: A JSON Array of nodes and their properties.

Example Response

{

"data": [

{

"enrolled_on": "2018-07-24 06:22:48",

"host_identifier": "77858CB1-6C24-584F-A28A-E054093C8924",

"last_checkin": "2018-08-01 13:42:05",

"network_info": {

"mac_address": "54:26:96:d7:9a:65"

},

"node_info": {

"computer_name": "",

"cpu_physical_cores": "2",

"hardware_model": "MacBookPro10,2",

"hardware_serial": "C02KV7RJDR53",

"hardware_vendor": "Apple Inc.",

"physical_memory": "8589934592"

},

"node_key": "10b18bd8-9e5e-455f-bd48-8d7c456b841f",

"tags": [

"second",

"thirdsssss",

"first"

]

}

],

"message": "Successfully fetched the nodes",

"status": "success"

}

###   Get Node by host_identifier

>   URL: https://\<Base URL\>/nodes/\<host_identifier\>

>   Request Type: GET

>   Response: A node with its properties.

>   Example Response

>   {

>   "data": {

>   "enrolled_on": "2018-07-24 06:22:48",

>   "host_identifier": "77858CB1-6C24-584F-A28A-E054093C8924",

>   "last_checkin": "2018-08-01 13:42:05",

>   "network_info": {

>   "mac_address": "54:26:96:d7:9a:65"

>   },

>   "node_info": {

>   "computer_name": "",

>   "cpu_physical_cores": "2",

>   "hardware_model": "MacBookPro10,2",

>   "hardware_serial": "C02KV7RJDR53",

>   "hardware_vendor": "Apple Inc.",

>   "physical_memory": "8589934592"

>   },

>   "node_key": "10b18bd8-9e5e-455f-bd48-8d7c456b841f",

>   "tags": [

>   "second",

>   "thirdsssss",

>   "first"

>   ]

>   },

>   "message": "Successfully fetched the node info",

>   "status": "success"

>   }

### Assign a Config to a node

By means of assigning a config, one or more scheduled queries can be assigned to
a node.

| URL: https://\<Base URL\>**/ nodes/config/assign** Request Type: POST Example Request { "host_identifier": "\<host_identifier\>", "config_id": 10, } Response { "status": "success", "message": "Successfully assigned the config" } |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


###  Remove a Config from a node

By means of assigning a config, one or more scheduled queries can be assigned to
a node.

| URL: https://\<Base URL\>**/ nodes/config/remove** Request Type: POST Example Request { "host_identifier": "\<host_identifier\>" } Response { "status": "success", "message": "Successfully removed the config" } |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Table Name: chocolatey_packages

| **Column** | **Type** | **Description**                         |
|------------|----------|-----------------------------------------|
| name       | TEXT     | Package display name                    |
| version    | TEXT     | Package-supplied version                |
| summary    | TEXT     | Package-supplied summary                |
| author     | TEXT     | Optional package author                 |
| license    | TEXT     | License under which package is launched |
| path       | TEXT     | Path at which this module resides       |

### Table Name: python_packages

| **Column** | **Type** | **Description**                         |
|------------|----------|-----------------------------------------|
| name       | TEXT     | Package display name                    |
| version    | TEXT     | Package-supplied version                |
| summary    | TEXT     | Package-supplied summary                |
| author     | TEXT     | Optional package author                 |
| license    | TEXT     | License under which package is launched |
| path       | TEXT     | Path at which this module resides       |
