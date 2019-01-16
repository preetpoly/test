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

These flags govern the

--config_tls_refresh=300




Default agent configuration
