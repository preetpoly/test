Getting Started
===============

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

For more information on the type of information, see [Using Recon
Data](#using-recon-data).

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

Architecture
------------

The following diagram depicts the product architecture and its ecosystem
components.
![product architecture](https://github.com/preetpoly/test/blob/master/Presentation2.png)


 Provisioning the PolyLogyx Server 
===================================

The PolyLogyx server is a headless server platform that is flexible and allows
you to effectively manage and control data. Here are the features the PolyLogyx
server offers:

-   Agent and policy management

-   Data forwarding and management

-   OpenC2-based out-of-band command and control

-   Incident response, including proactive threat hunting and remediation

This chapter includes these topics:

-   [Before you Begin](#before-you-begin)

-   [Installing the PolyLogyx Server](#installing-the-polylogyx-server)

-   [Uninstalling the Server](#uninstalling-the-server)

-   [Upgrading the Server](#upgrading-the-server)

-   [Troubleshooting Server Installation
    Issues](#troubleshooting-server-installation-issues)

<br>Before you Begin
--------------------

Before you begin installation of the PolyLogyx server, ensure you read the
following information.

-   Server installation is supported only on the Ubuntu platform.

-   Recommended configuration for the PoC (supports 10-25 clients)

    -   250 GB of free space

    -   Quad Core processor

    -   8 GB RAM

-   Contact PolyLogyx to procure the following:

    -   Server Docker image (plgx_docker.zip)

    -   Clean-up script (docker-cleanup.sh)

-   Docker and Docker Compose are required to install the PolyLogyx server.

| Component      | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Docker         | Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. To get started with Docker, review the information on their [website](https://docs.docker.com/install/overview/). Review instructions for [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce) or [Docker EE](https://docs.docker.com/install/linux/docker-ee/ubuntu/) to install Docker on Ubuntu. |
| Docker Compose | Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration. To install Docker Compose, complete the [prerequisites](https://docs.docker.com/compose/install/#prerequisites) and then perform [installation](https://docs.docker.com/compose/install/#install-compose).                                                                  |

Installing the PolyLogyx Server
-------------------------------

After you install Docker and Docker Compose, you can install the PolyLogyx
server.

1.  Clean-up existing Docker images and containers using the docker-cleanup.sh
    file.
    
    ```~/Downloads\$ sh ./docker-cleanup.sh```

**Note:** This will clean **all** the images and containers.

1.  Unzip the plgx_docker.zip file on the local server.
    ```(Md5: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx)
    ~/Downloads$ ls
    plgx_docker.zip
    ~/Downloads$ unzip plgx_docker.zip 
    Archive:  plgx_docker.zip
    inflating: plgx_docker/server.crt 
    <snip>
    inflating: plgx_docker/Doorman/doorman/plugins/alerters/debug.pyc  
    ~/Downloads$ ls
    plgx_docker  plgx_docker.zip```
1.  Switch to the folder where the installer is placed.

    ```~/Downloads\$ cd plgx_docker/```
1.  Enter the certificate-generate.sh script to generate certificates for
    osquery.  
    ```~/Downloads/plgx_docker$ sh ./certificate-generate.sh <IP address>
    Generating a 2048 bit RSA private key
    ................................................................................+++
    .........................+++
    writing new private key to 'Doorman/private.key``` 
            
In the syntax, \<IP address\> is the IP address of the system on which on to
host the PolyLogyx server. This will generate the certificate for osquery (used
for provisioning clients) and place the certificate in the plgx_docker folder.

1.  Modify and save the docker-compose.yaml file.

    1.  Edit the following configuration parameters in the file. In the syntax, replace the values in angle brackets with required values.
```
ENROLL_SECRET=<secret value>
DOORMAN_USER=<user login name>
DOORMAN_PASSWORD=<login password>
```  
             
1.  Ensure all the ports specified in the YAML file are open and accessible
2.  Save the file.
3.  Run the following command to start Docker compose.

    ```docker-compose up```
    
    Typically, this takes approximately 10-15 minutes. The following lines appear on
    the screen when Docker starts:
    ````~/Downloads/plgx_docker$ docker-compose up
    Starting plgx_docker_rabbit1_1  ... done
    Starting plgx_docker_postgres_1 ... done
    Starting plgx_docker_vasp_1     ... done
    Attaching to plgx_docker_rabbit1_1, plgx_docker_postgres_1, plgx_docker_vasp_1```
1.  Log on to server using following URL using the latest version of Chrome or
    Firefox browser.
    
    ```https://<ip address>:9000/manage```

In the syntax, `<IP address>` is the IP address of the system on which the
PolyLogyx server is hosted. This is the IP address you specified in step 4.

1.  Ignore the SSL warning, if any.

2.  Log on to the server using the credentials provided above at step 5a.

3.  Provision the clients. For more information, see [Provisioning the PolyLogyx
    Client for Endpoints](#provisioning-the-polylogyx-client-for-endpoints).

Uninstalling the Server 
------------------------

To uninstall the PolyLogyx server, run the following command to clean-up
existing Docker images and containers.

```~/Downloads\$ sh ./docker-cleanup.sh```

**Note:** This will clean **all** the images and containers.

Upgrading the Server
--------------------

Upgrading the PolyLogyx server is manual process. You must uninstall the
installed version and then install the latest version of the server. For more
information, see [Uninstalling the Server](#uninstalling-the-server) and
[Installing the PolyLogyx Server](#installing-the-polylogyx-server).

Troubleshooting Server Installation Issues
------------------------------------------

To be added

 Provisioning the PolyLogyx Client for Endpoints
================================================

The PolyLogyx client that is a part of the PolyLogyx endpoint platform,
leverages osquery, a multi-platform operating system monitoring and
instrumentation framework. Typically, deploying osquery and running it across
your fleet can be a daunting and complicated task. Using CPT makes it easy to
deploy and communicate with management server. Here are the features the
PolyLogyx client offers:

-   Compliance (PCI, HIPAA)

-   Digital forensics

-   Asset and inventory use cases

-   Vulnerability management

-   Host intrusion detection

-   Performance and operational troubleshooting

This chapter includes these topics:

-   [Installing the PolyLogyx Client](#installing-the-polylogyx-client)

-   [Uninstalling Client](#uninstalling-the-client)

-   [Upgrading the Client](#upgrading-the-client)

-   [Troubleshooting Client Installation
    Issues](#troubleshooting-client-installation-issues)

Installing the PolyLogyx Client 
--------------------------------

Installing the PolyLogyx client involves these steps:

1.  Complete the needed prerequisites. See [Before you
    Begin](#before-you-begin-1) for more information.

2.  Deploy the client. See [Deploying the PolyLogyx
    Client](#deploying-the-polylogyx-client) for more information.

3.  Verify if the installation was successful. See [Verifying Client
    Installation](#verifying-client-installation) for more information.

### Before you Begin

Before you begin installation, ensure you complete the following prerequisites.

-   Provision a PolyLogyx server using a docker image. For more information, see
    [Provisioning the PolyLogyx Server](#getting-started).

-   Ensure you have working knowledge of osquery. If not, please read about
    [osquery](https://osquery.io/).

-   Make sure the endpoints meet the following system requirements.

    -   Support 64-bit architecture

    -   Have Windows 7 or later operating system installed

    -   Do not have these installed:

        -   osquery

        -   older version of PolyLogyx client

    -   Do not have host-based firewalls or other security tools that might
        interfere with a remote installation

    -   Allow outbound TCP traffic on port 9000

-   Procure the needed software by downloading the following from the [PolyLogyx
    site](https://polylogyx.com/download).

    -   CPT utility

    -   Public key file

### Deploying the PolyLogyx Client

Use the PolyLogyx Client Provisioning Tool (CPT) utility to deploy the PolyLogyx
client on endpoints. After you identify endpoints on which to deploy the client,
push the CPT utility and public key file to the endpoints using any standard
mechanism, such as SCCM.

Initiate a remote session and run the installation command remotely (using
PSEXEC or WMIC command line utilities) with administrative privileges.

Here is the syntax to execute the installation command.

```plgx_cpt.exe -i <ip address> -h <hostname> -k <server's public key file> [-p <port number>] [-v <osquery version>] [-o <download directory>]```

Here is the syntax description.

| Parameter | Description                                                                                                                                                                                  |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| \-i or -h | Specify one of the following. This is a required parameter.                                                                                                                                  |
| \-k       | Indicates the full path to the server public key file. This is a required parameter.                                                                                                         |
| \-p       | Represents the server port. This is an optional parameter and defaults to 9000.                                                                                                              |
| \-v       | Represents the osquery version to be installed. Currently, only version 3.2.6 is supported. This is an optional parameter. If you do not specify a version, the latest version is installed. |
| \-o       | Indicates the location at which to download. The default value is c:\\plgx-temp\\. This is an optional parameter.                                                                            |

-   \-i represents the IP address of the PolyLogyx management server (x.x.x.x
    format).

-   \-h represents the fully qualified domain name to the management server in
    the format a.b.c. You don’t need to https.

Here is an example of a remote command execution using PSEXEC.

```psexec \\101.101.1.101 -u Administrator cmd /c dir C:\Users\Administrator\plgx_cpt.exe -i 11.111.111.11 -k c:\certificate.crt```

The installation begins and the CPT utility brings the required artefacts on the
endpoints. After installation is complete, the Osquery 3.2.4 and PolyLogyx
extensions are deployed and the osqueryd service starts. The following output is
displayed if the command is successful.

``` Downloading Files needed for install. Depending on your network connection, it might take some time. Please wait..
Files downloaded. Initiaiting install..
Osquery successfully installed.
Configuring client..Please wait..
Service stopped successfully
Osquery stopped
Client configuration..Ready to go in 5 seconds..
Service start pending...
Service started successfully.
=== PolyLogyx osquery client installed Successfully ===
```


### Verifying Client Installation 

After you deploy the PolyLogyx client, complete these steps to verify the
installation. When the PolyLogyx client is installed successfully, the following
processes start:

1.  osqueryd service

2.  vast service

3.  vastnw service

4.  Plgx_win_extension.exe process

Installation is not successful if any of these fail to start.

Follow these steps to check if the required processes are running.

1.  Open a command window with administrative privileges.

2.  Run the following command to check the state of software stack, including
    osquery, PolyLogyx Extension and associated services.

```plgx_cpt.exe –c```

The command output lists the current state of the osqueryd, vast, and vastnw
services.

```osqueryd service up and running

===================================================
||           Query Execution Output              ||
===================================================
name : plgx_win_extension
path : \\.\pipe\osquery.em.3694
sdk_version : 1.0.0
type : extension
uuid : 3694
version : 1.0.0

===================================================
||          Query Execution Finished             ||
===================================================

vast service up and running
vastnw service up and running  
```

1.  Review the output to verify if the required services are running.

2.  Check if the plgx_win_extension.exe process is running.

3.  Open Task Manager.

4.  Switch to the Details tab.

5.  Locate the entry for the plgx_win_extension.exe process.

6.  Verify that process status is set to Running.

    ![process](https://github.com/preetpoly/test/blob/master/process.png)

Uninstalling the Client
-----------------------

Follow these steps to uninstall the PolyLogyx client.

1.  Open a command window with administrative privileges.

2.  Close any open instances of the osqueryd, vast, and vastnw services.

3.  Run the uninstall command.

    Here is the syntax to execute the installation command.

```plgx_cpt.exe -u {\<d\> full-uninstall \| \<s\> shallow-uninstall}```

The -u parameter is used to uninstall the agent and cannot be combined with any
other options. With the –u option, you must use one of these options:

| Option | Description                                                                                                                           |
|--------|---------------------------------------------------------------------------------------------------------------------------------------|
| \-s    | Used with the –u parameter for shallow uninstall. This option only uninstalls the software and does not delete associated data files. |
| \-d    | Used with the –u parameter for deep uninstall. This option removes all traces of the agent, including data files.                     |

Here are command examples.

The following output is displayed if the `plgx_cpt.exe -u -d` command is successful. 
``` uninstall_osq, will remove everything osquery , db and all folders
Stopping services. Please wait for a few second. As they say, Patience is a Virtue
Services stopped successfully !!
Uninstalling Polylogxy osquery client successful. Removing all the services..
Removing files..
```   

The following output is displayed if the `plgx_cpt.exe -u -s` command is successful.
``` uninstall_osq_shallow, will only uninstall osquery but keep database untouched
Detected existing installation of osquery..
Uninstalling osquery successful
Trying to remove C:\ProgramData\osquery\plgx_win_extension.ext.exe
```                                                                                                                                                                                                                                                                              

Upgrading the Client 
---------------------

Upgrading the PolyLogyx client is manual process. You must uninstall the
installed version and then install the latest version of the client. For more
information, see [Uninstalling the Client](#uninstalling-the-client) and
[Installing the PolyLogyx Client](#installing-the-polylogyx-client).

Troubleshooting Client Installation Issues
------------------------------------------

This section examines the common errors and their resolution.

To resolve general osquery-related issues, review their [slack
channel](https://osquery-slack.herokuapp.com/).

### Incorrect server details

The following error mesaage is diaplayed when you run a command wih incoreect
server details, sch as IP address or host name.

``` Downloading Files needed for install. Depending on your network connection, it might take some time. Please wait..
Transfer failed for [c:\plgx-temp\osquery.flags] , Error Code: 7 (Couldn't connect to server)
Downloading files from Server failed
```

**Resolution**: To resolve this issue, execute the command with correct server
details.

### Insufficient privileges

The following error message is displayed when you run a command without
administrative privileges or sufficient arguments.

``` ##########################################
## Insufficient Privileges ###############
## Need Administrator privileges to run CPT#
##########################################
```

**Resolution**: To resolve this issue, execute the command with administrative
privileges and sufficient arguments.

### Incorrect certificate file name or path

If you execute the command to install the PolyLogyx client with an incorrect
certificate path, the following error is displayed.

``` Failed to open Server's public key file c:\Users\user1\certificate.crt , Error: 2
Failed to read server's public key from input file [c]
```
**Resolution**: To resolve this error, execute the command with the correct
path.

### Invalid certificate

If you execute the command to install the PolyLogyx client with administrative
privileges but with an invalid certificate, the following error is displayed.

``` Downloading Files needed for install. Depending on your network connection, it might take some time. Please wait..
Transfer failed for [c:\plgx-temp\osquery.msi] , Error Code: 60 (Peer certificate cannot be authenticated with given CA certificates)
Downloading files from Server failed
=== Failed to configure  ===
```

**Resolution**: To resolve this error, execute the command with a valid
certificate.

### Failed to configure Osquery

If you try to install the PolyLogyx client when osquery already installed, the
following error message is displayed.

``` Osquery is already installed, please uninstall before proceeding. Using plgx_cpt.exe -u <d/s> option```

**Resolution**: Follow these steps to resolve the issue:

1.  If osquery is already installed, use tool with -u option to uninstall
    osquery. For more information, see [Uninstalling the
    Client](#uninstalling-the-client).

2.  Execute the command to install the PolyLogyx client with administrative
    rights and a valid certificate. For more information, see [Deploying the
    PolyLogyx Client](#deploying-the-polylogyx-client).

 Using Recon Data
=================

As the name suggests, recon refers to preliminary research or analysis. After
the PolyLogyx client is provisioned and the connection to the PolyLogyx server
is established, seeded recon queries are run to pull relevant information for
each node. These out-of-box queries run every 30 minutes to fetch data from the
nodes and require no configuration.

Understanding reported data
---------------------------

For each configured node, these pre-populated queries fetch detailed data. The
fetched data provides insight and visibility on these aspects.

| Parameter                       | Description                                                                                                                                                                                                                                                                                           | Tag in response      | Available on          |
|---------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------|-----------------------|
| Application compatibility shims | Application compatibility shims enable you to fake aspects of the environment for a given application and allow fixing of issues that an application might encounter on a new version of Windows. Malwares leverage shim data bases (SDBs) as a persistence mechanism. Lists the SDBs on your system. | appcompat_shims      | Windows and Macintosh |
| Application hashes              | Lists the hash information for applications on your system.                                                                                                                                                                                                                                           | app_hashes           | Windows and Macintosh |
| Certificates                    | Digital certificates are used to establish the identity and trust level of software programs. Lists the various digital certificates installed on your system.                                                                                                                                        | certificates         | Windows and Macintosh |
| Chrome extensions               | Lists the Chrome extensions installed on your system.                                                                                                                                                                                                                                                 | chrome_extensions    | Windows and Macintosh |
| Drivers                         | Lists the various device drivers installed on your system.                                                                                                                                                                                                                                            | drivers              | Windows and Macintosh |
| etc hosts                       | The hosts file is used by an operating system to resolve a computer (or an internet domain) to an address. Lists the entries in the hosts file of your system.                                                                                                                                        | etc_hosts            | Windows and Macintosh |
| Internet Explorer extensions    | Lists the Internet Explorer extensions installed on your system.                                                                                                                                                                                                                                      | ie_extensions        | Windows and Macintosh |
| Kernel info                     | Lists the kernel details for your system.                                                                                                                                                                                                                                                             | kernel_info          | Windows and Macintosh |
| Kva speculative info            | Provides the presence (or absence) of mitigations for Spectre and Meltdown vulnerabilities.                                                                                                                                                                                                           | kva_speculative_info | Windows and Macintosh |
| Patches                         | Lists the updates or patches installed on your system.                                                                                                                                                                                                                                                | patches              | Windows and Macintosh |
| Scheduled tasks                 | scheduled_tasks (or cron jobs) are programs run by the task scheduler component of an operating system to run specified jobs at regular intervals. Malware can sometimes abuse this feature of an operating system. Provides the various scheduled tasks configured on your system.                   | scheduled_tasks      | Windows and Macintosh |
| Startup items                   | Lists applications are automatically started with the launch of your system. Malwares can install themselves as a start_up item on a system to gain persistence.                                                                                                                                      | startup_items        | Windows and Macintosh |
| Time                            | Provides the system time stamp in Unix and UTC formats. The timestamp represents the time at which the agent starts after installation or reboot.                                                                                                                                                     | time                 | Macintosh             |
| Windows epp table               | Lists the status of the Anti-Virus solutions on your system.                                                                                                                                                                                                                                          | win_epp_table        | Windows and Macintosh |
| Windows programs                | Lists the Windows programs installed or running on your system.                                                                                                                                                                                                                                       | win_programs         | Windows and Macintosh |
| Windows services                | A Windows service, or a daemon on Unix, is a program that operates in the background and is often loaded automatically on start-up. Lists the services running on your system.                                                                                                                        | win_services         | Windows and Macintosh |

Accessing data
--------------

To access or view the fetched data, use the getextendednode info API. Here is
the syntax to invoke the API.

```<server address:port\>/servces/api/v0/nodes/TBA```

Here is sample of the API response. Note that data is reported for each of the
parameters listed in this [table](#Table_list_recon).

```{
    "data": {
        "enrolled_on": "2018-09-11 20:36:01",
        "host_identifier": "A5B128CC-2A08-11B2-A85C-CFFDFBDACCA7",
        "last_checkin": "2018-09-18 20:02:06",
        "network_info": {
            "mac_address": "8c:16:45:a3:9f:61"
        },
        "node_info": {
            "computer_name": "janedoe-laptop",
            "cpu_physical_cores": "4",
            "hardware_model": "20KG0022US",
            "hardware_serial": "PF19A9DK",
            "hardware_vendor": "LENOVO",
            "physical_memory": "17026711552"
        },
        "node_key": "5c4a23e6-bfee-4445-ac98-5d2484d64fcb",
        "system_data": [
            {
                "data": [
                    {
                        "description": "Create and edit presentations ",
                        "identifier": "aapocclcgogkmnckokdopfmhonfmgoek",
                        "path": "C:\\Users\\JaneDoe\\AppData\\Local\\Google\\Chrome\\User Data\\Default\\Extensions\\aaposwadwrtgogkmnthdopfmhonfwemgoe\\0.10_0\\",
                        "version": "0.10"
                    }

                       ],
                "name": "chrome_extensions",
                "updated_at": "2018-09-18 20:01:35"
            },
            {
                "data": [
                    {
                        "name": "DEL_ST_CPL",
                        "path": "CMD",
                        "status": "enabled"
                    }

                ],
                "name": "startup_items",
                "updated_at": "2018-09-18 20:01:34"
            },
            {
                "data": [
                    {
                        "name": "Notepad++ (64-bit x64)",
                        "version": "7.5.8"
                    }
                ],
                "name": "win_programs",
                "updated_at": "2018-09-18 20:01:34"
            },
            {
                "data": [
                    {
                        "hidden": "1",
                        "last_run_message": "The operation completed successfully.",
                        "next_run_time": "1537302250",
                        "path": "\\GoogleUpdateTaskMachineCore"
                    }
                ],
                "name": "scheduled_tasks",
                "updated_at": "2018-09-18 20:01:34"
            },
            {
                "data": [
                    {
                        "module_path": "",
                        "path": "C:\\WINDOWS\\System32\\DriverStore\\FileRepository\\sgx_psw.inf_amd64_67efe445e1ece117\\aesm_service.exe",
                        "sha1": "f447c55f91a941d866ff5cb6227a64d2b0015b1e"
                    }

                ],
                "name": "win_services",
                "updated_at": "2018-09-18 20:01:34"
            },
            {
                "data": [
                    {
                        "bp_microcode_disabled": "0",
                        "bp_mitigations": "1",
                        "bp_system_pol_disabled": "",
                        "cpu_pred_cmd_supported": "1",
                        "cpu_spec_ctrl_supported": "1",
                        "ibrs_support_enabled": "1",
                        "kva_shadow_enabled": "1",
                        "kva_shadow_inv_pcid": "1",
                        "kva_shadow_pcid": "1",
                        "kva_shadow_user_global": "0",
                        "stibp_support_enabled": "1"
                    }
                ],
                "name": "kva_speculative_info",
                "updated_at": "2018-09-18 20:01:34"
            },
            {
                "data": [
                    {
                        "product_name": "Windows Defender Antivirus",
                        "product_signatures": "Up-to-date",
                        "product_state": "On",
                        "product_type": "Anti-Virus"

                    }
                ],
                "name": "win_epp_table",
                "updated_at": "2018-09-18 20:01:34"
            },
            {
                "data": [
                    {
                        "arguments": " NOEXECUTE=OPTIN  FVEBOOT=2125824  NOVGA",
                        "path": "C:\\WINDOWS\\System32\\ntoskrnl.exe",
                        "version": "10.0.17134.285"
                    }
                ],
                "name": "kernel_info",
                "updated_at": "2018-09-18 20:01:34"
            },
            {
                "data": [
                    {
                        "image": "C:\\WINDOWS\\system32\\drivers\\wudfrd.sys",
                        "provider": "Lenovo",
                        "sha1": "ccc9bd5f9e4d88ccac5881a62639bfde898502cc",
                        "signed": "1"
                    }
                ],
                "name": "drivers",
                "updated_at": "2018-09-18 20:01:34"
            },
            {
                "data": [],
                "name": "etc_hosts",
                "updated_at": "2018-09-18 20:01:34"
            },
            {
                "data": [],
                "name": "appcompat_shims",
                "updated_at": "2018-09-18 20:01:34"
            },
            {
                "data": [
                    {
                        "common_name": "Microsoft Root Certificate Authority",
                        "issuer": "com, microsoft, Microsoft Root Certificate Authority",
                        "not_valid_after": "1620602893",
                        "path": "CurrentUser\\Trusted Root Certification Authorities",
                        "self_signed": "1"
                    }
                ],
                "name": "certificates",
                "updated_at": "2018-09-18 20:01:35"
            },
            {
                "data": [
                    {
                        "timestamp": "Tue Sep 18 19:59:54 2018 UTC",
                        "unix_time": "1537300794"
                    }
                ],
                "name": "time",
                "updated_at": "2018-09-18 20:01:34"
            },
            {
                "data": [
                    {
                        "directory": "c:\\windows\\system32\\WindowsPowerShell\\v1.0",
                        "path": "c:\\windows\\system32\\WindowsPowerShell\\v1.0\\powershell.exe",
                        "sha1": "1b3b40fbc889fd4c645cc12c85d0805ac36ba254"
   	            }
                ],
                "name": "app_hashes",
                "updated_at": "2018-09-18 20:01:34"
            },
            {
                "data": [
                    {
                        "description": "Update",
                        "hotfix_id": "KB4230204",
                        "installed_on": "7/14/2018"
  		     }
              	      ],
                "name": "patches",
                "updated_at": "2018-09-18 20:01:35"
            },
            {
                "data": [
{
                        "name": "Microsoft Url Search Hook",
                        "path": "C:\\Windows\\System32\\ieframe.dll",
                        "sha1": "fde1cbd1e1009833285a479b6d88d69bcdd7f266",
                        "version": "11.0.17134.254"
                    }
                ],
                "name": "ie_extensions",
                "updated_at": "2018-09-18 20:01:35"
            }
        ],
        "tags": []
    },
    "message": "Successfully fetched the node info",
    "status": "success"
}
 ``` 

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
Here are a few examples of inlcude filters.
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

 To be added later 
===================

To do:

-   Recon - Default view – analytics

The duration for which the data is stored on the endpoint is configurable. Edit
the value for the \<xyz\> setting (in seconds) in the osquery.conf file to
indicate the duration for which to store the data at the endpoint. After the
specified time duration lapses, the data is moved from the endpoint to the
server and stored in the server database. The data stored on the server can be
accessed and queries using the REST APIs.

If deployment fails, examine logs placed at *c*:\\\\*plgx_cpt_log.txt* to
identify the issues.
