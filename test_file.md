![](media/e16309b5e01c825d98f9f4c2d0e38bc6.png)

*REST API Reference Guide*

*Table of Contents*

[Chapter 1 Overview 7](#overview)

[Chapter 2 API Concepts 8](#api-concepts)

[2.1 REST Based API 8](#rest-based-api)

[2.2 Versioning 8](#versioning)

[2.3 Base URL 8](#base-url)

[2.4 Authentication 8](#authentication)

[2.5 Transport Security 8](#transport-security)

[2.6 Client Request Context 9](#client-request-context)

[2.7 Pagination 9](#pagination)

[2.8 Errors 9](#errors)

[2.9 Request Debugging 10](#request-debugging)

[Chapter 3 Terminology 11](#terminology)

[Chapter 4 Use Cases 12](#use-cases)

[4.1 Endpoint/Node Information and Management
12](#endpointnode-information-and-management)

[4.1.1 Get Endpoint Node Info 12](#get-endpoint-node-info)

[4.1.2 Get Node by host_identifier 13](#get-node-by-host_identifier)

[4.1.3 Get Node Configuration 14](#_Toc523230054)

[4.1.4 Get All Configs 15](#_Toc523230055)

[4.1.5 Config by id 17](#_Toc523230056)

[4.1.6 Create a new config 18](#_Toc523230057)

[4.1.7 Updating a config 18](#_Toc523230058)

[4.1.8 Assign a Config to a node 20](#assign-a-config-to-a-node)

[4.1.9 Remove a Config from a node 20](#remove-a-config-from-a-node)

[4.2 Scheduled Queries 20](#_Toc523230061)

[4.2.1 Scheduled Query Results for an Endpoint Node 20](#_Toc523230062)

[4.2.2 Schema 22](#_Toc523230063)

[4.3 Schema 24](#_Toc523230064)

[4.4 Distributed (Ad-Hoc) Queries 24](#_Toc523230065)

[4.4.1 Add a Distributed Query 24](#_Toc523230066)

[4.4.2 Get Results of a Distributed Query 25](#_Toc523230067)

[4.5 List all queries 25](#_Toc523230068)

[4.6 Query by id 26](#_Toc523230069)

[4.7 Add a query 26](#_Toc523230070)

[4.8 List all packs 27](#_Toc523230071)

[4.9 Pack by id 28](#_Toc523230072)

[4.10 Upload a pack 29](#_Toc523230073)

[4.11 Tags 29](#_Toc523230074)

[4.11.1 Get all tags 29](#_Toc523230075)

[4.11.2 Add new tags 29](#_Toc523230076)

[4.11.3 Modify tags on a node 30](#_Toc523230077)

[4.11.4 Modify tags on a query 30](#_Toc523230078)

[4.11.5 Modify tags on a pack 31](#_Toc523230079)

[Chapter 5 Rules 32](#_Toc523230080)

[5.1.1 List all rules 32](#_Toc523230081)

[5.1.2 Rule by id 33](#_Toc523230082)

[5.1.3 Add a rule 34](#_Toc523230083)

[5.1.4 Modify a rule 35](#_Toc523230084)

[5.1.5 Alerts 36](#_Toc523230085)

[5.1.6 Configure email sender and recipients for alerts 37](#_Toc523230086)

[5.1.7 Response Commands 38](#_Toc523230087)

[5.1.8 Response Command by command_id 38](#_Toc523230088)

[5.1.9 Response 39](#_Toc523230089)

[5.1.10 Kill Process on Endpoint 39](#_Toc523230090)

[5.1.11 Carves 41](#_Toc523230091)

[Chapter 6 Appendix 42](#_Toc523230092)

[6.1 Tables 42](#_Toc523230093)

[6.1.1 Generic System Info Tables 42](#_Toc523230094)

[6.1.2 System Properties Tables 43](#_Toc523230095)

[6.1.3 Network Interfaces and Routing Tables 44](#_Toc523230096)

[6.1.4 System Users and Groups Tables 44](#_Toc523230097)

[6.1.5 System Configuration Tables 44](#_Toc523230098)

[6.1.6 Diagnostics Info Tables 45](#_Toc523230099)

[6.1.7 Forensic Info Tables 45](#_Toc523230100)

[6.1.8 Activity and Resource Monitoring Tables 45](#_Toc523230101)

[6.1.9 Real Time Activity Monitoring (Evented) 46](#_Toc523230102)

[6.1.10 Performance Monitoring Tables 47](#_Toc523230103)

[6.1.11 Security Table 47](#_Toc523230104)

[6.1.12 Log Monitoring Tables 47](#_Toc523230105)

[6.1.13 WMI related tables 47](#_Toc523230106)

[6.1.14 Miscellaneous 48](#_Toc523230107)

[6.2 Schema for Tables 48](#_Toc523230108)

[6.2.1 Table Name: system_info 48](#_Toc523230109)

[6.2.2 Table Name: Time 49](#_Toc523230110)

[6.2.3 Table Name: Uptime 49](#_Toc523230111)

[6.2.4 Table Name: platform_info 50](#_Toc523230112)

[6.2.5 Table Name: cpu_id 50](#_Toc523230113)

[6.2.6 Table Name: win_msr 51](#_Toc523230114)

[6.2.7 Table Name: intel_me_info 51](#_Toc523230115)

[6.2.8 Table Name: logical_drives 51](#_Toc523230116)

[6.2.9 Table Name: os_version 52](#_Toc523230117)

[6.2.10 Table Name: kernel_info 52](#_Toc523230118)

[6.2.11 Table Name: drivers 52](#_Toc523230119)

[6.2.12 Table Name: chrome_extensions 53](#_Toc523230120)

[6.2.13 Table Name: ie_extensions 54](#_Toc523230121)

[6.2.14 Table Name: patches 54](#_Toc523230122)

[6.2.15 Table Name: programs 55](#_Toc523230123)

[6.2.16 Table Name: services 55](#_Toc523230124)

[6.2.17 Table Name: arp_cache 56](#_Toc523230125)

[6.2.18 Table Name: etc_hosts 56](#_Toc523230126)

[6.2.19 Table Name: etc_protocols 56](#_Toc523230127)

[6.2.20 Table Name: etc_services 57](#_Toc523230128)

[6.2.21 Table Name: interface_details 57](#_Toc523230129)

[6.2.22 Table Name: interface_addresses 59](#_Toc523230130)

[6.2.23 Table Name: routes 59](#_Toc523230131)

[6.2.24 Table Name: groups 60](#_Toc523230132)

[6.2.25 Table Name: logged_in_users 60](#_Toc523230133)

[6.2.26 Table Name: users 61](#_Toc523230134)

[6.2.27 Table Name: autoexec 61](#_Toc523230135)

[6.2.28 Table Name: scheduled_tasks 61](#_Toc523230136)

[6.2.29 Table Name: startup_items 62](#_Toc523230137)

[6.2.30 Table Name: Certificates 62](#_Toc523230138)

[6.2.31 Table Name: windows_crashes 63](#_Toc523230139)

[6.2.32 Table Name: carves 64](#_Toc523230140)

[6.2.33 Table Name: appcompat_shims 65](#_Toc523230141)

[6.2.34 Table Name: authenticode 65](#_Toc523230142)

[6.2.35 Table Name: file 65](#_Toc523230143)

[6.2.36 Table Name: hash 66](#_Toc523230144)

[6.2.37 Table Name: listening_ports 67](#_Toc523230145)

[6.2.38 Table Name: pipes 67](#_Toc523230146)

[6.2.39 Table Name: process_memory_map 67](#_Toc523230147)

[6.2.40 Table Name: process_open_sockets 68](#_Toc523230148)

[6.2.41 Table Name: processes 69](#_Toc523230149)

[6.2.42 Table Name: win_process_handles 70](#_Toc523230150)

[6.2.43 Table Name: registry 70](#_Toc523230151)

[6.2.44 Table Name: shared_resources 71](#_Toc523230152)

[6.2.45 Table Name: win_file_events 71](#_Toc523230153)

[6.2.46 Table Name: win_http_events 72](#_Toc523230154)

[6.2.47 Table Name: win_image_load_events 72](#_Toc523230155)

[6.2.48 Table Name: win_pefile_events 73](#_Toc523230156)

[6.2.49 Table Name: win_process_events 74](#_Toc523230157)

[6.2.50 Table Name: win_process_open_events 74](#_Toc523230158)

[6.2.51 Table Name: win_removable_media_events 75](#_Toc523230159)

[6.2.52 Table Name: win_socket_events 75](#_Toc523230160)

[6.2.53 Table Name: win_dns_events 76](#_Toc523230161)

[6.2.54 Table Name: win_dns_response_events 76](#_Toc523230162)

[6.2.55 Table Name: win_registry_events 77](#_Toc523230163)

[6.2.56 Table Name: win_remote_thread_events 78](#_Toc523230164)

[6.2.57 Table Name: win_file_timestomp_events 78](#_Toc523230165)

[6.2.58 Table Name: physical_disk_performance 79](#_Toc523230166)

[6.2.59 Table Name: win_yara_events 80](#_Toc523230167)

[6.2.60 Table Name: win_epp_table 80](#_Toc523230168)

[6.2.61 Table Name: win_obfuscated_ps 80](#_Toc523230169)

[6.2.62 Table Name: windows_events 81](#_Toc523230170)

[6.2.63 Table Name: wmi_cli_event_consumers 81](#_Toc523230171)

[6.2.64 Table Name: wmi_event_filters 82](#_Toc523230172)

[6.2.65 Table Name: wmi_filter_consumer_binding 82](#_Toc523230173)

[6.2.66 Table Name: wmi_script_event_consumers 82](#_Toc523230174)

[6.2.67 Table Name: carbon_black_info 83](#_Toc523230175)

[6.2.68 Table Name: chocolatey_packages 84](#table-name-chocolatey_packages)

[6.2.69 Table Name: python_packages 84](#table-name-python_packages)

Overview
========

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
--------------

-   Makes use of standard HTTP verbs like GET, POST, and DELETE.

-   Uses standard HTTP error responses to describe errors

-   Authentication provided using API keys in the HTTP Authorization header

-   Requests and responses are in JSON format.

Versioning
----------

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
```


| URL: https://\<Base URL\>**/nodes** Request Type: GET Response: A JSON Array of nodes and their properties. Example Response { "data": [ { "enrolled_on": "2018-07-24 06:22:48", "host_identifier": "77858CB1-6C24-584F-A28A-E054093C8924", "last_checkin": "2018-08-01 13:42:05", "network_info": { "mac_address": "54:26:96:d7:9a:65" }, "node_info": { "computer_name": "", "cpu_physical_cores": "2", "hardware_model": "MacBookPro10,2", "hardware_serial": "C02KV7RJDR53", "hardware_vendor": "Apple Inc.", "physical_memory": "8589934592" }, "node_key": "10b18bd8-9e5e-455f-bd48-8d7c456b841f", "tags": [ "second", "thirdsssss", "first" ] } ], "message": "Successfully fetched the nodes", "status": "success" } ``` |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Get Node by host_identifier

| URL: https://\<Base URL\>/nodes/\<host_identifier\> Request Type: GET Response: A node with its properties. Example Response { "data": { "enrolled_on": "2018-07-24 06:22:48", "host_identifier": "77858CB1-6C24-584F-A28A-E054093C8924", "last_checkin": "2018-08-01 13:42:05", "network_info": { "mac_address": "54:26:96:d7:9a:65" }, "node_info": { "computer_name": "", "cpu_physical_cores": "2", "hardware_model": "MacBookPro10,2", "hardware_serial": "C02KV7RJDR53", "hardware_vendor": "Apple Inc.", "physical_memory": "8589934592" }, "node_key": "10b18bd8-9e5e-455f-bd48-8d7c456b841f", "tags": [ "second", "thirdsssss", "first" ] }, "message": "Successfully fetched the node info", "status": "success" } |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Assign a Config to a node

By means of assigning a config, one or more scheduled queries can be assigned to
a node.

| URL: https://\<Base URL\>**/ nodes/config/assign** Request Type: POST Example Request { "host_identifier": "\<host_identifier\>", "config_id": 10, } Response { "status": "success", "message": "Successfully assigned the config" } |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Remove a Config from a node

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
