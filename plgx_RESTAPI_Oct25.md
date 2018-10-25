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


`X-PolyLogyx-Request-Id - The unique identifier for the API request`

`HTTP/1.1 200 OK`

`X-PolyLogyx-Request-Id: reqVy8wsvmBQN27h4soUE3ZEnA`

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

```
URL: https://<Base URL>/nodes
 
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
 ```

###   Get Node by host_identifier

``` 
URL: https://<Base URL>/nodes/<host_identifier>
 
Request Type: GET
Response: A node with its properties.  
 
Example Response
 
{
	"data": {
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
	},
	"message": "Successfully fetched the node info",
	"status": "success"
}
```

### Get Node Configuration

Get the config for a node.
``` URL: https://<Base URL>/nodes/config/<host_identifier>
 
Request Type: GET
Response: Current config applied on node. 
 
Example Response
 
{
         "status": "success",
         "message": "Successfully fetched the node config",
         "data": {
                        "packs": {},
                        "win_include_paths": {
                              "user_folders": [ 
                              	"C:\\Users\\*\\Downloads\\"
                               ]
                         },
                         "options": {
                              "custom_plgx_LogModeQuiet": "0",
                              "custom_plgx_enable_respserver": "true",
                              "schedule_splay_percent": 10,
                              "custom_plgx_ServerHttpPort": "8280",
                              "custom_plgx_ServerHttpsPort": "443",
                              "logger_tls_compress": true,
                              "custom_plgx_LogFileName": "\\ProgramData\\osquery\\plgx_win_extn\\log\\plgx-agent.log",             
                              "custom_plgx_LogLevel": "1",
                              "custom_plgx_EnableLogging": "true",
                              "disable_watchdog": true,
                              "host_identifier": "uuid",
                              "custom_plgx_ServerPort": "443"
                           },
                           "win_exclude_paths": {
                               "temp_folders": [
                              	"C:\\Users\\*\\Downloads\\exclude\\"
                                ]
                            },
                            "schedule": {
                                "win_file_events": {
                                "query": "select * from win_file_events;",
                                "interval": 10,
                                "description": "win file events"
                                 }
                             }
                  }
}
```

### Get All Configs

Lists all configs available.
```                                                                               	                                            "user_folders": [                                                                                                  	                                               "C:\\Temp\\",                                                                                                	                                               "C:\\Users\\Default\\Downloads\\"
                                                                	]  
                                                                    },
                                             "win_exclude_paths": {
                                                   "temp_folders": [                                                                          	 	  	  	  	  	  	    "C:\\Windows\\Temp\\",                                                                        	 	  	  	  	  	  	    "C:\\Users\\admin\\AppData\\Roaming\\",                                                                            	 	  	  	  	  	  	    "C:\\Windows\\SoftwareDistribution\\",
	 	  	  	  	  	  	    "C:\\Windows\\WinSxS\\"
                                                                 	]
                                                   "prog_files": [                                                                                                                                                                                                                                                                                                       
                                                       "C:\\Program Files (x86)\\Windows Kits\\"
                                                                	]
                                                   "system_folders": [
                                                       "C:\\Windows\\Prefetch\\"
                                                                 	]
                                               },
                                               schedule": {
                                              	"win_file_events":                                                              	                                                "query": "select * from win_file_events;",
                                                  	     "interval": 10,                                                                                        	                                                "description": "win file events"
                                               }
                          }
               	},
                          "id": 1,
                          "name": "poly-config"
                                	},
                                	{
                          "updated_at": "2018-07-27 12:56:24",
                          "created_at": null,
                          "config": {
                                             "win_include_paths": {
                                                   "user_folders": [
	 	  	  	  	  	  	    "C:\\Users\\*\\Downloads\\"
                                	                               ]
                                              },
                                              "win_exclude_paths": {
                                                   "temp_folders": [
                                                       "C:\\Users\\*\\Downloads\\exclude\\"
                                                                   ]
                                               },
                                               "schedule": {
                                                   "win_file_events": {
	 	  	  	  	  	  	    "query": "select * from win_file_events;",
	 	  	  	  	  	  	    "interval": 10,
	 	  	  	  	  	  	    "description": "win file events"
                                                }
                          }
               	},
                          "id": 3,
                          "name": "poly-config-new"
                          }
               ]
}
```

