Configuring the PolyLogyx Client
================================================

After the PolyLogyx client is provisioned, the default and seeded configuration comes into play. If needed, you can customize the various configuration settings defined for the PolyLogyx client. 

To customize the configuration settings, you can modify the following:

1. osquery.flags file 
2. Predefined filters and queries
3. PolyLogyx configuration options

The osquery.flags File
--------------------------------
The osquery.flags file includes all the parameters needed for osquery initialization and functioning. By default, this file is stored in the <i>C:\programdata\osquery</i> folder. 

Although this file contains all the flags supported by osquery, in this section, we will discuss only the key flags that are relevant for the PolyLogyx platform. 

Update the parameters to configure the deployment environment to meet your specific needs. Note that modifying these values may significantly alter the performance of the endpoint agent. These configured values are passed to the endpoint agent during the [client provisioning](https://github.com/preetpoly/test/tree/pooja/Doc/Provisioning_Polylogyx_Client#provisioning-the-polylogyx-client-for-endpoints) through the osquery.flags file.


| Flag | Description                                                                                                                                                                                 |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| extensions_autoload=C:\programdata\osquery\extensions.load | Informs the osquery agent to load an extension during osquery initialization. The extensions.load contains the location to the PolyLogyx Extension file. We recommended that you DO NOT change this flag.                                                                                                                                  |
| extensions_interval=10 <br> extensions_timeout=90 <br> extensions_require=plgx_win_extension <br> allow_unsafe | Control the extension loading behavior of the osquery agent. We recommended that you DO NOT change this flag. |
| disable_watchdog=true <br> watchdog_level=-1 | PolyLogyx Extension is a real-time event monitor on the endpoint. Real time monitoring can be voluminous and query paths to the tables where those events are recorded could surpass the default performance constraints imposed by osquery on its child processes and threads. It is therefore recommended to turn off those constraints for better stability.|
| events_max=20000 <br> events_expiry=3600 | Manage the history of real time events recorded on the endpoint. By default, up to 20000 events are recorded and when the count is hit, all the events that are older than 3600 seconds are purged from the local database. Altering these values can cause performance impact on queries. | 
| config_tls_refresh=300 | Controls the refresh interval for agent configuration. Any changes to the agent configuration (as defined below) will get picked by the agent after this interval.|


Predefined filters and queries
---------------
As soon as an agent checks-in with the server, a default configuration is applied to the agent based on the operating system of the endpoint. The configuration contains the list of scheduled queries and filters that are applied on the agent. 
Perform these steps to view or edit this configuration:
1. Access the web interface for the server.
2. Navigate to View  > Configs. 
The configuration consists of the scheduled query intervals for various osquery tables. 

For Windows operating system, PolyLogyx Extension is part of the agent and therefore the configuration carries additional filtering criteria to eliminate 'white noise' from the real time telemetry and a set of scheduled queries that captures all the process creation and network connections data from the endpoint. The configurations are editable and the changes in the configuration gets picked up by the endpoint based on the config_tls_refresh value in the osquery.flags file. The edits can be done to add more queries, change the schedules or filters. 
 
For more information on filters, review the "Understanding Filters" section.

PolyLogyx configuration options
---------------------
The PolyLogyx configuration options are global in nature and are applied to all agents in addition to the agent configurations. 
Perform these steps to view or edit this configuration:
1. Access the web interface for the server.
2. Navigate to Management > Options. 
![poly_options](https://github.com/preetpoly/test/blob/pooja/poly_options.png)
Options in osquery allow for custom parameters that an extension can use. PolyLogyx Extension provides the feature of 'Live Response' and the options control the switch to enable/disable the response feature and its associate control flags.
WE NEED TO DETAIL ALL OPTIONS IN A TABLE 

