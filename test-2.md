![](media/e16309b5e01c825d98f9f4c2d0e38bc6.png)

*REST API Reference Guide*

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

| Code | Description                                                         |
|------|---------------------------------------------------------------------|
| 400  | Malformed or bad JSON Request                                       |
| 401  | API access without authentication or invalid API key                |
| 404  | Resource not found                                                  |
| 422  | Request can be parsed. But, has invalid content                     |
| 429  | Too many requests. Server has encountered rate limits               |
| 200  | Success                                                             |
| 201  | Created. Returns after a successful POST when a resource is created |
| 500  | Internal server error                                               |
| 503  | Service currently unavailable                                       |

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

| Term            | Description                                                                                                                                                                                                                                                                                                  |
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

\`\`\`\`

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

\`\`\`\`

###   Get Node by host_identifier

| URL: https://\<Base URL\>/nodes/\<host_identifier\> Request Type: GET Response: A node with its properties. Example Response { "data": { "enrolled_on": "2018-07-24 06:22:48", "host_identifier": "77858CB1-6C24-584F-A28A-E054093C8924", "last_checkin": "2018-08-01 13:42:05", "network_info": { "mac_address": "54:26:96:d7:9a:65" }, "node_info": { "computer_name": "", "cpu_physical_cores": "2", "hardware_model": "MacBookPro10,2", "hardware_serial": "C02KV7RJDR53", "hardware_vendor": "Apple Inc.", "physical_memory": "8589934592" }, "node_key": "10b18bd8-9e5e-455f-bd48-8d7c456b841f", "tags": [ "second", "thirdsssss", "first" ] }, "message": "Successfully fetched the node info", "status": "success" } |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Get Node Configuration

Get the config for a node.

| URL: https://\<Base URL\>/nodes/config/\<host_identifier\> Request Type: GET Response: Current config applied on node. Example Response { "status": "success", "message": "Successfully fetched the node config", "data": { "packs": {}, "win_include_paths": { "user_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\" ] }, "options": { "custom_plgx_LogModeQuiet": "0", "custom_plgx_enable_respserver": "true", "schedule_splay_percent": 10, "custom_plgx_ServerHttpPort": "8280", "custom_plgx_ServerHttpsPort": "443", "logger_tls_compress": true, "custom_plgx_LogFileName": "\\\\ProgramData\\\\osquery\\\\plgx_win_extn\\\\log\\\\plgx-agent.log", "custom_plgx_LogLevel": "1", "custom_plgx_EnableLogging": "true", "disable_watchdog": true, "host_identifier": "uuid", "custom_plgx_ServerPort": "443" }, "win_exclude_paths": { "temp_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\exclude\\\\" ] }, "schedule": { "win_file_events": { "query": "select \* from win_file_events;", "interval": 10, "description": "win file events" } } } } |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Get All Configs

Lists all configs available.

| URL: https://\<Base URL\>**/configs** Request Type: GET Response: List all the configs available. Example Response { "status": "success", "message": "Successfully received the configs", "data": [ { "updated_at": "2018-07-27 12:21:00", "created_at": null, "config": { "win_include_paths": { "user_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\" ] }, "win_exclude_paths": { "temp_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\exclude\\\\" ] }, "schedule": { "win_file_events": { "query": "select \* from win_file_events;", "interval": 10, "description": "win file events" } } }, "id": 2, "name": "poly-config-new" }, { "updated_at": "2018-07-27 12:15:46", "created_at": null, "config": { "win_include_paths": { "system_folders": [ "C:\\\\Windows\\\\System32\\\\drivers\\\\" ], "system_files": [ "C:\\\\Windows\\\\System32\\\\drivers\\\\etc\\\\hosts" ] "prog_files": [ "C:\\\\Program Files\\\\", "C:\\\\Program Files (x86)\\\\" ], "user_folders": [ "C:\\\\Temp\\\\", "C:\\\\Users\\\\Default\\\\Downloads\\\\" ] }, "win_exclude_paths": { "temp_folders": [ "C:\\\\Windows\\\\Temp\\\\", "C:\\\\Users\\\\admin\\\\AppData\\\\Roaming\\\\", "C:\\\\Windows\\\\SoftwareDistribution\\\\", "C:\\\\Windows\\\\WinSxS\\\\" ] "prog_files": [ "C:\\\\Program Files (x86)\\\\Windows Kits\\\\" ] "system_folders": [ "C:\\\\Windows\\\\Prefetch\\\\" ] }, schedule": { "win_file_events": "query": "select \* from win_file_events;", "interval": 10, "description": "win file events" } } }, "id": 1, "name": "poly-config" }, { "updated_at": "2018-07-27 12:56:24", "created_at": null, "config": { "win_include_paths": { "user_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\" ] }, "win_exclude_paths": { "temp_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\exclude\\\\" ] }, "schedule": { "win_file_events": { "query": "select \* from win_file_events;", "interval": 10, "description": "win file events" } } }, "id": 3, "name": "poly-config-new" } ] } |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Config by id

| URL: https://\<Base URL\>**/configs/\<config_id\>** Request Type: GET Example Response { "data": { "config": { "schedule": { "processes": { "description": "processes", "interval": 10, "query": "select \* from processes;" } }, "win_exclude_paths": { "temp_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\exclude\\\\" ] }, "win_include_paths": { "user_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\" ] } }, "created_at": null, "id": 3, "name": "poly-config-new", "updated_at": "2018-08-01 15:04:10" }, "message": "Successfully fetched the data", "status": "success" } |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Create a new config

Create a config by mixing a combination of packs, queries and other supported
configurations.

| URL: https://\<Base URL\>**/configs/add** Request Type: POST Example Request { "name": "poly-config-new", "config": { "schedule": { "win_file_events": { "query": "select \* from win_file_events;", "interval": 10, "description": "win file events" } }, "win_include_paths": { "user_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\" ] }, "win_exclude_paths": { "temp_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\exclude\\\\" ] } } } Response { "status": "success", "message": "Successfully added the config", "config_id": 10 } |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Updating a config 

| URL: https://\<Base URL\>**/configs/\<config_id\>** Request Type: POST Example Request { "config": { "schedule": { "processes": { "description": "processes", "interval": 10, "query": "select \* from processes;" } }, "win_exclude_paths": { "temp_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\exclude\\\\" ] }, "win_include_paths": { "user_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\" ] } }, "name": "poly-config-new" } Response { "data": { "config": { "schedule": { "processes": { "description": "processes", "interval": 10, "query": "select \* from processes;" } }, "win_exclude_paths": { "temp_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\exclude\\\\" ] }, "win_include_paths": { "user_folders": [ "C:\\\\Users\\\\\*\\\\Downloads\\\\" ] } }, "created_at": null, "id": 3, "name": "poly-config-new", "updated_at": "2018-08-01 15:04:10" }, "message": "Successfully updated the config", "status": "success" } |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


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


Scheduled Queries
-----------------

### Scheduled Query Results for an Endpoint Node

Get all the data coming from a schedule query for a specific endpoint node. This
query will retrieve all the results that match from the PolyLogyx server
database.

| URL: https://\<Base URL \>/nodes/schedule_query/results Request Type: POST Example Request: { "host_identifier": "\<host_identifier\>", "query": "win_file_events", "start": 0, "limit": 100 } Example Response: { "status": "success", "message": "Successfully received node schedule query results", "data": [ { "name": "win_file_events", "timestamp": "2018-07-24T07:09:38", "node_id": 6, "action": "added", "id": 948, "columns": { "uid": "poly-win10\\\\test", "pid": "4", "hashed": "1", "target_path": "C:\\\\Users\\\\test\\\\AppData\\\\Local\\\\Packages\\\\Microsoft.Windows.ContentDeliveryManager_cw5n1h2txyewy\\\\Settings\\\\settings.dat", "pe_file": "NO", "eid": "0A253E4E-6996-11E8-BF2C-000D3A01FCC1", "time": "1528295378", "action": "WRITE", "utc_time": "Wed Jun 6 14:29:38 2018 UTC", "md5": "6eea00c4dd37725e90c027a60f3ce1a6" } }, { "name": "win_file_events", "timestamp": "2018-07-24T07:09:38", "node_id": 6, "action": "added", "id": 949, "columns": { "uid": "poly-win10\\\\test", "pid": "4", "hashed": "1", "target_path": "C:\\\\Users\\\\test\\\\AppData\\\\Local\\\\Packages\\\\Microsoft.Windows.ContentDeliveryManager_cw5n1h2txyewy\\\\Settings\\\\settings.dat.LOG1", "pe_file": "NO", "eid": "0A253E4F-6996-11E8-BF2C-000D3A01FCC1", "time": "1528295378", "action": "WRITE", "utc_time": "Wed Jun 6 14:29:38 2018 UTC", "md5": "a8546005d1e59626d25c8884a1176a27" } }, { "name": "win_file_events", "timestamp": "2018-07-24T07:09:38", "node_id": 6, "action": "added", "id": 950, "columns": { "uid": "BUILTIN\\\\Administrators", "pid": "1688", "hashed": "1", "target_path": "C:\\\\Windows\\\\Prefetch\\\\IPCONFIG.EXE-62724FE6.pf", "pe_file": "NO", "eid": "0A253EC9-6996-11E8-BF2C-000D3A01FCC1", "time": "1528295396", "action": "WRITE", "utc_time": "Wed Jun 6 14:29:56 2018 UTC", "md5": "6507a711e37054b1ad1bec2d9daa6c28" } } ] } |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


###   Schema

List all the table schemas supported by server.

| URL: https://\<Base URL\> **/schema** Request Type: **GET** Example Response { "data": { "account_policy_data": "CREATE TABLE account_policy_data (uid BIGINT, creation_time DOUBLE, failed_login_count BIGINT, failed_login_timestamp DOUBLE, password_last_set_time DOUBLE)", "win_dns_events": "CREATE TABLE win_dns_events(event_type TEXT,eid TEXT, domain_name TEXT, pid TEXT, remote_address TEXT, remote_port BIGINT, time BIGINT, utc_time TEXT)", "win_dns_response_events": "CREATE TABLE win_dns_response_events( event_type TEXT,eid TEXT, domain_name TEXT,resolved_ip TEXT, pid BIGINT, remote_address TEXT, remote_port INTEGER , time BIGINT, utc_time TEXT )", "win_epp_table": "CREATE TABLE win_epp_table(product_type TEXT, product_name TEXT,product_state TEXT, product_signatures TEXT)", "win_file_events": "CREATE TABLE win_file_events(action TEXT, eid TEXT,target_path TEXT, md5 TEXT ,hashed BIGINT,uid TEXT, time BIGINT,utc_time TEXT, pe_file TEXT , pid BIGINT)", "win_http_events": "CREATE TABLE win_http_events(event_type TEXT, eid TEXT, pid TEXT, url TEXT, remote_address TEXT, remote_port BIGINT, time BIGINT,utc_time TEXT)", "win_image_load_events": "CREATE TABLE win_image_load_events(eid TEXT, pid BIGINT,uid TEXT, image_path TEXT, sign_info TEXT, trust_info TEXT, time BIGINT, utc_time TEXT, num_of_certs BIGINT, cert_type TEXT, version TEXT, pubkey TEXT, pubkey_length TEXT, pubkey_signhash_algo TEXT, issuer_name TEXT, subject_name TEXT, serial_number TEXT, signature_algo TEXT, subject_dn TEXT, issuer_dn TEXT)", "win_msr": "CREATE TABLE win_msr(turbo_disabled INTEGER , turbo_ratio_limt INTEGER ,platform_info INTEGER, perf_status INTEGER ,perf_ctl INTEGER,feature_control INTEGER, rapl_power_limit INTEGER ,rapl_energy_status INTEGER, rapl_power_units INTEGER )", "win_obfuscated_ps": "CREATE TABLE win_obfuscated_ps(script_id TEXT, time_created TEXT, obfuscated_state TEXT, obfuscated_score TEXT)", "win_pefile_events": "CREATE TABLE win_pefile_events(action TEXT, eid TEXT,target_path TEXT, md5 TEXT ,hashed BIGINT,uid TEXT, pid BIGINT, time BIGINT,utc_time TEXT )", "win_process_events": "CREATE TABLE win_process_events(action TEXT, eid TEXT,pid BIGINT, path TEXT ,cmdline TEXT,parent BIGINT, parent_path TEXT,owner_uid TEXT, time BIGINT, utc_time TEXT )", "win_process_handles": "CREATE TABLE win_process_handles(pid BIGINT, handle_type TEXT, object_name TEXT, access_mask BIGINT)", "win_process_open_events": "CREATE TABLE win_process_open_events(action TEXT, eid TEXT,src_pid BIGINT,target_pid BIGINT, src_path TEXT ,target_path TEXT,owner_uid TEXT, time BIGINT, utc_time TEXT )", "win_registry_events": "CREATE TABLE win_registry_events(action TEXT, eid TEXT,key_name TEXT, new_key_name TEXT,value_data TEXT, value_type TEXT, owner_uid TEXT, time BIGINT, utc_time TEXT)", "win_remote_thread_events": "CREATE TABLE win_remote_thread_events( eid TEXT,src_pid BIGINT,target_pid BIGINT, src_path TEXT ,target_path TEXT,owner_uid TEXT, time BIGINT, utc_time TEXT )", "win_removable_media_events": "CREATE TABLE win_removable_media_events(removable_media_event_type TEXT, eid TEXT,uid TEXT,time BIGINT, utc_time TEXT,pid BIGINT)", "win_socket_events": "CREATE TABLE win_socket_events(event_type TEXT, eid TEXT, action TEXT, utc_time TEXT,time BIGINT, pid BIGINT, family TEXT, protocol INTEGER, local_address TEXT, remote_address TEXT, local_port INTEGER,remote_port INTEGER)", "win_yara_events": "CREATE TABLE win_yara_events(target_path TEXT, category TEXT, action TEXT, matches TEXT, count INTEGER, eid TEXT)", "windows_crashes": "CREATE TABLE windows_crashes (datetime TEXT, module TEXT, path TEXT, pid BIGINT, tid BIGINT, version TEXT, process_uptime BIGINT, stack_trace TEXT, exception_code TEXT, exception_message TEXT, exception_address TEXT, registers TEXT, command_line TEXT, current_directory TEXT, username TEXT, machine_name TEXT, major_version INTEGER, minor_version INTEGER, build_number INTEGER, type TEXT, crash_path TEXT)" }, "message": "Successfully fetched the schema", "status": "success" } |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


 Schema
-------

List all the table schemas supported by server.

| URL: https://\<Base URL\> **/schema/\<table_name\>** Request Type: **GET** Example Response { "data": "CREATE TABLE win_file_events(action TEXT, eid TEXT,target_path TEXT, md5 TEXT ,hashed BIGINT,uid TEXT, time BIGINT,utc_time TEXT, pe_file TEXT , pid BIGINT)", "message": "Successfully received table schema", "status": "success" } |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


Distributed (Ad-Hoc) Queries
----------------------------

### Add a Distributed Query

Sends a live, ad-hoc query to specified endpoint nodes.

| URL: https://\<Base URL\> **/distributed/add** Request Type: **POST** Example Request { "query": "select \* from system_info;", "tags": [ "demo" ], "nodes": [ "6357CE4F-5C62-4F4C-B2D6-CAC567BD6113" ] } Response { "status": "success", "message": "Successfully send the distributed query", “query_id": "1" } |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Get Results of a Distributed Query

Distributed query results are streamed over a websocket. You can use any
websocket client. Query results will expire, if not retrieved in 10 minutes.

| URL: wss://\<IP_ADDRESS:PORT\>/distributed/result Send the **query id** as a message after connect |
|----------------------------------------------------------------------------------------------------|


The following picture shows an example of creating websockets using the ARC
([AdvancedRESTClient](https://install.advancedrestclient.com/#/install))

PIC MISSING

List all queries
----------------

| URL: https://\<Base URL\> **/queries** Request Type: **GET** Response { "data": [ { "description": "", "id": 103, "interval": 3600, "platform": "windows", "query": "select \* from win_removable_media_events;", "removed": false, "shard": 100, "tags": [], "value": "", "version": "" } ], "message": "Successfully received the queries", "status": "success" } |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


Query by id
-----------

| URL: https://\<Base URL\> **/queries/\<query_id\>** Request Type: **GET** Response { "data": { "description": "YARA scan result events", "id": 3, "interval": 5, "platform": "windows", "query": "select \* from win_yara_events limit 10;", "removed": true, "shard": 100, "tags": [], "value": "scan results", "version": "2.9.0" }, "message": "Successfully fetched the query", "status": "success" } |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


Add a query
-----------

| URL: https://\<Base URL\> **/queries/add** Request Type: **POST** Example Request { "name": "running_process_query", "query": "select \* from processes;", "interval": 5, "platform": "windows", "version": "2.9.0", "description": "Processes", "value": "Processes" , "tags":["finance","sales"] } Response { "data": 104, "message": "Successfully created the query", "status": "success" } |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


List all packs
--------------

| URL: https://\<Base URL\> **/packs** Request Type: **GET** Response { "data": [ { "discovery": [], "id": 1, "name": "all-query-pack", "platform": null, "queries": { "win_dns_events": { "description": "Windows DNS Events", "id": 8, "interval": 60, "platform": "windows", "query": "select \* from win_dns_events;", "removed": true, "shard": null, "tags": [], "value": "Dns events", "version": "2.9.0" } }, "shard": null, "tags": [], "version": null } ], "status": "success", "message": "Successfully received the packs" } |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


Pack by id
----------

| URL: https://\<Base URL\> **/packs/\<pack_id\>** Request Type: **GET** Response { { "data": { "discovery": [], "id": 1, "name": "pack_1", "platform": null, "queries": { "win_yara_events": { "description": "YARA scan result events", "id": 4, "interval": 5, "platform": "windows", "query": "select \* from win_yara_events;", "removed": true, "shard": 100, "tags": [], "value": "scan results", "version": "2.9.0" } }, "shard": null, "tags": [], "version": null }, "message": "Successfully fetched the pack", "status": "success" } |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


Upload a pack
-------------

| URL: https://\<Base URL\> **/packs/add** Request Type: **POST** Example Request { "name": "process_query_pack", "queries": { "win_file_events": { "query": "select \* from processes;", "interval": 5, "platform": "windows", "version": "2.9.0", "description": "Processes", "value": "Processes" } }, "tags":["finance","sales"] } Response { "message": "Imported query pack process_query_pack", "status": "success" } |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


 Tags
-----

### Get all tags

| URL: https://\<Base URL\> **/tags** Request Type: **GET** Example Response { "data": [ "finance", "sales" ], "message": "Successfully received the tags", "status": "success" } |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Add new tags

Add an array of tags.

| URL: https://\<Base URL\> **/tags/add** Request Type: **POST** Example Request { "tags":["finance","sales"] } Response { "message": "Successfully added the tags", "status": "success" } |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Modify tags on a node

Add/remove tags on a node.

| URL: https://\<Base URL\> **/nodes/tag/edit** Request Type: **POST** Example Request { "host_identifier":"77858CB1-6C24-584F-A28A-E054093C8924", "add_tags":["finance","sales"], "remove_tags":["demo"] } Response { "message": "Successfully modified the tag(s)", "status": "success" } |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


###  Modify tags on a query

Add/remove tags on a query.

| URL: https://\<Base URL\> **/queries/tag/edit** Request Type: **POST** Example Request { "query_id":1, "add_tags":["finance","sales"], "remove_tags":["demo"] } Response { "message": "Successfully modified the tag(s)", "status": "success" } |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Modify tags on a pack

Add/remove tags on a pack.

| URL: https://\<Base URL\> **/packs/tag/edit** Request Type: **POST** Example Request { "pack_id":1, "add_tags":["finance","sales"], "remove_tags":["demo"] } Response { "message": "Successfully modified the tag(s)", "status": "success" } |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


 Rules
======

### List all rules

List all the rules.

| URL: https://\<Base URL\> **/rules** Request Type: **GET** Response { "data": [ { "alerters": [ "email", "debug" ], "conditions": { "condition": "AND", "rules": [ { "field": "column", "id": "column", "input": "text", "operator": "column_contains", "type": "string", "value": [ "domain_name", "89.com" ] } ], "valid": true }, "description": "Adult websites test", "id": 1, "name": "Adult websites test", "status": "ACTIVE", "updated_at": "Tue, 31 Jul 2018 12:31:43 GMT" } ], "message": "Successfully received the alert rules", "status": "success" } |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Rule by id

| URL: https://\<Base URL\> **/rules/\<rule_id\>** Request Type: **GET** Response { "data": { "alerters": [ "email", "debug" ], "conditions": { "condition": "AND", "rules": [ { "field": "column", "id": "column", "input": "text", "operator": "column_contains", "type": "string", "value": [ "domain_name", "89.com" ] } ], "valid": true }, "description": "Adult websites test", "id": 1, "name": "Adult websites test", "status": "ACTIVE", "updated_at": "Tue, 31 Jul 2018 12:31:43 GMT" }, "message": "Successfully fetched the rule", "status": "success" } |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Add a rule

| URL: https://\<Base URL\> **/rules/add** Request Type: **POST** Example request: { "alerters": [ "email","splunk" ], "name":"Adult website test", "description":"Rule for finding adult websites", "conditions": { "condition": "AND", "rules": [ { "id": "column", "type": "string", "field": "column", "input": "text", "value": [ "issuer_name", "Polylogyx.com(Test)" ], "operator": "column_contains" } ], "valid": true } } Response { "data": 2, "message": "Successfully configured the rule", "status": "success" } |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Modify a rule

| URL: https://\<Base URL\> **/rules/\<rule_id\>** Request Type: **POST**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| Example request:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| { "alerters": [ "email", "debug" ], "conditions": { "condition": "AND", "rules": [ { "field": "column", "id": "column", "input": "text", "operator": "column_contains", "type": "string", "value": [ "domain_names", "89.com" ] } ], "valid": true }, "description": "Adult websites test", "name": "Adult websites test", "status": "ACTIVE" } Response:                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| { "data": { "alerters": [ "email", "debug" ], "conditions": { "condition": "AND", "rules": [ { "field": "column", "id": "column", "input": "text", "operator": "column_contains", "type": "string", "value": [ "domain_names", "89.com" ] } ], "valid": true }, "description": "Adult websites test", "id": 1, "name": "Adult websites test", "status": "ACTIVE", "updated_at": "Wed, 01 Aug 2018 15:15:10 GMT" }, "message": "Successfully modified the rule", "status": "success" }                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Alerts

List all the alerts based on node, query_name, and rule_id.

| URL: https://\<Base URL\> **/alerts** Request Type: **POST** Example Request: { "host_identifier":"77858CB1-6C24-584F-A28A-E054093C8924", "query_name":"processes", "rule_id":3 } Response { "data": [ { "created_at": "Tue, 31 Jul 2018 14:19:30 GMT", "id": 1, "message": { "cmdline": "/sbin/launchd", "cwd": "/", "egid": "0", "euid": "0", "gid": "0", "name": "launchd", "nice": "0", "on_disk": "1", "parent": "0", "path": "/sbin/launchd", "pgroup": "1", "pid": "1", "resident_size": "6078464", "root": "", "sgid": "0", "start_time": "0", "state": "R", "suid": "0", "system_time": "105116", "threads": "4", "total_size": "17092608", "uid": "0", "user_time": "10908", "wired_size": "0" }, "node_id": 1, "query_name": "processes", "rule_id": 3, "sql": null } ], "message": "Successfully received the alerts", "status": "success" } |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Configure email sender and recipients for alerts

| URL: https://\<Base URL\> **/email/configure** Request Type: **POST** Example request: { "emalRecipients": [ "mehtamouli1k\@gmail.com", "moulik1\@polylogyx.com" ], "email": "mehtamoulik13\@gmail.com", "smtpAddress": "smtp2.gmail.com", "password": "a", "smtpPort": 445 } Response { "data": { "email": "mehtamoulik13\@gmail.com", "emailRecipients": "mehtamouli1k\@gmail.com,moulik1\@polylogyx.com", "emails": "mehtamoulik\@gmail.com,moulik\@polylogyx.com", "password": "YQ==\\n", "smtpAddress": "smtp2.gmail.com", "smtpPort": 445 }, "message": "Successfully updated the details", "status": "success" } |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Response Commands

List all the response commands.

| URL: https://\<Base URL\> **/response** Request Type: **GET** Response { "data": [ { "action": null, "command": "{\\"action\\": \\"delete\\", \\"actuator\\": {\\"endpoint\\": \\"polylogyx_vasp\\"}, \\"target\\": {\\"file\\": {\\"device\\": {\\"hostname\\": \\"win10x64-1703\\"}, \\"hashes\\": {\\"md5\\": \\"F4377762954BC0F8A9641CE303539E4D\\"}, \\"name\\": \\"C:\\\\\\\\\\\\\\\\Users\\\\\\\\\\\\\\\\Default\\\\\\\\\\\\\\\\Downloads\\\\\\\\\\\\\\\\hi.txt\\"}}}", "command_id": "2c92808564cbab3d0164d135e21d0008", "id": 1, "message": "FILE_NOT_FOUND", "status": "failure" } ], "message": "Successfully received the response commands", "status": "success" } |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Response Command by command_id

| URL: https://\<Base URL\> /response/\<command_id\> Request Type: **GET** Response { "data": { "action": null, "command": "{\\"action\\": \\"delete\\", \\"actuator\\": {\\"endpoint\\": \\"polylogyx_vasp\\"}, \\"target\\": {\\"file\\": {\\"device\\": {\\"hostname\\": \\"win10x64-1703\\"}, \\"hashes\\": {\\"md5\\": \\"F4377762954BC0F8A9641CE303539E4D\\"}, \\"name\\": \\"C:\\\\\\\\\\\\\\\\Users\\\\\\\\\\\\\\\\Default\\\\\\\\\\\\\\\\Downloads\\\\\\\\\\\\\\\\hi.txt\\"}}}", "command_id": "2c92808564cbab3d0164d135e21d0008", "id": 1, "message": "FILE_NOT_FOUND", "status": "failure" }, "message": "Successfully received the command status", "status": "success" } |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Response

Delete a File on Endpoint

Allows the SOC analyst to respond to a potential compromise by requesting the
PolyLogyx server to delete a specific file on an identified endpoint.

| URL: https://\<Base URL\> **/response/add** Request Type: **POST** Example Request { "action": "delete", "actuator": { "endpoint": "polylogyx_vasp" }, "target": { "file": { "device": { "hostname": "poly-win10" }, "hashes": {}, "name": "C:\\\\Users\\\\Default\\\\Downloads\\\\malware.txt" } } } Response:                                                                                                                               |
| { "command_id": "2c92808564ebc9d20164f67fccdd000b", "message": "Successfully send the response command", "status": "success" }                                                                                                                                                                                                                                                                                                                |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Kill Process on Endpoint

Allows the SOC analyst to terminate a process (identified by a process ID-pid)
on a specific endpoint.

| URL: https://\<Base URL\> **/response/add** Request Type: **POST** Example Request { "action": "stop", "target": { "process": { "name": "calc.exe", "pid": 123, "device": { "hostname": "10.0.0.5" } } }, "actuator": { "endpoint": "polylogyx_vasp" } } Response:                                                                                                                               |
| { "command_id": "2c92808564ebc9d20164f67fccdd000b", "message": "Successfully send the response command", "status": "success" }                                                                                                                                                                                                                                                                   |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### Carves

List all the carve session. Host identifier is optional.

| URL: https://\<Base URL\> **/carves** Request Type: **POST** Example Request: { "host_identifier":"77858CB1-6C24-584F-A28A-E054093C8924" } Response { "data": [ { "archive": "2N1P2UNDY6cd0877fa-36e4-41ff-926a-ff2a22673dc3.tar", "block_count": 1, "carve_guid": "cd0877fa-36e4-41ff-926a-ff2a22673dc3", "carve_size": 5632, "created_at": "2018-07-24 07:50:05", "id": 10, "node_id": 1, "session_id": "2N1P2UNDY6" } ], "message": "Successfully fetched the carves", "status": "success" } |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


Appendix
========

Tables
------

The PolyLogyx Endpoint Platform leverages osquery, a multi-platform operating
system monitoring and instrumentation framework. This enables PolyLogyx to
expose the endpoint node as a high-performance relational database in form of
SQL tables. These tables represent a breadth of abstract operating system
concepts such as running processes, installed applications, hardware properties,
network connections etc. thereby allowing to gain deep visibility in form of
simple SQL queries.

This release of the PolyLogyx Endpoint Platform embeds osquery v3.2.4. PolyLogyx
will make a concerted effort to ensure that we leverage the latest, stable
version of osquery. For more information on base osquery tables and schema,
refer to <https://osquery.io/schema/>*.*

PolyLogyx has extended and optimized osquery to support detection and response
use cases by adding new tables (see below). Where required, the tables are real
time (evented) in nature ensuring that 100% of activity is captured for analysis
and investigation.

The query-based approach is extremely well suited for a “point in time” view of
the endpoint node. However, the approach also has limitations:

1.  It can be blind to system events that occur between 2 queries

2.  It can result in performance and latency issues especially when the data
    items queried are computationally expensive (e.g. hashing).

>   To overcome this, PolyLogyx implements an event-based architecture for
>   system events that require continuous monitoring in real time. This data is
>   stored in a local database and is made available to the PolyLogyx server as
>   on demand.

### Generic System Info Tables

| Table Name  | Brief Description                                           |
|-------------|-------------------------------------------------------------|
| system_info | System info like hardware model, vendor name, CPU type etc. |
| Time        | Current time in different formats                           |
| Uptime      | System Uptime                                               |

### System Properties Tables

#### Hardware Properties

| Table Name     | Brief Description                                                                                 |
|----------------|---------------------------------------------------------------------------------------------------|
| platform_info  | Platform vendor, firmware version                                                                 |
| cpuid          | Current time in different formats                                                                 |
| win_msr \*     | Details on MSR, their properties and values                                                       |
| intel_me_info  | System Uptime                                                                                     |
| logical_drives | Details for logical drives on the system. A logical drive generally represents a single partition |

#### Operating system

| Table Name  | Brief Description                  |
|-------------|------------------------------------|
| os_version  | operating system name and version. |
| kernel_info | Basic active kernel information.   |

#### Software Properties (Installed programs, browser extensions, patches, versions etc.)

| Table Name        | Brief Description                                                                                                                |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------|
| drivers           | Details for in-use Windows device driver                                                                                         |
| chrome_extensions | Chrome browser extensions.                                                                                                       |
| ie_extensions     | Internet Explorer browser extensions                                                                                             |
| patches           | Lists all the patches applied (does not include patches applied via MSI or downloaded from Windows Update such as Service Packs) |
| programs          | Products as they are installed by Windows Installer                                                                              |
| services          | Lists all installed Windows services and their relevant data                                                                     |

### Network Interfaces and Routing Tables

| Table Name          | Brief Description                                                 |
|---------------------|-------------------------------------------------------------------|
| arp_cache           | Address resolution cache, both static and dynamic (from ARP, NDP) |
| etc_hosts           | Line-parsed /etc/host                                             |
| etc_protocols       | Line-parsed /etc/protocols                                        |
| etc_services        | Line-parsed /etc/services                                         |
| interface_addresses | Network interfaces and relevant metadata.                         |
| interface_details   | Detailed information and stats of network interfaces              |
| routes              | The active route table for the host system                        |

### System Users and Groups Tables

| Table Name      | Brief Description                        |
|-----------------|------------------------------------------|
| groups          | Local system groups                      |
| logged_in_users | Users with an active shell on the system |
| users           | Local system users                       |

### System Configuration Tables

| Table Name      | Brief Description                                                              |
|-----------------|--------------------------------------------------------------------------------|
| autoexec        | Aggregate of executables that will automatically execute on the target machine |
| scheduled_tasks | Lists all of the tasks in the Windows task scheduler.                          |
| startup_items   | Applications and binaries set as user/login startup items                      |
| Certificates    | Certificate Authorities installed in Keychains/ca-bundles.                     |

###  Diagnostics Info Tables

| Table Name      | Brief Description                                          |
|-----------------|------------------------------------------------------------|
| windows_crashes | Extracted information from Windows crash logs (Minidumps). |

### Forensic Info Tables

| Table Name | Brief Description     |
|------------|-----------------------|
| carves     | Forensic File Carves. |

### Activity and Resource Monitoring Tables

| Table Name             | Brief Description                                                                                                                                          |
|------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| appcompat_shims        | Application Compatibility shims are a way to persist malware. This table presents the AppCompat Shim information from the registry in a nice format        |
| authenticode           | File (executable, bundle, installer, disk) code signing status                                                                                             |
| file                   | Interactive filesystem attributes and metadata.                                                                                                            |
| hash                   | Filesystem hash data                                                                                                                                       |
| listening_ports        | Processes with listening (bound) network sockets/ports                                                                                                     |
| pipes                  | Named and Anonymous pipes.                                                                                                                                 |
| process_memory_map     | Process memory mapped files and pseudo device/regions.                                                                                                     |
| process_open_sockets   | Processes which have open network sockets on the system.                                                                                                   |
| processes              | All running processes on the host system.                                                                                                                  |
| win_process_handles \* | Can be used to query the open handles by any process across the system (including the system privileged processes)                                         |
| registry               | All of the Windows registry hives.                                                                                                                         |
| shared_resources       | Displays shared resources on a computer system running Windows. This may be a disk drive, printer, inter process communication, or another sharable device |

### Real Time Activity Monitoring (Evented)

| Table Name                    | Brief Description                                                                                                                              |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| win_file_events \*            | Captures the file creation, deletion and modification events based on the config                                                               |
| win_http_events \*            | Captures the http requests and targets                                                                                                         |
| win_image_load_events \*      | Captures the load of binary executable files and their certificate information                                                                 |
| win_pefile_events \*          | Captures the creation, deletion, modification of executable files                                                                              |
| win_process_events \*         | Captures the creation and termination of processes                                                                                             |
| win_process_open_events \*    | Captures the events when a process opens another process with the intent to read/write into remote process’ virtual memory (VM_READ\|VM_WRITE) |
| win_removable_media_events \* | Captures the insertion of a removable media                                                                                                    |
| win_socket_events \*          | Captures the socket operations like Accept, listen, close                                                                                      |
| win_dns_events \*             | Captures the DNS look ups                                                                                                                      |
| win_dns_response_events \*    | Captures the DNS resolutions and can assist mapping the DNS look up to the source process based on IP address in win_socket_events             |
| win_registry_events \*        | Captures the creation, deletion, modification of registry keys and values                                                                      |
| win_remote_thread_events \*   | Captures the remote thread creations                                                                                                           |

### Performance Monitoring Tables

| Table Name                | Brief Description                                                                                |
|---------------------------|--------------------------------------------------------------------------------------------------|
| physical_disk_performance | Provides raw data from performance counters that monitor hard or fixed disk drives on the system |

### Security Table

| Table Name         | Brief Description                                                                                                                                                                                                                                                             |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| win_yara_events \* | Captures the yara matches based on input rules                                                                                                                                                                                                                                |
| win_epp_table \*   | Name, type, state and status of signatures of endpoint security products deployed on the endpoint                                                                                                                                                                             |
| win_obfuscated_ps  | Powershell based attacks can be ‘fileless’. Many of these attacks obfuscate powershell scripts and allow them evade detection. These scripts get logged in Windows Event Viewer. PolyLogyx uses AI and statistical to analyze the script logs to determine their obfuscation. |

### Log Monitoring Tables

| Table Name     | Brief Description  |
|----------------|--------------------|
| windows_events | Windows Event logs |

### WMI related tables

| Table Name                  | Brief Description                                                          |
|-----------------------------|----------------------------------------------------------------------------|
| wmi_cli_event_consumers     | WMI CommandLineEventConsumer, which can be used for persistance on Windows |
| wmi_event_filters           | Lists WMI event filters.                                                   |
| wmi_filter_consumer_binding | Lists the relationship between event consumers and filters.                |
| wmi_script_event_consumers  | WMI ActiveScriptEventConsumer, which can be used for persistance on Window |

### Miscellaneous

Besides these generic properties, osquery on windows also exports the properties
of certain third-party software (including itself via following tables)

| Table Name          | Brief Description                                       |
|---------------------|---------------------------------------------------------|
| carbon_black_info   | Returns information about a Carbon Black sensor install |
| chocolatey_packages | Chocolatey packages installed in a system.              |
| python_packages     | Python packages installed in a system.                  |

Schema for Tables
-----------------

### Table Name: system_info

| Column             | Type    | Description                                 |
|--------------------|---------|---------------------------------------------|
| hostname           | TEXT    | Network hostname including domain           |
| uuid               | TEXT    | Unique ID provided by the system            |
| cpu_type           | TEXT    | CPU type                                    |
| cpu_subtype        | TEXT    | CPU subtype                                 |
| cpu_brand          | TEXT    | CPU brand string, contains vendor and model |
| cpu_physical_cores | INTEGER | Max number of CPU physical cores            |
| cpu_logical_cores  | INTEGER | Max number of CPU logical cores             |
| cpu_microcode      | TEXT    | Microcode version                           |
| physical_memory    | BIGINT  | Total physical memory in bytes              |
| hardware_vendor    | TEXT    | Hardware or board vendor                    |
| hardware_model     | TEXT    | Hardware or board model                     |
| hardware_version   | TEXT    | Hardware or board version                   |
| hardware_serial    | TEXT    | Device or board serial number               |
| computer_name      | TEXT    | Friendly computer name (optional)           |
| local_hostname     | TEXT    | Local hostname (optional)                   |

###  Table Name: Time

| Column         | Type    | Description                                                        |
|----------------|---------|--------------------------------------------------------------------|
| weekday        | TEXT    | Current weekday in the system                                      |
| year           | INTEGER | Current year in the system                                         |
| month          | INTEGER | Current month in the system                                        |
| day            | INTEGER | Current day in the system                                          |
| hour           | INTEGER | Current hour in the system                                         |
| minutes        | INTEGER | Current minutes in the system                                      |
| seconds        | INTEGER | Current seconds in the system                                      |
| timezone       | TEXT    | Current timezone in the system                                     |
| local_time     | INTEGER | Current local UNIX time in the system                              |
| local_timezone | TEXT    | Current local timezone in the system                               |
| unix_time      | INTEGER | Current UNIX time in the system, converted to UTC if --utc enabled |
| timestamp      | TEXT    | Current timestamp (log format) in the system                       |
| datetime       | TEXT    | Current date and time (ISO format) in the system                   |
| iso_8601       | TEXT    | Current time (ISO format) in the system                            |

###  Table Name: Uptime

| Column        | Type    | Description          |
|---------------|---------|----------------------|
| days          | INTEGER | Days of uptime       |
| hours         | INTEGER | Hours of uptime      |
| minutes       | INTEGER | Minutes of uptime    |
| seconds       | INTEGER | Seconds of uptime    |
| total_seconds | BIGINT  | Total uptime seconds |

###  Table Name: platform_info

| Column      | Type    | Description                              |
|-------------|---------|------------------------------------------|
| vendor      | TEXT    | Platform code vendor                     |
| version     | TEXT    | Platform code version                    |
| date        | TEXT    | Self-reported platform code update date  |
| revision    | TEXT    | BIOS major and minor revision            |
| address     | TEXT    | Relative address of firmware mapping     |
| size        | TEXT    | Size in bytes of firmware                |
| volume_size | INTEGER | (Optional) size of firmware volume       |
| extra       | TEXT    | Platform-specific additional information |

###  Table Name: cpu_id

| Column          | Type    | Description                             |
|-----------------|---------|-----------------------------------------|
| feature         | TEXT    | Present feature flags                   |
| value           | TEXT    | Bit value or string                     |
| output_register | TEXT    | Register used to for feature value      |
| output_bit      | INTEGER | Bit in register value for feature value |
| input_eax       | TEXT    | Value of EAX used                       |

###  Table Name: win_msr

**\***
[Details](https://www.intel.com/content/dam/www/public/us/en/documents/manuals/64-ia-32-architectures-software-developer-vol-3b-part-2-manual.pdf)
on MSR, their properties and values

| Column             | Type    | Description             |
|--------------------|---------|-------------------------|
| turbo_disabled     | INTEGER | CPU properties from MSR |
| turbo_ratio_limit  | INTEGER | CPU properties from MSR |
| platform_info      | INTEGER | CPU properties from MSR |
| perf_status        | INTEGER | CPU properties from MSR |
| perf_ctl           | INTEGER | CPU properties from MSR |
| feature_control    | INTEGER | CPU properties from MSR |
| rapl_power_limit   | INTEGER | CPU properties from MSR |
| rapl_energy_status | INTEGER | CPU properties from MSR |
| rapl_power_units   | INTEGER | CPU properties from MSR |

###  Table Name: intel_me_info

| Column  | Type | Description      |
|---------|------|------------------|
| version | TEXT | Intel ME version |

### Table Name: logical_drives

| Column         | Type    | Description                                           |
|----------------|---------|-------------------------------------------------------|
| device_id      | TEXT    | The drive id, usually the drive name, e.g., 'C:'.     |
| type           | TEXT    | The type of disk-drive this logical drive represents. |
| free_space     | BIGINT  | The amount of free space, in bytes, of the drive.     |
| size           | BIGINT  | The total amount of space, in bytes, of the drive.    |
| file_system    | TEXT    | The file system of the drive.                         |
| boot_partition | INTEGER | True if Windows booted from this drive.               |

###  Table Name: os_version

| Column        | Type    | Description                                   |
|---------------|---------|-----------------------------------------------|
| name          | TEXT    | Distribution or product name                  |
| version       | TEXT    | Pretty, suitable for presentation, OS version |
| major         | INTEGER | Major release version                         |
| minor         | INTEGER | Minor release version                         |
| patch         | INTEGER | Optional patch release                        |
| build         | TEXT    | Optional build-specific or variant string     |
| platform      | TEXT    | OS Platform or ID                             |
| platform_like | TEXT    | Closely related platforms                     |
| codename      | TEXT    | OS version codename                           |

### Table Name: kernel_info

| Column    | Type | Description              |
|-----------|------|--------------------------|
| version   | TEXT | Kernel version           |
| arguments | TEXT | Kernel arguments         |
| path      | TEXT | Kernel path              |
| device    | TEXT | Kernel device identifier |

### Table Name: drivers

| Column       | Type    | Description                         |
|--------------|---------|-------------------------------------|
| device_id    | TEXT    | Device ID                           |
| device_name  | TEXT    | Device name                         |
| image        | TEXT    | Path to driver image file           |
| description  | TEXT    | Driver description                  |
| service      | TEXT    | Driver service name, if one exists  |
| service_key  | TEXT    | Driver service registry key         |
| version      | TEXT    | Driver version                      |
| inf          | TEXT    | Associated inf file                 |
| class        | TEXT    | Device/driver class name            |
| provider     | TEXT    | Driver provider                     |
| manufacturer | TEXT    | Device manufacturer                 |
| driver_key   | TEXT    | Driver key                          |
| date         | BIGINT  | Driver date                         |
| signed       | INTEGER | Whether the driver is signed or not |

### Table Name: chrome_extensions

| Column      | Type    | Description                                         |
|-------------|---------|-----------------------------------------------------|
| uid         | BIGINT  | The local user that owns the extension              |
| name        | TEXT    | Extension display name                              |
| identifier  | TEXT    | Extension identifier                                |
| version     | TEXT    | Extension-supplied version                          |
| description | TEXT    | Extension-optional description                      |
| locale      | TEXT    | Default locale supported by extension               |
| update_url  | TEXT    | Extension-supplied update URI                       |
| author      | TEXT    | Optional extension author                           |
| persistent  | INTEGER | 1 If extension is persistent across all tabs else 0 |
| path        | TEXT    | Path to extension folder                            |

###  Table Name: ie_extensions

| Column        | Type | Description               |
|---------------|------|---------------------------|
| name          | TEXT | Extension display name    |
| registry_path | TEXT | Extension identifier      |
| version       | TEXT | Version of the executable |
| path          | TEXT | Path to executable        |

### Table Name: patches

| Column       | Type | Description                                                                                                 |
|--------------|------|-------------------------------------------------------------------------------------------------------------|
| csname       | TEXT | The name of the host the patch is installed on.                                                             |
| hotfix_id    | TEXT | The KB ID of the patch.                                                                                     |
| caption      | TEXT | Short description of the patch.                                                                             |
| description  | TEXT | Fuller description of the patch.                                                                            |
| fix_comments | TEXT | Additional comments about the patch.                                                                        |
| installed_by | TEXT | The system context in which the patch as installed.                                                         |
| install_date | TEXT | Indicates when the patch was installed. Lack of a value does not indicate that the patch was not installed. |
| installed_on | TEXT | The date when the patch was installed.                                                                      |

###  Table Name: programs

| Column             | Type | Description                                                                                     |
|--------------------|------|-------------------------------------------------------------------------------------------------|
| name               | TEXT | Commonly used product name.                                                                     |
| version            | TEXT | Product version information.                                                                    |
| install_location   | TEXT | The installation location directory of the product.                                             |
| install_source     | TEXT | The installation source of the product.                                                         |
| language           | TEXT | The language of the product.                                                                    |
| publisher          | TEXT | Name of the product supplier.                                                                   |
| uninstall_string   | TEXT | Path and filename of the uninstaller.                                                           |
| install_date       | TEXT | Date that this product was installed on the system.                                             |
| identifying_number | TEXT | Product identification such as a serial number on software, or a die number on a hardware chip. |

###  Table Name: services

| Column            | Type    | Description                                                                                                                                                                                                                |
|-------------------|---------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name              | TEXT    | Service name                                                                                                                                                                                                               |
| service_type      | TEXT    | Service Type: OWN_PROCESS, SHARE_PROCESS and maybe Interactive (can interact with the desktop)                                                                                                                             |
| display_name      | TEXT    | Service Display name                                                                                                                                                                                                       |
| status            | TEXT    | Service Current status: STOPPED, START_PENDING, STOP_PENDING, RUNNING, CONTINUE_PENDING, PAUSE_PENDING, PAUSED                                                                                                             |
| pid               | INTEGER | The Process ID of the service                                                                                                                                                                                              |
| start_type        | TEXT    | Service start type: BOOT_START, SYSTEM_START, AUTO_START, DEMAND_START, DISABLED                                                                                                                                           |
| win32_exit_code   | INTEGER | The error code that the service uses to report an error that occurs when it is starting or stopping                                                                                                                        |
| service_exit_code | INTEGER | The service-specific error code that the service returns when an error occurs while the service is starting or stopping                                                                                                    |
| path              | TEXT    | Path to Service Executable                                                                                                                                                                                                 |
| module_path       | TEXT    | Path to ServiceDll                                                                                                                                                                                                         |
| description       | TEXT    | Service Description                                                                                                                                                                                                        |
| user_account      | TEXT    | The name of the account that the service process will be logged on as when it runs. This name can be of the form Domain\\UserName. If the account belongs to the built-in domain, the name can be of the form .\\UserName. |

###  Table Name: arp_cache

| Column    | Type | Description                          |
|-----------|------|--------------------------------------|
| address   | TEXT | IPv4 address target                  |
| mac       | TEXT | MAC address of broadcasted address   |
| interface | TEXT | Interface of the network for the MAC |
| permanent | TEXT | 1 for true, 0 for false              |

### Table Name: etc_hosts

| Column    | Type | Description        |
|-----------|------|--------------------|
| address   | TEXT | IP address mapping |
| hostnames | TEXT | Raw hosts mapping  |

###  Table Name: etc_protocols

| Column  | Type    | Description                       |
|---------|---------|-----------------------------------|
| name    | TEXT    | Protocol name                     |
| number  | INTEGER | Protocol number                   |
| alias   | TEXT    | Protocol alias                    |
| comment | TEXT    | Comment with protocol description |

### Table Name: etc_services

| Column   | Type    | Description                                                |
|----------|---------|------------------------------------------------------------|
| name     | TEXT    | Service name                                               |
| port     | INTEGER | Service port number                                        |
| protocol | TEXT    | Transport protocol (TCP/UDP)                               |
| aliases  | TEXT    | Optional space separated list of other names for a service |
| comment  | TEXT    | Optional comment for a service.                            |

### Table Name: interface_details

| Column                         | Type    | Description                                                                                                                                                       |
|--------------------------------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| interface                      | TEXT    | Interface name                                                                                                                                                    |
| mac                            | TEXT    | MAC of interface (optional)                                                                                                                                       |
| type                           | INTEGER | Interface type (includes virtual)                                                                                                                                 |
| mtu                            | INTEGER | Network MTU                                                                                                                                                       |
| metric                         | INTEGER | Metric based on the speed of the interface                                                                                                                        |
| flags                          | INTEGER | Flags (netdevice) for the device                                                                                                                                  |
| ipackets                       | BIGINT  | Input packets                                                                                                                                                     |
| opackets                       | BIGINT  | Output packets                                                                                                                                                    |
| ibytes                         | BIGINT  | Input bytes                                                                                                                                                       |
| obytes                         | BIGINT  | Output bytes                                                                                                                                                      |
| ierrors                        | BIGINT  | Input errors                                                                                                                                                      |
| oerrors                        | BIGINT  | Output errors                                                                                                                                                     |
| idrops                         | BIGINT  | Input drops                                                                                                                                                       |
| odrops                         | BIGINT  | Output drops                                                                                                                                                      |
| collisions                     | BIGINT  | Packet Collisions detected                                                                                                                                        |
| last_change                    | BIGINT  | Time of last device modification (optional)                                                                                                                       |
| link_speed                     | BIGINT  | Interface speed in Mb/s                                                                                                                                           |
| pci_slot                       | TEXT    | PCI slot number                                                                                                                                                   |
| friendly_name                  | TEXT    | The friendly display name of the interface.                                                                                                                       |
| description                    | TEXT    | Short description of the object—a one-line string.                                                                                                                |
| manufacturer                   | TEXT    | Name of the network adapter's manufacturer.                                                                                                                       |
| connection_id                  | TEXT    | Name of the network connection as it appears in the Network Connections Control Panel program.                                                                    |
| connection_status              | TEXT    | State of the network adapter connection to the network.                                                                                                           |
| enabled                        | INTEGER | Indicates whether the adapter is enabled or not.                                                                                                                  |
| physical_adapter               | INTEGER | Indicates whether the adapter is a physical or a logical adapter.                                                                                                 |
| speed                          | INTEGER | Estimate of the current bandwidth in bits per second.                                                                                                             |
| service                        | TEXT    | The name of the service the network adapter uses.                                                                                                                 |
| dhcp_enabled                   | INTEGER | If TRUE, the dynamic host configuration protocol (DHCP) server automatically assigns an IP address to the computer system when establishing a network connection. |
| dhcp_lease_expires             | TEXT    | Expiration date and time for a leased IP address that was assigned to the computer by the dynamic host configuration protocol (DHCP) server.                      |
| dhcp_lease_obtained            | TEXT    | Date and time the lease was obtained for the IP address assigned to the computer by the dynamic host configuration protocol (DHCP) server.                        |
| dhcp_server                    | TEXT    | IP address of the dynamic host configuration protocol (DHCP) server.                                                                                              |
| dns_domain                     | TEXT    | Organization name followed by a period and an extension that indicates the type of organization, such as 'microsoft.com'.                                         |
| dns_domain_suffix_search_order | TEXT    | Array of DNS domain suffixes to be appended to the end of host names during name resolution.                                                                      |
| dns_host_name                  | TEXT    | Host name used to identify the local computer for authentication by some utilities.                                                                               |
| dns_server_search_order        | TEXT    | Array of server IP addresses to be used in querying for DNS servers.                                                                                              |
| interface                      | TEXT    | Interface name                                                                                                                                                    |

###  Table Name: interface_addresses 

| Column         | Type | Description                                       |
|----------------|------|---------------------------------------------------|
| interface      | TEXT | Interface name                                    |
| address        | TEXT | Specific address for interface                    |
| mask           | TEXT | Interface netmask                                 |
| broadcast      | TEXT | Broadcast address for the interface               |
| point_to_point | TEXT | PtP address for the interface                     |
| type           | TEXT | Type of address. One of dhcp, manual, auto, other |
| friendly_name  | TEXT | The friendly display name of the interface.       |

###  Table Name: routes

| Column      | Type    | Description                             |
|-------------|---------|-----------------------------------------|
| destination | TEXT    | Destination IP address                  |
| netmask     | TEXT    | Netmask length                          |
| gateway     | TEXT    | Route gateway                           |
| source      | TEXT    | Route source                            |
| flags       | INTEGER | Flags to describe route                 |
| interface   | TEXT    | Route local interface                   |
| mtu         | INTEGER | Maximum Transmission Unit for the route |
| metric      | INTEGER | Cost of route. Lowest is preferred      |
| type        | TEXT    | Type of route                           |

### Table Name: groups

| Column     | Type   | Description                                   |
|------------|--------|-----------------------------------------------|
| gid        | BIGINT | Unsigned int64 group ID                       |
| gid_signed | BIGINT | A signed int64 version of gid                 |
| groupname  | TEXT   | Canonical local group name                    |
| group_sid  | TEXT   | Unique group ID                               |
| comment    | TEXT   | Remarks or comments associated with the group |

### Table Name: logged_in_users

| Column     | Type   | Description                                   |
|------------|--------|-----------------------------------------------|
| gid        | BIGINT | Unsigned int64 group ID                       |
| gid_signed | BIGINT | A signed int64 version of gid                 |
| groupname  | TEXT   | Canonical local group name                    |
| group_sid  | TEXT   | Unique group ID                               |
| comment    | TEXT   | Remarks or comments associated with the group |
| gid        | BIGINT | Unsigned int64 group ID                       |

###  Table Name: users

| Column      | Type   | Description                                                         |
|-------------|--------|---------------------------------------------------------------------|
| uid         | BIGINT | User ID                                                             |
| gid         | BIGINT | Group ID (unsigned)                                                 |
| uid_signed  | BIGINT | User ID as int64 signed (Apple)                                     |
| gid_signed  | BIGINT | Default group ID as int64 signed (Apple)                            |
| username    | TEXT   | Username                                                            |
| description | TEXT   | Optional user description                                           |
| directory   | TEXT   | User's home directory                                               |
| shell       | TEXT   | User's configured default shell                                     |
| uuid        | TEXT   | User's UUID (Apple)                                                 |
| type        | TEXT   | Whether the account is roaming (domain), local, or a system profile |

###  Table Name: autoexec

| Column | Type | Description                       |
|--------|------|-----------------------------------|
| path   | TEXT | Path to the executable            |
| name   | TEXT | Name of the program               |
| source | TEXT | Source table of the autoexec item |

###  Table Name: scheduled_tasks

| Column | Type | Description                       |
|--------|------|-----------------------------------|
| path   | TEXT | Path to the executable            |
| name   | TEXT | Name of the program               |
| source | TEXT | Source table of the autoexec item |
| path   | TEXT | Path to the executable            |
| name   | TEXT | Name of the program               |
| source | TEXT | Source table of the autoexec item |
| path   | TEXT | Path to the executable            |
| name   | TEXT | Name of the program               |
| source | TEXT | Source table of the autoexec item |
| path   | TEXT | Path to the executable            |

###  Table Name: startup_items

| Column   | Type | Description                                |
|----------|------|--------------------------------------------|
| name     | TEXT | Name of startup item                       |
| path     | TEXT | Path of startup item                       |
| args     | TEXT | Arguments provided to startup executable   |
| type     | TEXT | Startup Item or Login Item                 |
| source   | TEXT | Directory or plist containing startup item |
| status   | TEXT | Startup status; either enabled or disabled |
| username | TEXT | The user associated with the startup item  |
| name     | TEXT | Name of startup item                       |

###  Table Name: Certificates

| Column            | Type    | Description                                        |
|-------------------|---------|----------------------------------------------------|
| common_name       | TEXT    | Certificate CommonName                             |
| subject           | TEXT    | Certificate distinguished name                     |
| issuer            | TEXT    | Certificate issuer distinguished name              |
| ca                | INTEGER | 1 if CA: true (certificate is an authority) else 0 |
| self_signed       | INTEGER | 1 if self-signed, else 0                           |
| not_valid_before  | TEXT    | Lower bound of valid date                          |
| not_valid_after   | TEXT    | Certificate expiration data                        |
| signing_algorithm | TEXT    | Signing algorithm used                             |
| key_algorithm     | TEXT    | Key algorithm used                                 |
| key_strength      | TEXT    | Key size used for RSA/DSA, or curve name           |
| key_usage         | TEXT    | Certificate key usage and extended key usage       |
| subject_key_id    | TEXT    | SKID an optionally included SHA1                   |
| authority_key_id  | TEXT    | AKID an optionally included SHA1                   |
| sha1              | TEXT    | SHA1 hash of the raw certificate contents          |
| path              | TEXT    | Path to Keychain or PEM bundle                     |
| serial            | TEXT    | Certificate serial number                          |

###  Table Name: windows_crashes

| Column            | Type    | Description                                                   |
|-------------------|---------|---------------------------------------------------------------|
| datetime          | TEXT    | Timestamp (log format) of the crash                           |
| module            | TEXT    | Path of the crashed module within the process                 |
| path              | TEXT    | Path of the executable file for the crashed process           |
| pid               | BIGINT  | Process ID of the crashed process                             |
| tid               | BIGINT  | Thread ID of the crashed thread                               |
| version           | TEXT    | File version info of the crashed process                      |
| process_uptime    | BIGINT  | Uptime of the process in seconds                              |
| stack_trace       | TEXT    | Multiple stack frames from the stack trace                    |
| exception_code    | TEXT    | The Windows exception code                                    |
| exception_message | TEXT    | The NTSTATUS error message associated with the exception code |
| exception_address | TEXT    | Address (in hex) where the exception occurred                 |
| registers         | TEXT    | The values of the system registers                            |
| command_line      | TEXT    | Command-line string passed to the crashed process             |
| current_directory | TEXT    | Current working directory of the crashed process              |
| username          | TEXT    | Username of the user who ran the crashed process              |
| machine_name      | TEXT    | Name of the machine where the crash happened                  |
| major_version     | INTEGER | Windows major version of the machine                          |
| minor_version     | INTEGER | Windows minor version of the machine                          |
| build_number      | INTEGER | Windows build number of the crashing machine                  |
| type              | TEXT    | Type of crash log                                             |
| crash_path        | TEXT    | Path of the log file                                          |

###  Table Name: carves

| Column     | Type    | Description                                                       |
|------------|---------|-------------------------------------------------------------------|
| time       | BIGINT  | Time at which the carve was kicked off                            |
| sha256     | TEXT    | A SHA256 sum of the carved archive                                |
| size       | INTEGER | Size of the carved archive                                        |
| path       | TEXT    | The path of the requested carve                                   |
| status     | TEXT    | Status of the carve, can be STARTING, PENDING, SUCCESS, or FAILED |
| carve_guid | TEXT    | Identifying value of the carve session                            |
| carve      | INTEGER | Set this value to '1' to start a file carve                       |

###  Table Name: appcompat_shims

| Column       | Type    | Description                                                                     |
|--------------|---------|---------------------------------------------------------------------------------|
| executable   | TEXT    | Name of the executable that is being shimmed. This is pulled from the registry. |
| path         | TEXT    | This is the path to the SDB database.                                           |
| description  | TEXT    | Description of the SDB.                                                         |
| install_time | INTEGER | Install time of the SDB                                                         |
| type         | TEXT    | Type of the SDB database.                                                       |
| sdb_id       | TEXT    | Unique GUID of the SDB.                                                         |

###  Table Name: authenticode

| Column                | Type | Description                                             |
|-----------------------|------|---------------------------------------------------------|
| path                  | TEXT | Must provide a path or directory                        |
| original_program_name | TEXT | The original program name that the publisher has signed |
| serial_number         | TEXT | The certificate serial number                           |
| issuer_name           | TEXT | The certificate issuer name                             |
| subject_name          | TEXT | The certificate subject name                            |
| result                | TEXT | The signature check result                              |

###  Table Name: file

| Column     | Type    | Description                             |
|------------|---------|-----------------------------------------|
| path       | TEXT    | Absolute file path                      |
| directory  | TEXT    | Directory of file(s)                    |
| filename   | TEXT    | Name portion of file path               |
| inode      | BIGINT  | Filesystem inode number                 |
| uid        | BIGINT  | Owning user ID                          |
| gid        | BIGINT  | Owning group ID                         |
| mode       | TEXT    | Permission bits                         |
| device     | BIGINT  | Device ID (optional)                    |
| size       | BIGINT  | Size of file in bytes                   |
| block_size | INTEGER | Block size of filesystem                |
| atime      | BIGINT  | Last access time                        |
| mtime      | BIGINT  | Last modification time                  |
| ctime      | BIGINT  | Last status change time                 |
| btime      | BIGINT  | (B)irth or (cr)eate time                |
| hard_links | INTEGER | Number of hard links                    |
| symlink    | INTEGER | 1 if the path is a symlink, otherwise 0 |
| type       | TEXT    | File status                             |

###  Table Name: hash

| Column    | Type | Description                             |
|-----------|------|-----------------------------------------|
| path      | TEXT | Must provide a path or directory        |
| directory | TEXT | Must provide a path or directory        |
| md5       | TEXT | MD5 hash of provided filesystem data    |
| sha1      | TEXT | SHA1 hash of provided filesystem data   |
| sha256    | TEXT | SHA256 hash of provided filesystem data |

###  Table Name: listening_ports

| Column        | Type    | Description                               |
|---------------|---------|-------------------------------------------|
| pid           | INTEGER | Process (or thread) ID                    |
| port          | INTEGER | Transport layer port                      |
| protocol      | INTEGER | Transport protocol (TCP/UDP)              |
| family        | INTEGER | Network protocol (IPv4, IPv6)             |
| address       | TEXT    | Specific address for bind                 |
| fd            | BIGINT  | Socket file descriptor number             |
| socket        | BIGINT  | Socket handle or inode number             |
| path          | TEXT    | Path for UNIX domain sockets              |
| net_namespace | TEXT    | The inode number of the network namespace |

###  Table Name: pipes

| Column        | Type    | Description                                                                                                                |
|---------------|---------|----------------------------------------------------------------------------------------------------------------------------|
| pid           | BIGINT  | Process ID of the process to which the pipe belongs                                                                        |
| name          | TEXT    | Name of the pipe                                                                                                           |
| instances     | INTEGER | Number of instances of the named pipe                                                                                      |
| max_instances | INTEGER | The maximum number of instances creatable for this pipe                                                                    |
| flags         | TEXT    | The flags indicating whether this pipe connection is a server or client end, and if the pipe for sending messages or bytes |

### Table Name: process_memory_map

| Column      | Type    | Description                                    |
|-------------|---------|------------------------------------------------|
| pid         | INTEGER | Process (or thread) ID                         |
| start       | TEXT    | Virtual start address (hex)                    |
| end         | TEXT    | Virtual end address (hex)                      |
| permissions | TEXT    | r=read, w=write, x=execute, p=private (cow)    |
| offset      | BIGINT  | Offset into mapped path                        |
| device      | TEXT    | MA:MI Major/minor device ID                    |
| inode       | INTEGER | Mapped path inode, 0 means uninitialized (BSS) |
| path        | TEXT    | Path to mapped file or mapped type             |
| pseudo      | INTEGER | 1 If path is a pseudo path, else 0             |

###  Table Name: process_open_sockets

| Column         | Type    | Description                                        |
|----------------|---------|----------------------------------------------------|
| pid            | INTEGER | Process (or thread) ID                             |
| fd             | BIGINT  | Socket file descriptor number                      |
| socket         | BIGINT  | Socket handle or inode number                      |
| family         | INTEGER | Network protocol (IPv4, IPv6)                      |
| protocol       | INTEGER | Transport protocol (TCP/UDP)                       |
| local_address  | TEXT    | Socket local address                               |
| remote_address | TEXT    | Socket remote address                              |
| local_port     | INTEGER | Socket local port                                  |
| remote_port    | INTEGER | Socket remote port                                 |
| path           | TEXT    | For UNIX sockets (family=AF_UNIX), the domain path |
| state          | TEXT    | TCP socket state                                   |
| net_namespace  | TEXT    | The inode number of the network namespace          |

### Table Name: processes

| Column             | Type    | Description                                        |
|--------------------|---------|----------------------------------------------------|
| pid                | BIGINT  | Process (or thread) ID                             |
| name               | TEXT    | The process path or shorthand argv[0]              |
| path               | TEXT    | Path to executed binary                            |
| cmdline            | TEXT    | Complete argv                                      |
| state              | TEXT    | Process state                                      |
| cwd                | TEXT    | Process current working directory                  |
| root               | TEXT    | Process virtual root directory                     |
| uid                | BIGINT  | Unsigned user ID                                   |
| gid                | BIGINT  | Unsigned group ID                                  |
| euid               | BIGINT  | Unsigned effective user ID                         |
| egid               | BIGINT  | Unsigned effective group ID                        |
| suid               | BIGINT  | Unsigned saved user ID                             |
| sgid               | BIGINT  | Unsigned saved group ID                            |
| on_disk            | INTEGER | The process path exists yes=1, no=0, unknown=-1    |
| wired_size         | BIGINT  | Bytes of unpagable memory used by process          |
| resident_size      | BIGINT  | Bytes of private memory used by process            |
| total_size         | BIGINT  | Total virtual memory size                          |
| user_time          | BIGINT  | CPU time spent in user space                       |
| system_time        | BIGINT  | CPU time spent in kernel space                     |
| disk_bytes_read    | BIGINT  | Bytes read from disk                               |
| disk_bytes_written | BIGINT  | Bytes written to disk                              |
| start_time         | BIGINT  | Process start in seconds since boot (non-sleeping) |
| parent             | BIGINT  | Process parent's PID                               |
| pgroup             | BIGINT  | Process group                                      |
| threads            | INTEGER | Number of threads used by process                  |
| nice               | INTEGER | Process nice level (-20 to 20, default 0)          |
| is_elevated_token  | INTEGER | Process uses elevated token yes=1, no=0            |
| cgroup_namespace   | TEXT    | cgroup namespace inode                             |
| ipc_namespace      | TEXT    | ipc namespace inode                                |
| mnt_namespace      | TEXT    | mnt namespace inode                                |
| net_namespace      | TEXT    | net namespace inode                                |
| pid_namespace      | TEXT    | pid namespace inode                                |
| user_namespace     | TEXT    | user namespace inode                               |
| uts_namespace      | TEXT    | uts namespace inode                                |

###  Table Name: win_process_handles

| Column      | Type    | Description                         |
|-------------|---------|-------------------------------------|
| Pid         | INTEGER | Process ID                          |
| handle_type |         | TEXT                                |
| object_name | TEXT    | Name of the object                  |
| access_mask | INTEGER | Access mask at the time of creation |

###  Table Name: registry

| Column | Type   | Description                                                 |
|--------|--------|-------------------------------------------------------------|
| key    | TEXT   | Name of the key to search for                               |
| path   | TEXT   | Full path to the value                                      |
| name   | TEXT   | Name of the registry value entry                            |
| type   | TEXT   | Type of the registry value, or 'subkey' if item is a subkey |
| data   | TEXT   | Data content of registry value                              |
| mtime  | BIGINT | timestamp of the most recent registry write                 |

### Table Name: shared_resources

| Column          | Type    | Description                                                                                                                                           |
|-----------------|---------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| description     | TEXT    | A textual description of the object                                                                                                                   |
| install_date    | TEXT    | Indicates when the object was installed. Lack of a value does not indicate that the object is not installed.                                          |
| status          | TEXT    | String that indicates the current status of the object.                                                                                               |
| allow_maximum   | INTEGER | Number of concurrent users for this resource has been limited. If True, the value in the MaximumAllowed property is ignored.                          |
| maximum_allowed | INTEGER | Limit on the maximum number of users allowed to use this resource concurrently. The value is only valid if the AllowMaximum property is set to FALSE. |
| name            | TEXT    | Alias given to a path set up as a share on a computer system running Windows.                                                                         |
| path            | TEXT    | Local path of the Windows share.                                                                                                                      |
| type            | INTEGER | Type of resource being shared. Types include: disk drives, print queues, interprocess communications (IPC), and general devices.                      |

###  Table Name: win_file_events

| Column       | Type    | Description                     |
|--------------|---------|---------------------------------|
| Action       | TEXT    | Create/delete/modify            |
| Eid          | TEXT    | Unique event id                 |
| target_path  | TEXT    | File path                       |
| md5          | TEXT    | MD5 hash                        |
| Hashed       | TEXT    | Is hash available?              |
| Uid          | TEXT    | User id                         |
| Time         | INTEGER | Unix time                       |
| utc_time     | TEXT    | UTC Time                        |
| pe_file      | TEXT    | If it is a executable (PE) file |
| Pid          | INTEGER | Process ID                      |
| process_name | TEXT    | Name of the process             |

### Table Name: win_http_events

| Column         | Type    | Description             |
|----------------|---------|-------------------------|
| event_type     | TEXT    | HTTP Event              |
| eid            | TEXT    | Unique event id         |
| pid            | INTEGER | Windows process vent id |
| process_name   | TEXT    | Name of the process     |
| url            | TEXT    | HTTP URL                |
| remote_address | TEXT    | Remote address          |
| remote_port    | INTEGER | Remote port             |
| Time           | INTEGER | Unix Time               |
| utc_time       | TEXT    | UTC Time                |

### Table Name: win_image_load_events

| Column               | Type    | Description                                      |
|----------------------|---------|--------------------------------------------------|
| eid                  | TEXT    | Unique event id                                  |
| pid                  | INTEGER | Process ID loading the image                     |
| image_path           | TEXT    | Path of the image                                |
| uid                  | TEXT    | User Id                                          |
| time                 | INTEGER | Unix Time                                        |
| utc_time             | TEXT    | UTC Time                                         |
| sign_info            | TEXT    | Signed/Unsigned                                  |
| trust_info           | TEXT    | Trusted/untrusted                                |
| num_of_certs         | INTEGER | Number of certificates associated with the image |
| cert_type            | TEXT    | Catalog/Embedded                                 |
| version              | TEXT    | Version                                          |
| pubkey               | TEXT    | Public key of the certificate                    |
| pubkey_length        | TEXT    | , separated length of all the public key         |
| pubkey_signhash_algo | TEXT    | Algorithm for public keys                        |
| issuer_name          | TEXT    | Issuer name                                      |
| subject_name         | TEXT    | Subject of the certificate                       |
| serial_number        | TEXT    | Serial number                                    |
| signature_algo       | TEXT    | Algorithm to sign the certificate                |
| subject_dn           | TEXT    | Subject Domain                                   |
| issuer_dn            | TEXT    | Issuer domain                                    |

### Table Name: win_pefile_events

| Column       | Type    | Description          |
|--------------|---------|----------------------|
| action       | TEXT    | Create/delete/modify |
| eid          | TEXT    | Unique event id      |
| target_path  | TEXT    | File path            |
| md5          | TEXT    | MD5 hash             |
| hashed       | TEXT    | Is hash available?   |
| uid          | TEXT    | User id              |
| time         | INTEGER | Unix time            |
| utc_time     | TEXT    | UTC Time             |
| pid          | INTEGER | Process ID           |
| process_name | TEXT    | Name of the process  |

### Table Name: win_process_events

| Column      | Type    | Description                       |
|-------------|---------|-----------------------------------|
| action      | TEXT    | Created/Terminated                |
| Eid         | TEXT    | Unique event id                   |
| pid         | INTEGER | Process ID                        |
| path        | TEXT    | Path of the process executable    |
| cmdline     | TEXT    | Command line at process start     |
| parent_path | TEXT    | Path of parent process executable |
| owner_uid   | TEXT    | Process owners’ user id           |
| time        | INTEGER | Unix time                         |
| utc_time    | TEXT    | UTC time                          |

### Table Name: win_process_open_events

| Column         | Type    | Description                       |
|----------------|---------|-----------------------------------|
| action         | TEXT    | Process Open Event                |
| eid            | TEXT    | Unique Event Id                   |
| src_pid        | INTEGER | Source PID                        |
| target_pid     | INTEGER | Target PID                        |
| src_path       | TEXT    | Full path to source process image |
| target_path    | TEXT    | Full path to target process image |
| granted_access | TEXT    | Granted Access                    |
| owner_uid      | TEXT    | Source process owner’s user id    |
| time           | INTEGER | Unix Time                         |
| utc_time       | TEXT    | UTC time                          |

### Table Name: win_removable_media_events

| Column                     | Type    | Description                       |
|----------------------------|---------|-----------------------------------|
| eid                        | TEXT    | Unique Event Id                   |
| removable_media_event_type | TEXT    | Media                             |
| uid                        | TEXT    | User id associated with the event |
| time                       | INTEGER | Unix time                         |
| utc_time                   | TEXT    | UTC time                          |

### Table Name: win_socket_events

| Column         | Type    | Description                              |
|----------------|---------|------------------------------------------|
| eid            | TEXT    | Unique Event Id                          |
| event_type     | TEXT    | Socket connection event                  |
| Action         | TEXT    | Socket operation (listen/accept/connect) |
| Time           | INTEGER | Unix time                                |
| utc_time       | TEXT    | UTC time                                 |
| Pid            | INTEGER | Process ID                               |
| Family         | TEXT    | Socket address family (e.g. AF_NET)      |
| Protocol       | INTEGER | Transport protocol identifier            |
| local_address  | TEXT    | Local IP address (source)                |
| remote_address | TEXT    | Remote IP address (destination           |
| local_port     | INTEGER | Local port number for connection         |
| remoter_port   | INTEGER | Remote Port number                       |

### Table Name: win_dns_events

| Column          | Type    | Description                                     |
|-----------------|---------|-------------------------------------------------|
| eid             | TEXT    | Unique Event Id                                 |
| event_type      | TEXT    | DNS Request                                     |
| domain_name     | TEXT    | Domain name looked up                           |
| pid             | INTEGER | Process Id (usually svchost)                    |
| remote_addresss | TEXT    | IP address where the look up request is sent to |
| remote_port     | INTEGER | Remote port                                     |
| time            | INTEGER | Unix Time                                       |
| utc_time        | TEXT    | UTC Time                                        |

### Table Name: win_dns_response_events

| Column          | Type    | Description                                     |
|-----------------|---------|-------------------------------------------------|
| eid             | TEXT    | Unique Event Id                                 |
| event_type      | TEXT    | DNS Response                                    |
| domain_name     | TEXT    | Domain name looked up                           |
| resolved_ip     | TEXT    | IP address resolved                             |
| pid             | INTEGER | Process id (usually svchost)                    |
| remote_addresss | TEXT    | IP address where the look up request is sent to |
| remote_port     | INTEGER | Remote port                                     |
| time            | INTEGER | Unix Time                                       |
| utc_time        | TEXT    | UTC Time                                        |

### Table Name: win_registry_events

| Column          | Type    | Description                                                     |
|-----------------|---------|-----------------------------------------------------------------|
| action          | TEXT    | Type of action performed on registry (Create, Read, Write, Set) |
| eid             | TEXT    | Unique Event ID                                                 |
| pid             | INTEGER | Process id of the originating process                           |
| process_name    | TEXT    | Name of the process                                             |
| target_name     | TEXT    | Name of the registry key being changed/updated                  |
| target_new_name | TEXT    | New name of registry key if/when it is renamed                  |
| value_type      | TEXT    | Registry type being added                                       |
| value_data      | TEXT    | Registry value being added                                      |
| Owner_uid       | TEXT    | User Name                                                       |
| Time            | BIGINT  | Time stamp of the event in Unix format                          |
| Utc_time        | TEXT    | UTC time                                                        |

### Table Name: win_remote_thread_events

| Column      | Type    | Description                       |
|-------------|---------|-----------------------------------|
| eid         | TEXT    | Unique Event Id                   |
| src_pid     | INTEGER | Source PID                        |
| target_pid  | INTEGER | Target PID                        |
| src_path    | TEXT    | Full path to source process image |
| target_path | TEXT    | Full path to target process image |
| owner_uid   | TEXT    | Source process owner’s user id    |
| Time        | INTEGER | Unix Time                         |
| utc_time    | TEXT    | UTC time                          |

### Table Name: win_file_timestomp_events

| Column        | Type    | Description                              |
|---------------|---------|------------------------------------------|
| action        | TEXT    | Create, Write or Delete events           |
| eid           | TEXT    | Unique Event Id                          |
| pid           | INTEGER | Process id                               |
| target_path   | TEXT    | Full file path                           |
| process_name  | TEXT    | Process name                             |
| old_timestamp | TEXT    | Old File Timestamp                       |
| new_timestamp | TEXT    | New File Timestamp                       |
| md5           | TEXT    | MD5 hash of file contents, if available  |
| hashed        | INTEGER | Hash available or not                    |
| pe_file       | TEXT    | True, if it is an executable binary file |
| uid           | TEXT    | User name of file owner                  |
| Time          | INTEGER | Unix Time                                |
| utc_time      | TEXT    | UTC time                                 |

### Table Name: physical_disk_performance

| Column                      | Type    | Description                                                                                        |
|-----------------------------|---------|----------------------------------------------------------------------------------------------------|
| name                        | TEXT    | Name of the physical disk                                                                          |
| avg_disk_bytes_per_read     | BIGINT  | Average number of bytes transferred from the disk during read operations                           |
| avg_disk_bytes_per_write    | BIGINT  | Average number of bytes transferred to the disk during write operations                            |
| avg_disk_read_queue_length  | BIGINT  | Average number of read requests that were queued for the selected disk during the sample interval  |
| avg_disk_write_queue_length | BIGINT  | Average number of write requests that were queued for the selected disk during the sample interval |
| avg_disk_sec_per_read       | INTEGER | Average time, in seconds, of a read operation of data from the disk                                |
| avg_disk_sec_per_write      | INTEGER | Average time, in seconds, of a write operation of data to the disk                                 |
| current_disk_queue_length   | INTEGER | Number of requests outstanding on the disk at the time the performance data is collected           |
| percent_disk_read_time      | BIGINT  | Percentage of elapsed time that the selected disk drive is busy servicing read requests            |
| percent_disk_write_time     | BIGINT  | Percentage of elapsed time that the selected disk drive is busy servicing write requests           |
| percent_disk_time           | BIGINT  | Percentage of elapsed time that the selected disk drive is busy servicing read or write requests   |
| percent_idle_time           | BIGINT  | Percentage of time during the sample interval that the disk was idle                               |

### Table Name: win_yara_events

| Column      | Type    | Description                    |
|-------------|---------|--------------------------------|
| eid         | TEXT    | Unique Event Id                |
| target_path | TEXT    |                                |
| Category    | TEXT    | YARA Rule category             |
| Action      | INTEGER | File action (created/modified) |
| Matches     | TEXT    | The list of rules that matched |
| Count       | INTEGER | Number of rules that matched   |

### Table Name: win_epp_table

| Column            | Type | Description                                                  |
|-------------------|------|--------------------------------------------------------------|
| Product_type      | TEXT | Endpoint security product type (e.g. AntiVirus/FireWall etc) |
| Product_name      | TEXT | Endpoint security product name (as provided by vendor)       |
| Product_state     | TEXT | ON/OFF/SNOOZED                                               |
| Product_signature | TEXT | Status of product signatures (up-to-date/out-dated etc)      |

### Table Name: win_obfuscated_ps

| Column           | Type | Description                                            |
|------------------|------|--------------------------------------------------------|
| Script_id        | TEXT | Script id as recorded in windows event log             |
| Time_created     | TEXT | Time of creation                                       |
| Obfuscated_state | TEXT | Obfuscated/Un-obfuscated                               |
| Obfuscated_score | TEXT | A numerical value to measure the degree of obfuscation |

### Table Name: windows_events

| Column        | Type    | Description                                    |
|---------------|---------|------------------------------------------------|
| time          | BIGINT  | Timestamp the event was received               |
| datetime      | TEXT    | System time at which the event occurred        |
| source        | TEXT    | Source or channel of the event                 |
| provider_name | TEXT    | Provider name of the event                     |
| provider_guid | TEXT    | Provider guid of the event                     |
| eventid       | INTEGER | Event ID of the event                          |
| task          | INTEGER | Task value associated with the event           |
| level         | INTEGER | The severity level associated with the event   |
| keywords      | BIGINT  | A bitmask of the keywords defined in the event |
| data          | TEXT    | Data associated with the event                 |
| eid           | TEXT    | Event ID                                       |

### Table Name: wmi_cli_event_consumers

| Column                | Type | Description                                                                                                                                                                                                            |
|-----------------------|------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name                  | TEXT | Unique name of a consumer.                                                                                                                                                                                             |
| command_line_template | TEXT | Standard string template that specifies the process to be started. This property can be NULL, and the ExecutablePath property is used as the command line.                                                             |
| executable_path       | TEXT | Module to execute. The string can specify the full path and file name of the module to execute, or it can specify a partial name. If a partial name is specified, the current drive and current directory are assumed. |
| class                 | TEXT | The name of the class.                                                                                                                                                                                                 |
| relative_path         | TEXT | Relative path to the class or instance.                                                                                                                                                                                |

### Table Name: wmi_event_filters

| Column         | Type | Description                                                                                                                                                                   |
|----------------|------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name           | TEXT | Unique identifier of an event filter.                                                                                                                                         |
| query          | TEXT | Windows Management Instrumentation Query Language (WQL) event query that specifies the set of events for consumer notification, and the specific conditions for notification. |
| query_language | TEXT | Query language that the query is written in.                                                                                                                                  |
| class          | TEXT | The name of the class.                                                                                                                                                        |
| relative_path  | TEXT | Relative path to the class or instance.                                                                                                                                       |

### Table Name: wmi_filter_consumer_binding

| Column           | Type | Description                                                                                                                                              |
|------------------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| name             | TEXT | Unique identifier for the event consumer.                                                                                                                |
| scripting_engine | TEXT | Name of the scripting engine to use, for example, 'VBScript'. This property cannot be NULL.                                                              |
| script_file_name | TEXT | Name of the file from which the script text is read, intended as an alternative to specifying the text of the script in the ScriptText property.         |
| script_text      | TEXT | Text of the script that is expressed in a language known to the scripting engine. This property must be NULL if the ScriptFileName property is not NULL. |

### Table Name: wmi_script_event_consumers

| Column           | Type | Description                                                                                                                                              |
|------------------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| name             | TEXT | Unique identifier for the event consumer.                                                                                                                |
| scripting_engine | TEXT | Name of the scripting engine to use, for example, 'VBScript'. This property cannot be NULL.                                                              |
| script_file_name | TEXT | Name of the file from which the script text is read, intended as an alternative to specifying the text of the script in the ScriptText property.         |
| script_text      | TEXT | Text of the script that is expressed in a language known to the scripting engine. This property must be NULL if the ScriptFileName property is not NULL. |
| class            | TEXT | The name of the class.                                                                                                                                   |
| relative_path    | TEXT | Relative path to the class or instance.                                                                                                                  |

### Table Name: carbon_black_info

| Column                         | Type    | Description                                                                  |
|--------------------------------|---------|------------------------------------------------------------------------------|
| sensor_id                      | INTEGER | Sensor ID of the Carbon Black sensor                                         |
| config_name                    | TEXT    | Sensor group                                                                 |
| collect_store_files            | INTEGER | If the sensor is configured to send back binaries to the Carbon Black server |
| collect_module_loads           | INTEGER | If the sensor is configured to capture module loads                          |
| collect_module_info            | INTEGER | If the sensor is configured to collect metadata of binaries                  |
| collect_file_mods              | INTEGER | If the sensor is configured to collect file modification events              |
| collect_reg_mods               | INTEGER | If the sensor is configured to collect registry modification events          |
| collect_net_conns              | INTEGER | If the sensor is configured to collect network connections                   |
| collect_processes              | INTEGER | If the sensor is configured to process events                                |
| collect_cross_processes        | INTEGER | If the sensor is configured to cross process events                          |
| collect_emet_events            | INTEGER | If the sensor is configured to EMET events                                   |
| collect_data_file_writes       | INTEGER | If the sensor is configured to collect non binary file writes                |
| collect_process_user_context   | INTEGER | If the sensor is configured to collect the user running a process            |
| collect_sensor_operations      | INTEGER | Unknown                                                                      |
| log_file_disk_quota_mb         | INTEGER | Event file disk quota in MB                                                  |
| log_file_disk_quota_percentage | INTEGER | Event file disk quota in a percentage                                        |
| protection_disabled            | INTEGER | If the sensor is configured to report tamper events                          |
| sensor_ip_addr                 | TEXT    | IP address of the sensor                                                     |
| sensor_backend_server          | TEXT    | Carbon Black server                                                          |
| event_queue                    | INTEGER | Size in bytes of Carbon Black event files on disk                            |
| binary_queue                   | INTEGER | Size in bytes of binaries waiting to be sent to Carbon Black server          |

### Table Name: chocolatey_packages

| Column  | Type | Description                             |
|---------|------|-----------------------------------------|
| name    | TEXT | Package display name                    |
| version | TEXT | Package-supplied version                |
| summary | TEXT | Package-supplied summary                |
| author  | TEXT | Optional package author                 |
| license | TEXT | License under which package is launched |
| path    | TEXT | Path at which this module resides       |

### Table Name: python_packages

| Column  | Type | Description                             |
|---------|------|-----------------------------------------|
| name    | TEXT | Package display name                    |
| version | TEXT | Package-supplied version                |
| summary | TEXT | Package-supplied summary                |
| author  | TEXT | Optional package author                 |
| license | TEXT | License under which package is launched |
| path    | TEXT | Path at which this module resides       |