### Config by id

``` URL: https://<Base URL>/configs/<config_id>
 
Request Type: GET
 
Example Response
 
{
	"data": {
		"config": {
			"schedule": {
				"processes": {
					"description": "processes",
					"interval": 10,
					"query": "select * from processes;"
				}
			},
			"win_exclude_paths": {
				"temp_folders": [
					"C:\\Users\\*\\Downloads\\exclude\\"
				]
			},
			"win_include_paths": {
				"user_folders": [
					"C:\\Users\\*\\Downloads\\"
				]
			}
		},
		"created_at": null,
		"id": 3,
		"name": "poly-config-new",
		"updated_at": "2018-08-01 15:04:10"
	},
	"message": "Successfully fetched the data",
	"status": "success"
}
```

### Create a new config

Create a config by mixing a combination of packs, queries and other supported
configurations.
```URL: https://<Base URL>/configs/add
 
Request Type: POST
 
Example Request
 
{
  "name": "poly-config-new",
  "config": {
	"schedule": {
  	"win_file_events": {
    	"query": "select * from win_file_events;",
    	"interval": 10,
    	"description": "win file events"
  	}
	},
	"win_include_paths": {
  	"user_folders": [
    	"C:\\Users\\*\\Downloads\\"
  	]
	},
	"win_exclude_paths": {
  	"temp_folders": [
        "C:\\Users\\*\\Downloads\\exclude\\"
  	]
	}
  }
}
 
Response
{
              	"status": "success",
              	"message": "Successfully added the config",
          	"config_id": 10
}
 ```


### Updating a config 
``` URL: https://<Base URL>/configs/<config_id>
 
Request Type: POST
 
Example Request
 
{
  "config": {
    "schedule": {
      "processes": {
        "description": "processes",
        "interval": 10,
        "query": "select * from processes;"
      }
    },
    "win_exclude_paths": {
      "temp_folders": [
        "C:\\Users\\*\\Downloads\\exclude\\"
      ]
    },
    "win_include_paths": {
      "user_folders": [
        "C:\\Users\\*\\Downloads\\"
      ]
    }
  },
  "name": "poly-config-new"
}

Response

{
	"data": {
		"config": {
			"schedule": {
				"processes": {
					"description": "processes",
					"interval": 10,
					"query": "select * from processes;"
				}
			},
			"win_exclude_paths": {
				"temp_folders": [
					"C:\\Users\\*\\Downloads\\exclude\\"
				]
			},
			"win_include_paths": {
				"user_folders": [
					"C:\\Users\\*\\Downloads\\"
				]
			}
		},
		"created_at": null,
"id": 3,
		"name": "poly-config-new",
		"updated_at": "2018-08-01 15:04:10"
	},
	"message": "Successfully updated the config",
	"status": "success"
}
```

### Assign a Config to a node

By means of assigning a config, one or more scheduled queries can be assigned to
a node.

``` URL: https://<Base URL>/ nodes/config/assign
 
Request Type: POST
 
Example Request
 
{
  "host_identifier": "<host_identifier>",
  "config_id": 10,
}
 
Response
{
              	"status": "success",
              	"message": "Successfully assigned the config"

}
```

###  Remove a Config from a node

By means of assigning a config, one or more scheduled queries can be assigned to
a node.
``` URL: https://<Base URL>/ nodes/config/remove
 
Request Type: POST
 
Example Request
 
{
  "host_identifier": "<host_identifier>"
}
Response
{
              	"status": "success",
              	"message": "Successfully removed the config"
}
```

Scheduled Queries
-----------------

### Scheduled Query Results for an Endpoint Node

