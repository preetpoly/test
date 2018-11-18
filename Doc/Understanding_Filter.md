Understanding Filters
======================

By default, the PolyLogyx client is designed to capture system events in
real-time for a variety of system activities. Also, it makes the telemetry
available through a flexible SQL syntax.

Any system, even when idle, generates a high volume of events. Streaming these
events from an endpoint to the server at regular intervals using scheduled
queries, despite compressing data, causes burden on the network and server
storage. A lot of the system activity is benign or irrelevant for incident
reporting and results in a large volume of data.

By default, the data captured for endpoints is based on multiple seeded filters
already defined in the osquery configuration file. These seeded filters reduce
the volume of data you need to review to search for incidents of interest. Also,
this reduces the burden on network and server resources. These filter are
derived from the high fidelity sysmon filters built by
[SwiftOnSecurity](https://github.com/SwiftOnSecurity/sysmon-config). For the
most part, the data captured by the seeded filters will meet your needs.
However, if needed, you can further tweak the data captured by defining
additional filters.

Filter Types
------------

Using filters, you can configure the PolyLogyx client to capture only data
relevant to you. You can choose to include relevant data and exclude
non-meaningful data. In effect you can define these type of filters:

-   Include filters to receive information about events matching the specified
    filtering criteria.

-   Exclude filters to ignore information about events matching the specified
    filtering criteria.

Before you define filters, review the following guidelines:

-   If you define an include filter for a table and column combination, all
    other data for that table and column combination is automatically excluded.
    Only the data matching the defined include filters is captured.

-   If you define an exclude filter for a table and column combination, all
    other data for that table and column combination is automatically captured.
    Only the data matching the defined exclude filters is ignored.

-   When the defined filters are processed:

    -   Exclude filters take precedence over include filters when rules
        conflict. So, if an include and exclude filter match the same event,
        information for the file is not captured.

    -   Include filters take precedence over exclude filters when rules do not
        conflict.

Adding Filters
--------------

Filters operate on the [tables](#polylogyx-tables) and are defined in the
osquery configuration file. Use the JSON syntax to define filters. Place all
filters within the plgx_event_filters tag in the osquery configuration file.
Here is the syntax used to define a filter.
``` 
“plgx_event_filters”: {
 	“table name1”: {
“column name1” : {
“filter type” : {
“values”:[
“value 1”,
“value 2”
 ]
 }
			  },
“column name2” : {
“filter type” : {
 “values”:[
“value 3”,
“value 4”
   ]
				        }
				   }
			},
“table name2”: {
“column name3” : {
“filter type” : {
“values”:[
“value 5”,
“value 6”
 ]
 }
			   },
“column name2” : {
“filter type” : {
 “values”:[
“value 7”,
“value 8”
   ]
				  	 }
				   }

			}
			}

```
 

In the syntax:

-   table name1 and table name2 - Represents the name of the table for which to
    define filters. You must include the table names in quotes (“”). You can
    apply filters only on a selected set of tables. For more information, see
    [Supported Tables](#tables-that-support-filters).

-   column name1, column name2, and so on - Indicates the name of the column
    within the table on which to filter information. You must include the column
    names in quotes (“”). You can define filters on selected columns in a set of
    tables. For more information, see [Supported
    Tables](#tables-that-support-filters).

-   filter type - Specifies the filter type. Possible values are include and
    exclude. You must include the values in quotes (“”).

-   value 1, value 2 and so on - List the values to match for the specified
    filter. Each entry represents a value that you want to store or ignore data
    for (based on the filter type). You must include the values in quotes (“”).
    Specified values are case insensitive. You can also use regular expressions
    in the values.

    -   \* – Represents one or more characters

    -   ? – Represents a single character

Examples
--------

Here are a few examples of exclude filters.
``` "win_process_events": {	
	"cmdline": {
		"exclude" : {
			"values": 
				[
				"C:\\Windows\\system32\\DllHost.exe /Processid*",
				"C:\\Windows\\system32\\SearchIndexer.exe /Embedding",
				"C:\\windows\\system32\\wermgr.exe -queuereporting",
				]
     }
    }
                                         }
```
Here are a few examples of include filters.
``` "win_registry_events": {
			"target_name": {
				"include": {
					"values": 
					[
					"*CurrentVersion\\Run*",
					"*Policies\\Explorer\\Run*",
					"*Group Policy\\Scripts*",
					"*Windows\\System\\Scripts*",
					]
					   }
					 }
			}
```

Tables that Support Filters
---------------------------

Event filtering is supported on following tables (and fields or columns).

| Table Name                                                  | Supported Columns |
|-------------------------------------------------------------|-------------------|
| [win_process_events](#win_process_events-table)             | cmdline, path, and parent_path           |
| [win_registry_events](#win_registry_events-table)           | target_name       |
| [win_socket_events](#win_socket_events-table)               | process_name, remote_port, and remote_address      |
| [win_file_events](#win_file_events-table)                   | target_path and process_name       |
| [win_remote_thread_events](#win_remote_thread_events-table) | src_path, target_path, and function_name          |
| [win_dns_events](#win_dns_events-table)                     | domain_name       |
| [win_dns_response_events](#win_dns_response_events-table)   | domain_name       |



Using REST APIs
===============

PolyLogyx REST API allows developers to use a programming language of their
choice to integrate with the headless PolyLogyx server. The REST APIs provide
the means to configure and query the data from the fleet manager. The APIs also
allow an openc2 orchestrator to get visibility into endpoint data and
activities, which can then be used to craft an openc2 action.

All payloads are exchanged over REST and use the JSON schema.

REST Based API
------------------

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

Fetching Node Details
-----------------

### Fetch Details for all Managed Nodes

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

### Fetch Detailed Information for all Managed Nodes

Fetch detailed information for all endpoint nodes managed by the PolyLogyx
server.

TO BE ADDED - GETDETAILEDNODEINFO

### Fetch Details for Specific Managed Node

Lists information and properties for a specific managed endpoint based on
host_identifier.

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

Managing Configs
-----------------

### Get Schema Info for all Tables 

List all the table schemas supported by the server.

``` 
URL: https://<Base URL> /schema
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

### Get Schema info for Specific Table from Server

List the table schemas for a specific table from the server.

```
URL: https://<Base URL> /schema/<table_name>
Request Type: GET
 
Example Response

 
{
	"data": "CREATE TABLE win_file_events(action TEXT, eid TEXT,target_path TEXT, md5 TEXT ,hashed BIGINT,uid TEXT, time BIGINT,utc_time TEXT, pe_file TEXT , pid BIGINT)",
	"message": "Successfully received table schema",
	"status": "success"
}


```
### Get All Available Configs from Server

Lists all available configs that can be applied to managed nodes.

```
URL: https://<Base URL>/configs
 
Request Type: GET
Response: List all the configs available. 
 
Example Response
 
{
      "status": "success",
      "message": "Successfully received the configs",
      "data": [
               	{
                          "updated_at": "2018-07-27 12:21:00",
                          "created_at": null,
                          "config": {
                                      "win_include_paths": {
                                            "user_folders":   [                                                                                                                                                                                                                                  
                                                 "C:\\Users\\*\\Downloads\\"
                                                              ]
                                       },
                                       "win_exclude_paths": {                                              	                                     "temp_folders":   [
                                                "C:\\Users\\*\\Downloads\\exclude\\"
                                                              ]
                                       },
                                        "schedule": {                                                                                     	                                     "win_file_events": {                                                                                                                  	                                          "query": "select * from win_file_events;",                                                                                                     	                                          "interval": 10,                                                                                                                	                                          "description": "win file events"
                                        }
                             }
               	},
                          "id": 2,
                          "name": "poly-config-new"
                           	},
                             	{
                           "updated_at": "2018-07-27 12:15:46",
                           "created_at": null,
                           "config": {
                                             "win_include_paths": {
                                                   "system_folders": [                                                                     	                                               "C:\\Windows\\System32\\drivers\\"
                                                                     ],
                                                   "system_files": [ 
                                                      "C:\\Windows\\System32\\drivers\\etc\\hosts"                                                                                                                                       
                                                                     ]                                                           	                                            "prog_files": [                                                                                            	                                               "C:\\Program Files\\",                                                                                                      	                                               "C:\\Program Files (x86)\\"
                                                                 	],                                                                              	                                            "user_folders": [                                                                                                  	                                               "C:\\Temp\\",                                                                                                	                                               "C:\\Users\\Default\\Downloads\\"
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
### Get Details for Specific Config from Server

Each configuration available at the server is identified by a config ID. Use
this API to fetch information for a specific config based on its ID.

```
URL: https://<Base URL>/configs/<config_id>
 
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

### Create a new Config on the Server

Create a config by mixing a combination of packs, queries and other supported
configurations.

```
URL: https://<Base URL>/configs/add
 
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
### Update a Config on the Server 

Use this API to modify information for a specific config based on its ID.

```
URL: https://<Base URL>/configs/<config_id>
 
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

Managing Node Configuration
-----------------

### Get Node Configuration 

Get the config for a node based on host_identifier.

```
URL: https://<Base URL>/nodes/config/<host_identifier>
 
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

### Assign a Config to a Node

Use this API to assign a specific config based on its ID to a node based on its
host_identifier. When you assign a config to a node, one or more scheduled
queries are assigned to the node.

```
URL: https://<Base URL>/ nodes/config/assign
 
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

### Remove a Config from a Node

Use this API to assign a remove assigned config from a node based on its
host_identifier. When you remove a config to a node, one or more scheduled
queries are removed from the node.

```
URL: https://<Base URL>/ nodes/config/remove
 
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
Managing Queries
-----------------

### Run a Live Query

Define and run a live query on one or more nodes.

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

### Define a Scheduled Query

Define and assign a scheduled query.

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

### List all Defined Queries

List all queries defined on the server. 

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

### List a Specific Query
List a specific query defined on the server based on its ID.

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
### List all Packs
Use this API to list all defined packs on the server. 

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

### List a Specific Pack
Get details for a specific query pack defined on the server based on its ID.
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
### Define a Pack
A group of scheduled queries is known as a pack. Use this API to define a new pack.
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
Managing Tags
-----------------

### Get all Tags

Get details for all tags defined on the server.

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

### Add Tags
Tags are a mechanism to logically group or associate elements such as nodes, packs, and so on. Add one or more tags.
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
### Modify Tags for a Node
Add or remove tags for a node.
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
### Modify Tags for a Query
Add or remove tags on a query.
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
### Modify Tags on a Pack
Add and remove tags on a pack.
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
Monitoring and Viewing Fleet Activity
-------------------------------------

### View Scheduled Query Results for a Node

Get all the data coming from a scheduled query for a specific endpoint node.
This query will retrieve all the results that match from the PolyLogyx server
database.

```URL: https://<Base URL >/nodes/schedule_query/results
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

### View Live Query Results 
Live query results are streamed over a websocket. You can use any websocket client. Query results will expire, if not retrieved in 10 minutes.

```
URL: wss://<IP_ADDRESS:PORT>/distributed/result
Send the query id as a message after connect
```
Managing Rules for Alerts
-------------------------------------

### List all the Rules

List all rules defined for alerts on the server.

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
### List a Specific Rule
List a specific rule defined on the server based on its ID.

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
### Add a Rule
Define a rule to for a new alert.

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

### Modify a Rule
Update an existing rule.

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

### List Alerts
List alerts based on node, query_name, and rule_id.

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
    "janedoe@abccomp.com",
    "charliedoe@xyzcomp.com"
  ],
  "email": "johndoe@xyzcomp.com",
  "smtpAddress": "smtp2.gmail.com",
  "password": "a",
  "smtpPort": 445
}

Response
{
	"data": {
		"email": "johndoe@xyzcomp.com",
		"emailRecipients": "janedoe@abccomp.com,charliedoe@xyzcomp.com",
		"emails": "jimdoe@abccorp.com",
		"password": "YQ==\n",
		"smtpAddress": "smtp2.gmail.com",
		"smtpPort": 445
	},
	"message": "Successfully updated the details",
	"status": "success"
}

```
Managing Responses
-----------------

### List all the Response Commands
Use this API to list all defined response commands. 
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

### List a Specific Response Command

Use this API to list a specific response command based on command_id.
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
### Delete a File

Allows the SOC analyst to respond to a potential compromise by requesting the
PolyLogyx server to delete a specific file on a node.
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
### Kill a Process

Allows the SOC analyst to terminate a process (identified by a process ID-pid) on a specific endpoint.
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

List all the carve sessions. Host identifier is optional.
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

Tables
======

The PolyLogyx Endpoint Platform leverages osquery that enables PolyLogyx to
expose the endpoint node as a relational database in the form of SQL tables.
These tables represent a breadth of system information, including running
processes, installed applications, hardware properties, and network connections.
As a result, you can run simple SQL queries to simple gain visibility for a
*point in time* view of the endpoint node.

PolyLogyx has extended and optimized osquery to support detection and response
use cases by adding new tables (see below). Where required, the tables are real
time (evented) in nature to ensure all activity is captured for analysis and
investigation.

For more information on the tables, see:

-   [PolyLogyx Tables](#polylogyx-tables)

-   [osquery Tables](#osquery-tables)

PolyLogyx Tables
----------------

Here are the PolyLogyx tables that allow real time activity monitoring
(evented).

-   [win_dns_events](#win_dns_events-table)

-   [win_dns_response_events](#win_dns_response_events-table)

-   [win_epp_table](#win_epp_table-table)

-   [win_file_events](#win_file_events-table)

-   [win_file_timestomp_events](#win_file_timestomp_events-table)

-   [win_http_events](#win_http_events-table)

-   [win_image_load_events](#win_image_load_events-table)

-   [win_msr](#win_msr-table)

-   [win_obfuscated_ps](#win_obfuscated_ps-table)

-   [win_pefile_events](#win_pefile_events-table)

-   [win_process_events](#win_process_events-table)

-   [win_process_handles](#win_process_handles-table)

-   [win_process_open_events](#win_process_open_events-table)

-   [win_registry_events](#win_registry_events-table)

-   [win_remote_thread_events](#win_remote_thread_events-table)

-   [win_removable_media_events](#win_removable_media_events-table)

-   [win_socket_events](#win_socket_events-table)

-   [win_yara_events](#win_yara_events-table)

### win_dns_events Table

Captures the DNS look up details.

| Column          | Type    | Description                                     |
|-----------------|---------|-------------------------------------------------|
| eid             | TEXT    | Unique event identifier                         |
| event_type      | TEXT    | DNS request                                     |
| domain_name     | TEXT    | Domain name to be looked up                     |
| pid             | INTEGER | Process identifier (usually svchost)            |
| remote_addresss | TEXT    | IP address to which the look up request is sent |
| remote_port     | INTEGER | Remote port                                     |
| time            | INTEGER | Unix time                                       |
| utc_time        | TEXT    | UTC time                                        |

### win_dns_response_events Table

Captures the DNS response events and can assist in mapping the DNS look up to
the source process based on IP address in the
[win_socket_events](#win_socket_events-table) table.

| Column          | Type    | Description                                              |
|-----------------|---------|----------------------------------------------------------|
| eid             | TEXT    | Unique event identifier                                  |
| event_type      | TEXT    | DNS response                                             |
| domain_name     | TEXT    | Domain name to be looked up                              |
| resolved_ip     | TEXT    | Resolved IP address                                      |
| pid             | INTEGER | Process identifier of the process making the DNS request |
| remote_addresss | TEXT    | IP address to which the look up request is sent          |
| remote_port     | INTEGER | Remote port                                              |
| time            | INTEGER | Unix time                                                |
| utc_time        | TEXT    | UTC time                                                 |

### win_epp_table Table

Stores name, type, state, and status of signatures of endpoint security products
deployed on the endpoint.

| Column             | Type | Description                       |
|--------------------|------|-----------------------------------|
| product_type       | TEXT | Endpoint product type             |
| product_name       | TEXT | Endpoint product name             |
| product_state      | TEXT | Endpoint product state (On/Off)   |
| product_signatures | TEXT | Endpoint product signatures state |

### win_file_events Table

Captures file creation, deletion, and modification events based on the
configuration.

| Column       | Type    | Description                             |
|--------------|---------|-----------------------------------------|
| action       | TEXT    | Create, delete, or write events         |
| eid          | TEXT    | Unique event identifier                 |
| target_path  | TEXT    | Complete file path                      |
| md5          | TEXT    | MD5 hash of file                        |
| hashed       | INTEGER | Hash available or not                   |
| uid          | TEXT    | User name                               |
| time         | INTEGER | Unix time                               |
| utc_time     | TEXT    | UTC time                                |
| pe_file      | TEXT    | Ture, if file is a executable (PE) file |
| pid          | INTEGER | Process identifier                      |
| process_name | TEXT    | Name of the process                     |

### win_file_timestomp_events Table

Stores information on Windows file timestomp events.

| Column        | Type    | Description                                |
|---------------|---------|--------------------------------------------|
| action        | TEXT    | Create, write or delete events             |
| eid           | TEXT    | Unique event identifier                    |
| pid           | INTEGER | Process identifier                         |
| target_path   | TEXT    | Full file path                             |
| process_name  | TEXT    | Process name                               |
| old_timestamp | TEXT    | Old file timestamp                         |
| new_timestamp | TEXT    | New file timestamp                         |
| md5           | TEXT    | MD5 hash of file contents, if available    |
| hashed        | INTEGER | Hash available or not                      |
| pe_file       | TEXT    | True, if file is an executable binary file |
| uid           | TEXT    | User name                                  |
| time          | INTEGER | Unix time                                  |
| utc_time      | TEXT    | UTC time                                   |

### win_http_events Table

Captures the http requests and targets details.

| Column         | Type    | Description                      |
|----------------|---------|----------------------------------|
| event_type     | TEXT    | HTTP event                       |
| eid            | TEXT    | Unique event identifier          |
| pid            | INTEGER | Windows process event identifier |
| process_name   | TEXT    | Name of the process              |
| url            | TEXT    | HTTP URL                         |
| remote_address | TEXT    | Remote address                   |
| remote_port    | INTEGER | Remote port                      |
| time           | INTEGER | Unix time                        |
| utc_time       | TEXT    | UTC time                         |

### win_image_load_events Table

Captures the load of binary executable files and their certificate information.

| Column               | Type    | Description                                      |
|----------------------|---------|--------------------------------------------------|
| eid                  | TEXT    | Unique event identifier                          |
| pid                  | INTEGER | Process identifier loading the image             |
| image_path           | TEXT    | Path of the image                                |
| uid                  | TEXT    | User name or identifier                          |
| time                 | INTEGER | Unix time                                        |
| utc_time             | TEXT    | UTC time                                         |
| sign_info            | TEXT    | File is signed or unsigned                       |
| trust_info           | TEXT    | File is trusted or untrusted                     |
| num_of_certs         | INTEGER | Number of certificates associated with the image |
| cert_type            | TEXT    | Catalog or embedded                              |
| version              | TEXT    | Version                                          |
| pubkey               | TEXT    | Public key of the certificate                    |
| pubkey_length        | TEXT    | Length of all the public keys (comma separated)  |
| pubkey_signhash_algo | TEXT    | Algorithm for public keys                        |
| issuer_name          | TEXT    | Issuer name                                      |
| subject_name         | TEXT    | Subject of the certificate                       |
| serial_number        | TEXT    | Serial number                                    |
| signature_algo       | TEXT    | Algorithm to sign the certificate                |
| subject_dn           | TEXT    | Subject domain                                   |
| issuer_dn            | TEXT    | Issuer domain                                    |

### win_msr Table

Stores
[details](https://www.intel.com/content/dam/www/public/us/en/documents/manuals/64-ia-32-architectures-software-developer-vol-3b-part-2-manual.pdf)
on MSR, their properties and values.

| Column             | Type    | Description                                        |
|--------------------|---------|----------------------------------------------------|
| turbo_disabled     | INTEGE  | CPU properties from MSR (MSR_IA32_MISC_ENABLE)     |
| turbo_ratio_limit  | INTEGER | CPU properties from MSR (MSR_TURBO_RATIO_LIMIT)    |
| platform_info      | INTEGER | CPU properties from MSR (MSR_PLATFORM_INFO)        |
| perf_status        | INTEGER | CPU properties from MSR (MSR_IA32_PERF_STATUS)     |
| perf_ctl           | INTEGER | CPU properties from MSR (MSR_IA32_PERF_CTL)        |
| feature_control    | INTEGER | CPU properties from MSR (MSR_IA32_FEATURE_CONTROL) |
| rapl_power_limit   | INTEGER | CPU properties from MSR (MSR_PKG_POWER_LIMIT)      |
| rapl_energy_status | INTEGER | CPU properties from MSR (MSR_PKG_ENERGY_STATUS)    |
| rapl_power_units   | INTEGER | CPU properties from MSR (MSR_RAPL_POWER_UNIT)      |

### win_obfuscated_ps Table

Powershell based attacks can be *fileless*. Many of these attacks obfuscate
powershell scripts and allow them evade detection. These scripts get logged in
Windows Event Viewer. PolyLogyx uses intelligence and statistics to analyze the
script logs to determine their obfuscation.

| Column           | Type | Description                                            |
|------------------|------|--------------------------------------------------------|
| script_id        | TEXT | Script identifier as recorded in the Windows event log |
| time_created     | TEXT | Time of creation                                       |
| obfuscated_state | TEXT | Obfuscated or Un-obfuscated                            |
| obfuscated_score | TEXT | Numerical value to measure the degree of obfuscation   |

### win_pefile_events Table

Captures the creation, deletion, modification of executable files.

| Column       | Type    | Description                     |
|--------------|---------|---------------------------------|
| action       | TEXT    | Create, delete, or modify event |
| eid          | TEXT    | Unique event identifier         |
| target_path  | TEXT    | Complete file path              |
| md5          | INTEGER | MD5 hash                        |
| hashed       | INTEGER | Hash available or not           |
| uid          | TEXT    | User identifier                 |
| time         | INTEGER | Unix time                       |
| utc_time     | TEXT    | UTC time                        |
| pid          | INTEGER | Process identifier              |
| process_name | TEXT    | Name of the process             |

### win_process_events Table

Captures the creation and termination of processes.

| Column      | Type    | Description                              |
|-------------|---------|------------------------------------------|
| action      | TEXT    | Created or terminated                    |
| eid         | TEXT    | Unique event identifier                  |
| pid         | INTEGER | Process identifier                       |
| path        | TEXT    | Path of the process executable           |
| cmdline     | TEXT    | Command line at process start            |
| parent      | INTEGER | Process identifier of the parent process |
| parent_path | TEXT    | Path of parent process executable        |
| owner_uid   | TEXT    | Process owners’ user identifier          |
| time        | INTEGER | Unix time                                |
| utc_time    | TEXT    | UTC time                                 |

### win_process_handles Table

Stores the open handles for processed across the system (including the system
privileged processes).

| Column      | Type    | Description                         |
|-------------|---------|-------------------------------------|
| Pid         | INTEGER | Process identifier                  |
| handle_type | TEXT    | Type of handle                      |
| object_name | TEXT    | Name of the object                  |
| access_mask | INTEGER | Access mask at the time of creation |

### win_process_open_events Table

Captures the events when a process opens another process with the intent to read
or write into the remote process’ virtual memory (VM_READ\|VM_WRITE).

| Column         | Type    | Description                            |
|----------------|---------|----------------------------------------|
| action         | TEXT    | Process open event                     |
| eid            | TEXT    | Unique event identifier                |
| src_pid        | INTEGER | Process identifier of source process   |
| target_pid     | INTEGER | Process identifier of target process   |
| src_path       | TEXT    | Full path to source process executable |
| target_path    | TEXT    | Full path to target process executable |
| granted_access | TEXT    | Granted access                         |
| owner_uid      | TEXT    | Source process owner’s user identifier |
| time           | INTEGER | Unix time                              |
| utc_time       | TEXT    | UTC time                               |

### win_registry_events Table

Captures the creation, deletion, and modification of registry keys and values.

| Column          | Type    | Description                                                        |
|-----------------|---------|--------------------------------------------------------------------|
| action          | TEXT    | Type of action performed on registry (create, read, write, or set) |
| eid             | TEXT    | Unique event identifier                                            |
| pid             | INTEGER | Process identifier of the originating process                      |
| process_name    | TEXT    | Name of the process                                                |
| target_name     | TEXT    | Name of the registry key being changed                             |
| target_new_name | TEXT    | New name of registry key if it is renamed                          |
| value_type      | TEXT    | Registry type being added                                          |
| value_data      | TEXT    | Registry value being added                                         |
| owner_uid       | TEXT    | User name                                                          |
| time            | BIGINT  | Unix time                                                          |
| utc_time        | TEXT    | UTC time                                                           |

### win_remote_thread_events Table

Captures the remote thread creations.

| Column      | Type    | Description                            |
|-------------|---------|----------------------------------------|
| eid         | TEXT    | Unique event identifier                |
| src_pid     | INTEGER | Process identifier of source process   |
| target_pid  | INTEGER | Process identifier of target process   |
| src_path    | TEXT    | Full path to source process executable |
| target_path | TEXT    | Full path to target process executable |
| owner_uid   | TEXT    | Source process owner’s user identifier |
| time        | INTEGER | Unix time                              |
| utc_time    | TEXT    | UTC time                               |

### win_removable_media_events Table

Captures the insertion of a removable media.

| Column                     | Type    | Description                               |
|----------------------------|---------|-------------------------------------------|
| eid                        | TEXT    | Unique event identifier                   |
| removable_media_event_type | TEXT    | Media event                               |
| uid                        | TEXT    | User identifier associated with the event |
| pid                        | INTEGER | Process identifier                        |
| time                       | INTEGER | Unix time                                 |
| utc_time                   | TEXT    | UTC time                                  |

### win_socket_events Table

Captures socket operations, such as accept, listen, and close.

| Column         | Type    | Description                                  |
|----------------|---------|----------------------------------------------|
| eid            | TEXT    | Unique event identifier                      |
| event_type     | TEXT    | Socket connection event                      |
| action         | TEXT    | Socket operation (accept, connect, or close) |
| time           | INTEGER | Unix time                                    |
| utc_time       | TEXT    | UTC time                                     |
| pid            | INTEGER | Process identifier                           |
| process_name   | TEXT    | Process name                                 |
| family         | TEXT    | Socket address family (e.g. AF_NET)          |
| protocol       | INTEGER | Transport protocol identifier                |
| local_address  | TEXT    | Local IP address (source)                    |
| remote_address | TEXT    | Remote IP address (destination)              |
| local_port     | INTEGER | Local port number for connection             |
| remote_port    | INTEGER | Remote port number                           |

### win_yara_events Table

Captures the yara matches based on input rules.

| Column      | Type    | Description                       |
|-------------|---------|-----------------------------------|
| eid         | TEXT    | Unique event identifier           |
| target_path | TEXT    | Full path to be scanned           |
| category    | TEXT    | YARA Rule category                |
| action      | INTEGER | File action (created or modified) |
| matches     | TEXT    | List of rules that matched        |
| count       | INTEGER | Number of rules that matched      |

osquery Tables
--------------

This release of the PolyLogyx Endpoint Platform embeds osquery v3.2.6. PolyLogyx
will make a concerted effort to ensure that we leverage the latest, stable
version of osquery. For more information on base osquery tables and schema,
refer to <https://osquery.io/schema/>*.*
