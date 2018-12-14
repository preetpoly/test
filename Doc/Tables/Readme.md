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
| process_guid | TEXT    | Process Guid                            |

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