Get all the data coming from a schedule query for a specific endpoint node. This
query will retrieve all the results that match from the PolyLogyx server
database.
``` URL: https://<Base URL >/nodes/schedule_query/results
Request Type: POST
 
Example Request:
{
  "host_identifier": "<host_identifier>",
  "query": "win_file_events",
  "start": 0,
  "limit": 100
}
 
 
Example Response:
 
{
     	"status": "success",
     	"message": "Successfully received node schedule query results",
     	"data": [
              	{
                                	"name": "win_file_events",
                                	"timestamp": "2018-07-24T07:09:38",
                                	"node_id": 6,
                                	"action": "added",
                                	"id": 948,
                                	"columns": {
                                                  	"uid": "poly-win10\\test",
                                                  	"pid": "4",
                                                  	"hashed": "1",
                                                  	"target_path": "C:\\Users\\test\\AppData\\Local\\Packages\\Microsoft.Windows.ContentDeliveryManager_cw5n1h2txyewy\\Settings\\settings.dat",
                                                  	"pe_file": "NO",
                                                  	"eid": "0A253E4E-6996-11E8-BF2C-000D3A01FCC1",
                                                  	"time": "1528295378",
                                                  	"action": "WRITE",
                                                  	"utc_time": "Wed Jun  6 14:29:38 2018 UTC",
                                                  	"md5": "6eea00c4dd37725e90c027a60f3ce1a6"
                                	}
              	},
              	{
                                	"name": "win_file_events",
                                	"timestamp": "2018-07-24T07:09:38",
                                	"node_id": 6,
                                	"action": "added",
                                	"id": 949,
                                	"columns": {
                                                  	"uid": "poly-win10\\test",
                                                  	"pid": "4",
                                                  	"hashed": "1",
                                                  	"target_path": "C:\\Users\\test\\AppData\\Local\\Packages\\Microsoft.Windows.ContentDeliveryManager_cw5n1h2txyewy\\Settings\\settings.dat.LOG1",
                                                  	"pe_file": "NO",
URL: https://<Base URL >/nodes/schedule_query/results
Request Type: POST
 
Example Request:
{
  "host_identifier": "<host_identifier>",
  "query": "win_file_events",
  "start": 0,
  "limit": 100
}
 
 
Example Response:
 
{
     	"status": "success",
     	"message": "Successfully received node schedule query results",
     	"data": [
              	{
                                	"name": "win_file_events",
                                	"timestamp": "2018-07-24T07:09:38",
                                	"node_id": 6,
                                	"action": "added",
                                	"id": 948,
                                	"columns": {
                                                  	"uid": "poly-win10\\test",
                                                  	"pid": "4",
                                                  	"hashed": "1",
                                                  	"target_path": "C:\\Users\\test\\AppData\\Local\\Packages\\Microsoft.Windows.ContentDeliveryManager_cw5n1h2txyewy\\Settings\\settings.dat",
                                                  	"pe_file": "NO",
                                                  	"eid": "0A253E4E-6996-11E8-BF2C-000D3A01FCC1",
                                                  	"time": "1528295378",
                                                  	"action": "WRITE",
                                                  	"utc_time": "Wed Jun  6 14:29:38 2018 UTC",
                                                  	"md5": "6eea00c4dd37725e90c027a60f3ce1a6"
                                	}
              	},
              	{
                                	"name": "win_file_events",
                                	"timestamp": "2018-07-24T07:09:38",
                                	"node_id": 6,
                                	"action": "added",
                                	"id": 949,
                                	"columns": {
                                                  	"uid": "poly-win10\\test",
                                                  	"pid": "4",
                                                  	"hashed": "1",
                                                  	"target_path": "C:\\Users\\test\\AppData\\Local\\Packages\\Microsoft.Windows.ContentDeliveryManager_cw5n1h2txyewy\\Settings\\settings.dat.LOG1",
                                                  	"pe_file": "NO",
                                                  	"eid": "0A253E4F-6996-11E8-BF2C-000D3A01FCC1",
                                                  	"time": "1528295378",
                                                  	"action": "WRITE",
                                                  	"utc_time": "Wed Jun  6 14:29:38 2018 UTC",
                                                  	"md5": "a8546005d1e59626d25c8884a1176a27"
                                	}
              	},
              	{
                                	"name": "win_file_events",
                                	"timestamp": "2018-07-24T07:09:38",
                                	"node_id": 6,
                                	"action": "added",
                                	"id": 950,
                                	"columns": {
                                                  	"uid": "BUILTIN\\Administrators",
                                                  	"pid": "1688",
                                                  	"hashed": "1",
                                                  	"target_path": "C:\\Windows\\Prefetch\\IPCONFIG.EXE-62724FE6.pf",
                                                  	"pe_file": "NO",
                                                  	"eid": "0A253EC9-6996-11E8-BF2C-000D3A01FCC1",
                                                  	"time": "1528295396",
                                                  	"action": "WRITE",
                                                  	"utc_time": "Wed Jun  6 14:29:56 2018 UTC",
                                                  	"md5": "6507a711e37054b1ad1bec2d9daa6c28"
                                	}
              	}
     	]
}
```

