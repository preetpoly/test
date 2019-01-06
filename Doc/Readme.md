The PolyLogyx Endpoint Visibility and Control Platform is built for cloud and
SOCs. It provides endpoint monitoring and visibility, threat detection and
incident response for Security operating Centres (SOCs).

Intended Audience 
------------------

This guide is designed for security analysts and Security Operations Center
(SOC) teams who manage and maintain enterprise security. To use this guide
effectively, you should be familiar with Microsoft Windows and Ubuntu operating
systems.

Features 
---------

At a high-level, the PolyLogyx platform provides these features.

### Monitoring and visibility

Allows you view and monitor endpoint-related information. You are always
informed of what is occurring at all times on all managed endpoints. It helps
you meet your compliance needs and get real-time insight into network activity.

In addition to standard osquery events, you can also get information about these
events:

-   File actions (create, delete, timestomp, modify, and PE)

-   Registry operations (create and modify)

-   Process activities (open, create, terminate, map, and open handles)

-   Network events (socket, DNS request and response, HTTP)

-   Removable media events

-   MSR values

-   EPP status (endpoint protection)

-   Image load (DLl, process, and drivers) events

-   Remote thread creation

-   YARA scan results

For more information on the type of information, see Using Recon
Data.

### Intrusion detection

To allow detection of malicious activities, PolyLogyx offers:

-   Alerting – You can set up rules to define alerts to stay updated on
    pertinent activities. After you set up a rule for an event, you receive an
    email when the event occurs allowing you to take immediate action.

-   Threat intelligence – You can connect with various threat intel sources,
    such as Virus Total.

-   Queries

    -   *Query packs* (such as the queries based on the MITRE attack framework)
        are provisioned during installation. These SQL-based *scheduled queries*
        run at regular intervals to fetch data stored on the endpoints. These
        queries serve as a starting point and can be configured to fetch data
        meaningful for your setup.

    -   *Live queries* that you can run to fetch specific information at a point
        in time. You can define the query and run it to see results.

### Response

To respond to a malicious activity, you can perform one or all of these
activities:

-   File deletion

-   Process kill

-   Network isolation
