The PolyLogyx Endpoint Visibility and Control Platform leverages osquery as the agent along side an extension built by PolyLogyx for real-time telemetry and response commands on Windows.

The platform comes with a default set of configuration needed for osquery initialization and functioning. 
This configuration is specified by following:

1. osquery.flags
2. Default configuration
3. Options


osquery.flags

The entire list of flags supported by osquery can be found at osquery flags. Some of the key values relevant for PolyLogyx platform are described below. These can be changed/modified to suit the deployment environment specific needs however altering these variables can significantly alter the performance of the endpoint agent. The variables are passed to the client agent during the <link>Client Provisioning</link> via the file osquery.flags 

--extensions_autoload=C:\programdata\osquery\extensions.load

This flag tells the osquery agent to load an extension as part of osquery initialization. The extensions.load contains the location to the PolyLogyx Extension file. It is strongly recommended not to change this flag.

--extensions_interval=10

--extensions_timeout=90

--extensions_require=plgx_win_extension

--allow_unsafe

These flags control the extension loading behavior of the osquery agent. It is strongly recommended not to change these.


--disable_watchdog=true

--watchdog_level=-1

PolyLogyx Extension is a real-time event monitor on the endpoint. The real time monitoring can be enormously voluminous and the query paths to the tables where those events are recorded could surpass the defaul performance constraints imposed by osquery on its child processes and threads. It is therefore recommended to turn off those constraints for better stability.

--events_max=20000

--events_expiry=3600

These flags govern the history of real time events recorded on the endpoint. By default, upto 20000 events are recorded and when the count is hit, all the events that are older than 3600 seconds are purged from the local database. Altering these values can cause performance impact on queries.

--config_tls_refresh=300

This flag governs the refresh interval of agent config. Any changes to the agent configuration (as defined below) will get picked by the agent after this interval.


Default agent configuration

As soon as an agent checks-in with the server, a default configuration is applied to the agent based on the operating system of the endpoint. The configuration contains the list of scheduled queries that are applied on the agent. For Windows operating system, PolyLogyx Extension is part of the agent and therefore the configuration carries additional filtering criteria to eliminate a great deal of 'white noise' from the real time telemetry. 