###   Schema

List all the table schemas supported by server.

``` URL: https://<Base URL> /schema
Request Type: GET
 
Example Response

 
{
  "data": {
    "account_policy_data": "CREATE TABLE account_policy_data (uid BIGINT, creation_time DOUBLE, failed_login_count BIGINT, failed_login_timestamp DOUBLE, password_last_set_time DOUBLE)",
    "win_dns_events": "CREATE TABLE win_dns_events(event_type TEXT,eid TEXT, domain_name TEXT, pid TEXT, remote_address TEXT, remote_port BIGINT, time BIGINT, utc_time TEXT)",
    "win_dns_response_events": "CREATE TABLE win_dns_response_events( event_type TEXT,eid TEXT, domain_name TEXT,resolved_ip TEXT, pid BIGINT, remote_address TEXT, remote_port INTEGER , time BIGINT, utc_time TEXT  )",
    "win_epp_table": "CREATE TABLE win_epp_table(product_type TEXT, product_name TEXT,product_state TEXT, product_signatures TEXT)",
    "win_file_events": "CREATE TABLE win_file_events(action TEXT, eid TEXT,target_path TEXT, md5 TEXT ,hashed BIGINT,uid TEXT, time BIGINT,utc_time TEXT, pe_file TEXT , pid BIGINT)",
    "win_http_events": "CREATE TABLE win_http_events(event_type TEXT, eid TEXT, pid TEXT, url TEXT, remote_address TEXT, remote_port BIGINT, time BIGINT,utc_time TEXT)",
    "win_image_load_events": "CREATE TABLE win_image_load_events(eid TEXT, pid BIGINT,uid TEXT,  image_path TEXT, sign_info TEXT, trust_info TEXT, time BIGINT, utc_time      TEXT, num_of_certs BIGINT, cert_type         TEXT, version TEXT, pubkey TEXT, pubkey_length TEXT, pubkey_signhash_algo         TEXT, issuer_name TEXT, subject_name TEXT, serial_number TEXT, signature_algo     TEXT, subject_dn TEXT, issuer_dn TEXT)",
    "win_msr": "CREATE TABLE win_msr(turbo_disabled INTEGER , turbo_ratio_limt INTEGER ,platform_info INTEGER, perf_status INTEGER ,perf_ctl INTEGER,feature_control INTEGER, rapl_power_limit INTEGER ,rapl_energy_status INTEGER, rapl_power_units INTEGER )",
    "win_obfuscated_ps": "CREATE TABLE win_obfuscated_ps(script_id TEXT, time_created TEXT, obfuscated_state TEXT, obfuscated_score TEXT)",
    "win_pefile_events": "CREATE TABLE win_pefile_events(action TEXT, eid TEXT,target_path TEXT, md5 TEXT ,hashed BIGINT,uid TEXT, pid BIGINT, time BIGINT,utc_time TEXT )",
    "win_process_events": "CREATE TABLE win_process_events(action TEXT, eid TEXT,pid BIGINT, path TEXT ,cmdline TEXT,parent BIGINT, parent_path TEXT,owner_uid TEXT, time BIGINT, utc_time TEXT  )",
    "win_process_handles": "CREATE TABLE win_process_handles(pid BIGINT, handle_type TEXT, object_name TEXT, access_mask BIGINT)",
    "win_process_open_events": "CREATE TABLE win_process_open_events(action TEXT, eid TEXT,src_pid BIGINT,target_pid BIGINT, src_path TEXT ,target_path TEXT,owner_uid TEXT, time BIGINT, utc_time TEXT  )",
    "win_registry_events": "CREATE TABLE win_registry_events(action TEXT, eid TEXT,key_name TEXT, new_key_name TEXT,value_data TEXT, value_type TEXT, owner_uid TEXT, time BIGINT, utc_time TEXT)",
    "win_remote_thread_events": "CREATE TABLE win_remote_thread_events( eid TEXT,src_pid BIGINT,target_pid BIGINT, src_path TEXT ,target_path TEXT,owner_uid TEXT, time BIGINT, utc_time TEXT  )",
    "win_removable_media_events": "CREATE TABLE win_removable_media_events(removable_media_event_type TEXT, eid TEXT,uid TEXT,time BIGINT, utc_time TEXT,pid BIGINT)",
    "win_socket_events": "CREATE TABLE win_socket_events(event_type TEXT, eid TEXT, action TEXT, utc_time TEXT,time BIGINT, pid BIGINT, family TEXT, protocol INTEGER, local_address TEXT, remote_address TEXT, local_port INTEGER,remote_port INTEGER)",
    "win_yara_events": "CREATE TABLE win_yara_events(target_path TEXT, category TEXT, action TEXT, matches TEXT, count INTEGER, eid TEXT)",
    "windows_crashes": "CREATE TABLE windows_crashes (datetime TEXT, module TEXT, path TEXT, pid BIGINT, tid BIGINT, version TEXT, process_uptime BIGINT, stack_trace TEXT, exception_code TEXT, exception_message TEXT, exception_address TEXT, registers TEXT, command_line TEXT, current_directory TEXT, username TEXT, machine_name TEXT, major_version INTEGER, minor_version INTEGER, build_number INTEGER, type TEXT, crash_path TEXT)"
  },
  "message": "Successfully fetched the schema",
  "status": "success"
}
```

Schema
-------

List all the table schemas supported by server.

``` URL: https://<Base URL> /schema/<table_name>
Request Type: GET
 
Example Response

 
{
	"data": "CREATE TABLE win_file_events(action TEXT, eid TEXT,target_path TEXT, md5 TEXT ,hashed BIGINT,uid TEXT, time BIGINT,utc_time TEXT, pe_file TEXT , pid BIGINT)",
	"message": "Successfully received table schema",
	"status": "success"
}
```

Distributed (Ad-Hoc) Queries
----------------------------

### Add a Distributed Query

Sends a live, ad-hoc query to specified endpoint nodes.
``` 
URL: https://<Base URL> /distributed/add
Request Type: POST
 
Example Request
{
       "query": "select * from system_info;",
       "tags": [
        	"demo"
        ],
   	"nodes": [
            "6357CE4F-5C62-4F4C-B2D6-CAC567BD6113"
       ]
}
 
Response
 
{
    	"status": "success",
    	"message": "Successfully send the distributed query",
    	“query_id": "1"
}
```


### Get Results of a Distributed Query

Distributed query results are streamed over a websocket. You can use any
websocket client. Query results will expire, if not retrieved in 10 minutes.

`URL: wss://<IP_ADDRESS:PORT>/distributed/result`

Send the **query id** as a message after connect

The following picture shows an example of creating websockets using the ARC
([AdvancedRESTClient](https://install.advancedrestclient.com/#/install))

PIC MISSING - TO BE ADDED

List all queries
----------------
``` 
URL: https://<Base URL> /queries
Request Type: GET
 
Response

{
  "data": [
	{
  	"description": "",
  	"id": 103,
  	"interval": 3600,
  	"platform": "windows",
  	"query": "select * from win_removable_media_events;",
  	"removed": false,
  	"shard": 100,
  	"tags": [],
  	"value": "",
  	"version": ""
	}
  
  ],
  "message": "Successfully received the queries",
  "status": "success"
}
```

Query by id
-----------

```
URL: https://<Base URL> /queries/<query_id>
Request Type: GET
 
Response

{
	"data": {
		"description": "YARA scan result events",
		"id": 3,
		"interval": 5,
		"platform": "windows",
		"query": "select * from win_yara_events limit 10;",
		"removed": true,
		"shard": 100,
		"tags": [],
		"value": "scan results",
		"version": "2.9.0"
	},
	"message": "Successfully fetched the query",
	"status": "success"
}
```

Add a query
-----------
``` 
URL: https://<Base URL> /queries/add
Request Type: POST
 
Example Request
{
"name": "running_process_query",
 
"query": "select * from processes;",
"interval": 5,
"platform": "windows",
"version": "2.9.0",
"description": "Processes",
"value": "Processes"
,
"tags":["finance","sales"]
}

Response

{
        	"data": 104,
        	"message": "Successfully created the query",
        	"status": "success"
}
```

List all packs
--------------
``` 
URL: https://<Base URL> /packs
Request Type: GET
 
Response

{
  "data": [
    {
      "discovery": [],
      "id": 1,
      "name": "all-query-pack",
      "platform": null,
      "queries": {
        "win_dns_events": {
          "description": "Windows DNS Events",
          "id": 8,
          "interval": 60,
          "platform": "windows",
          "query": "select * from win_dns_events;",
          "removed": true,
          "shard": null,
          "tags": [],
          "value": "Dns events",
          "version": "2.9.0"
        }
      },
      "shard": null,
      "tags": [],
      "version": null
    }
  ],
  "status": "success",
  "message": "Successfully received the packs"
}

```

Pack by id
----------
``` 
URL: https://<Base URL> /packs/<pack_id>
Request Type: GET
 
Response

{
{
	"data": {
		"discovery": [],
		"id": 1,
		"name": "pack_1",
		"platform": null,
		"queries": {
			"win_yara_events": {
				"description": "YARA scan result events",
				"id": 4,
				"interval": 5,
				"platform": "windows",
				"query": "select * from win_yara_events;",
				"removed": true,
				"shard": 100,
				"tags": [],
				"value": "scan results",
				"version": "2.9.0"
			}
		},
		"shard": null,
		"tags": [],
		"version": null
	},
	"message": "Successfully fetched the pack",
	"status": "success"
}

```


Upload a pack
-------------
``` 
URL: https://<Base URL> /packs/add
Request Type: POST
 
Example Request
{
"name": "process_query_pack",
"queries": {
"win_file_events": {
"query": "select * from processes;",
"interval": 5,
"platform": "windows",
"version": "2.9.0",
"description": "Processes",
"value": "Processes"
}
},
"tags":["finance","sales"]
}

Response

{
        	"message": "Imported query pack process_query_pack",
        	"status": "success"
}

```

 Tags
-----

### Get all tags
``` 
URL: https://<Base URL> /tags
Request Type: GET
 
Example Response
{
        	"data": [
                    	"finance",
                    	"sales"
        	],
        	"message": "Successfully received the tags",
        	"status": "success"
}

```

### Add new tags

Add an array of tags.
``` 
URL: https://<Base URL> /tags/add
Request Type: POST
 
Example Request
{
"tags":["finance","sales"]
}
 
Response
{
        	"message": "Successfully added the tags",
        	"status": "success"
}
```

### Modify tags on a node

Add/remove tags on a node.
```
URL: https://<Base URL> /nodes/tag/edit
Request Type: POST
 
Example Request
{
"host_identifier":"77858CB1-6C24-584F-A28A-E054093C8924",
"add_tags":["finance","sales"],
"remove_tags":["demo"]
}
Response
{
        	"message": "Successfully modified the tag(s)",
        	"status": "success"
}
```

###  Modify tags on a query

Add/remove tags on a query.

``` 
URL: https://<Base URL> /queries/tag/edit
Request Type: POST
 
Example Request
{
"query_id":1,
"add_tags":["finance","sales"],
"remove_tags":["demo"]
}
Response
{
        	"message": "Successfully modified the tag(s)",
        	"status": "success"
}

```

### Modify tags on a pack

Add/remove tags on a pack.

``` 
URL: https://<Base URL> /packs/tag/edit
Request Type: POST
 
Example Request
{
"pack_id":1,
"add_tags":["finance","sales"],
"remove_tags":["demo"]
}
Response
{
        	"message": "Successfully modified the tag(s)",
        	"status": "success"
}

```

 Rules
======

### List all rules

List all the rules.

``` 
URL: https://<Base URL> /rules
Request Type: GET

Response
{
	"data": [
		{
			"alerters": [
				"email",
				"debug"
			],
			"conditions": {
				"condition": "AND",
				"rules": [
					{
						"field": "column",
						"id": "column",
						"input": "text",
						"operator": "column_contains",
						"type": "string",
						"value": [
							"domain_name",
							"89.com"
						]
					}
				],
				"valid": true
			},
			"description": "Adult websites test",
			"id": 1,
			"name": "Adult websites test",
			"status": "ACTIVE",
			"updated_at": "Tue, 31 Jul 2018 12:31:43 GMT"
		}
	],
	"message": "Successfully received the alert rules",
	"status": "success"
}

```

### Rule by id
``` 
URL: https://<Base URL> /rules/<rule_id>
Request Type: GET
 

Response
{
	"data": {
		"alerters": [
			"email",
			"debug"
		],
		"conditions": {
			"condition": "AND",
			"rules": [
				{
					"field": "column",
					"id": "column",
					"input": "text",
					"operator": "column_contains",
					"type": "string",
					"value": [
						"domain_name",
						"89.com"
					]
				}
			],
			"valid": true
		},
		"description": "Adult websites test",
		"id": 1,
		"name": "Adult websites test",
		"status": "ACTIVE",
		"updated_at": "Tue, 31 Jul 2018 12:31:43 GMT"
	},
	"message": "Successfully fetched the rule",
	"status": "success"
}

```

### Add a rule

``` 
URL: https://<Base URL> /rules/add
Request Type: POST
 


Example request:
{
  "alerters": [
    "email","splunk"
  ],
	"name":"Adult website test",
"description":"Rule for finding adult websites",
  "conditions": {
    "condition": "AND",
  
  "rules": [
    {
      "id": "column",
      "type": "string",
      "field": "column",
      "input": "text",
      "value": [
        "issuer_name",
        "Polylogyx.com(Test)"
      ],
      "operator": "column_contains"
    }
  ],
  "valid": true
}

}


Response

{
	"data": 2,
	"message": "Successfully configured the rule",
	"status": "success"
}

```

### Modify a rule

``` 
URL: https://<Base URL> /rules/<rule_id>
Request Type: POST

Example request:

{
  "alerters": [
    "email",
    "debug"
  ],
  "conditions": {
    "condition": "AND",
    "rules": [
      {
        "field": "column",
        "id": "column",
        "input": "text",
        "operator": "column_contains",
        "type": "string",
        "value": [
          "domain_names",
          "89.com"
        ]
      }
    ],
    "valid": true
  },
  "description": "Adult websites test",
  "name": "Adult websites test",
  "status": "ACTIVE"
}

Response:
{
	"data": {
		"alerters": [
			"email",
			"debug"
		],
		"conditions": {
			"condition": "AND",
			"rules": [
				{
					"field": "column",
					"id": "column",
					"input": "text",
					"operator": "column_contains",
					"type": "string",
					"value": [
						"domain_names",
						"89.com"
					]
				}
			],
			"valid": true
		},
		"description": "Adult websites test",
		"id": 1,
		"name": "Adult websites test",
		"status": "ACTIVE",
		"updated_at": "Wed, 01 Aug 2018 15:15:10 GMT"
	},
	"message": "Successfully modified the rule",
	"status": "success"
}

```

### Alerts

List all the alerts based on node, query_name, and rule_id.

``` 
URL: https://<Base URL> /alerts
Request Type: POST
 
Example Request:

{
	"host_identifier":"77858CB1-6C24-584F-A28A-E054093C8924",
	"query_name":"processes",
	"rule_id":3
}



Response
{
  "data": [
    {
      "created_at": "Tue, 31 Jul 2018 14:19:30 GMT",
      "id": 1,
      "message": {
        "cmdline": "/sbin/launchd",
        "cwd": "/",
        "egid": "0",
        "euid": "0",
        "gid": "0",
        "name": "launchd",
        "nice": "0",
        "on_disk": "1",
        "parent": "0",
        "path": "/sbin/launchd",
        "pgroup": "1",
        "pid": "1",
        "resident_size": "6078464",
        "root": "",
        "sgid": "0",
        "start_time": "0",
        "state": "R",
        "suid": "0",
        "system_time": "105116",
        "threads": "4",
        "total_size": "17092608",
        "uid": "0",
        "user_time": "10908",
        "wired_size": "0"
      },
      "node_id": 1,
      "query_name": "processes",
      "rule_id": 3,
      "sql": null
    }
  ],
  "message": "Successfully received the alerts",
  "status": "success"
}
```

### Configure email sender and recipients for alerts

```
URL: https://<Base URL> /email/configure
Request Type: POST
 
Example request:
{
  "emalRecipients": [
    "mehtamouli1k@gmail.com",
    "moulik1@polylogyx.com"
  ],
  "email": "mehtamoulik13@gmail.com",
  "smtpAddress": "smtp2.gmail.com",
  "password": "a",
  "smtpPort": 445
}

Response
{
	"data": {
		"email": "mehtamoulik13@gmail.com",
		"emailRecipients": "mehtamouli1k@gmail.com,moulik1@polylogyx.com",
		"emails": "mehtamoulik@gmail.com,moulik@polylogyx.com",
		"password": "YQ==\n",
		"smtpAddress": "smtp2.gmail.com",
		"smtpPort": 445
	},
	"message": "Successfully updated the details",
	"status": "success"
}
```

### Response Commands

List all the response commands.
```
URL: https://<Base URL> /response
Request Type: GET
 
Response

{
  "data": [
    {
      "action": null,
      "command": "{\"action\": \"delete\", \"actuator\": {\"endpoint\": \"polylogyx_vasp\"}, \"target\": {\"file\": {\"device\": {\"hostname\": \"win10x64-1703\"}, \"hashes\": {\"md5\": \"F4377762954BC0F8A9641CE303539E4D\"}, \"name\": \"C:\\\\\\\\Users\\\\\\\\Default\\\\\\\\Downloads\\\\\\\\hi.txt\"}}}",
      "command_id": "2c92808564cbab3d0164d135e21d0008",
      "id": 1,
      "message": "FILE_NOT_FOUND",
      "status": "failure"
    }
  ],
  "message": "Successfully received the response commands",
  "status": "success"
}
```

### Response Command by command_id
```
URL: https://<Base URL> /response/<command_id>
Request Type: GET
 
Response

{
	"data": {
		"action": null,
		"command": "{\"action\": \"delete\", \"actuator\": {\"endpoint\": \"polylogyx_vasp\"}, \"target\": {\"file\": {\"device\": {\"hostname\": \"win10x64-1703\"}, \"hashes\": {\"md5\": \"F4377762954BC0F8A9641CE303539E4D\"}, \"name\": \"C:\\\\\\\\Users\\\\\\\\Default\\\\\\\\Downloads\\\\\\\\hi.txt\"}}}",
		"command_id": "2c92808564cbab3d0164d135e21d0008",
		"id": 1,
		"message": "FILE_NOT_FOUND",
		"status": "failure"
	},
	"message": "Successfully received the command status",
	"status": "success"
}
```

### Response

Delete a File on Endpoint

Allows the SOC analyst to respond to a potential compromise by requesting the
PolyLogyx server to delete a specific file on an identified endpoint.

```
URL: https://<Base URL> /response/add
Request Type: POST
 
Example Request

{
  "action": "delete",
  "actuator": {
    "endpoint": "polylogyx_vasp"
  },
  "target": {
    "file": {
      "device": {
        "hostname": "poly-win10"
      },
      "hashes": {},
      "name": "C:\\Users\\Default\\Downloads\\malware.txt"
    }
  }
}

Response:
{
	"command_id": "2c92808564ebc9d20164f67fccdd000b",
	"message": "Successfully send the response command",
	"status": "success"
}
```

### Kill Process on Endpoint

Allows the SOC analyst to terminate a process (identified by a process ID-pid)
on a specific endpoint.
```
URL: https://<Base URL> /response/add
Request Type: POST
 
Example Request

{
 "action": "stop",
"target": {
 
"process": {
"name": "calc.exe",
"pid": 123,
"device": {
"hostname": "10.0.0.5"
}
}
},
"actuator": {
"endpoint": "polylogyx_vasp"
}
}


Response:
{
	"command_id": "2c92808564ebc9d20164f67fccdd000b",
	"message": "Successfully send the response command",
	"status": "success"
}

```

### Carves

List all the carve session. Host identifier is optional.

```
URL: https://<Base URL> /carves
Request Type: POST
 
Example Request:

{
	"host_identifier":"77858CB1-6C24-584F-A28A-E054093C8924"
	
}



Response

{
	"data": [
		{
			"archive": "2N1P2UNDY6cd0877fa-36e4-41ff-926a-ff2a22673dc3.tar",
			"block_count": 1,
			"carve_guid": "cd0877fa-36e4-41ff-926a-ff2a22673dc3",
			"carve_size": 5632,
			"created_at": "2018-07-24 07:50:05",
			"id": 10,
			"node_id": 1,
			"session_id": "2N1P2UNDY6"
		}
	],
	"message": "Successfully fetched the carves",
	"status": "success"
}

```
