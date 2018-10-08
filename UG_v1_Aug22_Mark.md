![](media/e16309b5e01c825d98f9f4c2d0e38bc6.png)

*User’s Guide*

Provisioning the PolyLogyx Server 
==================================

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

-   Download the following from [PolyLogyx
    site](https://polylogyx.com/download):

    -   Server Docker image (plgx_docker.zip)

    -   Clean-up script (docker-cleanup.sh)

-   Docker and Docker Compose are required to install the PolyLogyx server.

| Docker         | Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. To get started with Docker, review the information on their [website](https://docs.docker.com/install/overview/). Review instructions for [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce) or [Docker EE](https://docs.docker.com/install/linux/docker-ee/ubuntu/) to install Docker on Ubuntu. |
|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Docker Compose | Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration. To install Docker Compose, complete the [prerequisites](https://docs.docker.com/compose/install/#prerequisites) and then perform [installation](https://docs.docker.com/compose/install/#install-compose).                                                                  |

Installing the PolyLogyx Server
-------------------------------

After you install Docker and Docker Compose, you can install the PolyLogyx
server.

1.  Clean-up existing Docker images and containers using the docker-cleanup.sh
    file.

\~/Downloads\$ sh ./docker-cleanup.sh

**Note:** This will clean **all** the images and containers.

1.  Unzip the plgx_docker.zip file on the local server.

(Md5: 63f1127b5dd28b3314a397fd1482974e)

\~/Downloads\$ ls

plgx_docker.zip

\~/Downloads\$ unzip plgx_docker.zip

Archive: plgx_docker.zip

inflating: plgx_docker/server.crt

\<snip\>

inflating: plgx_docker/Doorman/doorman/plugins/alerters/debug.pyc

\~/Downloads\$ ls

plgx_docker plgx_docker.zip

1.  Switch to the folder where the installer is placed.

\~/Downloads\$ cd plgx_docker/

1.  Enter the certificate-generate.sh script to generate certificates for
    osquery.

\~/Downloads/plgx_docker\$ sh ./certificate-generate.sh \<IP address\>

Generating a 2048 bit RSA private key

................................................................................+++

.........................+++

writing new private key to 'Doorman/private.key'

>   In the syntax, \<IP address\> is the IP address of the system on which on to
>   host the PolyLogyx server. This will generate the certificate for osquery
>   (used for provisioning clients) and place the certificate in the plgx_docker
>   folder.

1.  Modify and save the docker-compose.yaml file.

    1.  Edit the following configuration parameters in the file.

>   ENROLL_SECRET=\<secret value\>

>   DOORMAN_USER=\<user login name\>

>   DOORMAN_PASSWORD=\<login password\>

>   In the syntax, replace the values in angle brackets with required values.

1.  Ensure all the ports specified in the YAML file are open and accessible

2.  Save the file.

3.  Run the following command to start Docker compose.

docker-compose up

>   Typically, this takes approximately 10-15 minutes. The following lines
>   appear on the screen when Docker starts:

\~/Downloads/plgx_docker\$ docker-compose up

Starting plgx_docker_rabbit1_1 ... done

Starting plgx_docker_postgres_1 ... done

Starting plgx_docker_vasp_1 ... done

Attaching to plgx_docker_rabbit1_1, plgx_docker_postgres_1, plgx_docker_vasp_1

1.  Log on to server using following URL using the latest version of Chrome or
    Firefox browser.

https://\<ip address\>:9000/manage

>   In the syntax, \<IP address\> is the IP address of the system on which the
>   PolyLogyx server is hosted. This is the IP address you specified in step 4.

1.  Ignore the SSL warning, if any.

2.  Log on to the server using the credentials provided above at step 5a.

3.  Provision the clients. For more information, see [Provisioning the PolyLogyx
    Client for Endpoints](#provisioning-the-polylogyx-client-for-endpoints).

Uninstalling the Server 
------------------------

To uninstall the PolyLogyx server, run the following command to clean-up
existing Docker images and containers.

\~/Downloads\$ sh ./docker-cleanup.sh

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
===============================================

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

-   Host intrusion prevention

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
    [Provisioning the PolyLogyx Server](#provisioning-the-polylogyx-server).

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

plgx_cpt.exe -i \<ip address\> -k \<server's public key file\> [-p \<port
number\>] [-v \<osquery version\>] [-o \<download directory\>]

Here is the syntax description.

| *Parameter* | *Description*                                                                                                                                                                                |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| \-i         | Represents the IP address of the PolyLogyx management server (x.x.x.x format). This is a required parameter.                                                                                 |
| \-k         | Indicates the full path to the server public key file. This is a required parameter.                                                                                                         |
| \-p         | Represents the server port. This is an optional parameter and defaults to 9000.                                                                                                              |
| \-v         | Represents the osquery version to be installed. Currently, only version 3.2.6 is supported. This is an optional parameter. If you do not specify a version, the latest version is installed. |
| \-o         | Indicates the location at which to download. The default value is c:\\plgx-temp\\. This is an optional parameter.                                                                            |

Here is an example of a remote command execution using PSEXEC.

psexec \\\\101.101.1.101 -u Administrator cmd /c dir
C:\\Users\\Administrator\\plgx_cpt.exe -i 11.111.111.11 -k c:\\certificate.crt

The installation begins and the CPT utility brings the required artefacts on the
endpoints. After installation is complete, the Osquery 3.2.4 and PolyLogyx
extensions are deployed and the osqueryd service starts. The following output is
displayed if the command is successful.

[+] Server's public key input file: [c:\\certificate.crt]

[+] Output Directory [c:\\plgx-temp\\] already exists.

[+] File Size for [c:\\plgx-temp\\osquery.msi] is : 9396224.000000 bytes

[+] Transfer succeeded for [c:\\plgx-temp\\osquery.msi]

[+] File Size for [c:\\plgx-temp\\osquery.flags] is : 1114.000000 bytes

[+] Transfer succeeded for [c:\\plgx-temp\\osquery.flags]

[+] File Size for [c:\\plgx-temp\\plgx_win_extension.ext.exe] is :
9570888.000000 bytes

[+] Transfer succeeded for [c:\\plgx-temp\\plgx_win_extension.ext.exe]

[+] File Size for [c:\\plgx-temp\\secret.txt] is : 7.000000 bytes

[+] Transfer succeeded for [c:\\plgx-temp\\secret.txt]

[+] File Size for [c:\\plgx-temp\\certificate.crt] is : 1208.000000 bytes

[+] Transfer succeeded for [c:\\plgx-temp\\certificate.crt]

[+] File Size for [c:\\plgx-temp\\extensions.load] is : 51.000000 bytes

[+] Transfer succeeded for [c:\\plgx-temp\\extensions.load]

[+] Osquery successfully installed.

Service stopped successfully

osquery.flags replaced.

[+] Extension plgx_win_extension.ext.exe copied to osquery folder.

[+] Server cert copied to osquery folder.

[+] Copied server cert certificate.crt to osquery folder.

[+] secret file secret.txt copied to osquery folder.

[+] Success writing to SYSTEM\\CurrentControlSet\\Services\\osqueryd Registry.

Service start pending...

Service started successfully.

[+] Cleanup of all the downloaded files from [c:\\plgx-temp\\] directory.

[+] Extension plgx_win_extension.ext.exe deleted from downloaded directory.

[+] Server cert deleted from downloaded directory.

[+] Deleted server cert certificate.crt from downloaded directory.

[+] Secret file secret.txt deleted from downloaded directory.

[+] msi file c:\\plgx-temp\\osquery.msi deleted from downloaded directory.

[+] osquery flags file osquery.flags deleted from downloaded directory.

[+] === PolyLogyx osquery agent installed Successfully ===

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

>   plgx_cpt.exe –c

The command output lists the current state of the osqueryd, vast, and vastnw
services.

>   osqueryd service up and running

>   ===================================================

>   \|\| Query Execution Output \|\|

>   ===================================================

>   name : plgx_win_extension

>   path : \\\\.\\pipe\\osquery.em.3694

>   sdk_version : 1.0.0

>   type : extension

>   uuid : 3694

>   version : 1.0.0

>   ===================================================

>   \|\| Query Execution Finished \|\|

>   ===================================================

>   vast service up and running

>   vastnw service up and running

1.  Review the output to verify if the required services are running.

2.  Check if the plgx_win_extension.exe process is running.

3.  Open Task Manager.

4.  Switch to the Details tab.

5.  Locate the entry for the plgx_win_extension.exe process.

6.  Verify that process status is set to Running.

    ![](media/5bbc10122c54c1c8057268c6fb85f0fa.png)

Uninstalling the Client
-----------------------

Follow these steps to uninstall the PolyLogyx client.

1.  Open a command window with administrative privileges.

2.  Close any open instances of the osqueryd, vast, and vastnw services.

3.  Run the uninstall command.

    Here is the syntax to execute the installation command.

>   plgx_cpt.exe -u {\<d\> full-uninstall \| \<s\> shallow-uninstall}

>   The -u parameter is used to uninstall the agent and cannot be combined with
>   any other options. With the –u option, you must use one of these options:

| \-s | Used with the –u parameter for shallow uninstall. This option only uninstalls the software and does not delete associated data files. |
|-----|---------------------------------------------------------------------------------------------------------------------------------------|
| \-d | Used with the –u parameter for deep uninstall. This option removes all traces of the agent, including data files.                     |

Here are command examples.

| plgx_cpt.exe -u -d | The following output is displayed if the command is successful. [+] Stopping services. Please wait, as Patience is a Virtue Service is already stopped. [+] Uninstalling osquery successful Service stopped successfully Service stopped successfully [+] Removing ACL restrictions from plgx_win_extension folder [+] Deleting C:\\ProgramData\\plgx_win_extension [+] Removing ACL restrictions from osquery folder [+] Deleting C:\\ProgramData\\osquery                     |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| plgx_cpt.exe -u -s | The following output is displayed if the command is successful. [+] Detected existing installation of osquery.. Service is already stopped. [+] Uninstalling osquery successful [+] Removing all binaries dropped by plgx extension from [C:\\ProgramData\\osquery] directory. Removing ACL restrictions from C:\\ProgramData\\osquery Trying to remove C:\\ProgramData\\osquery\\plgx_win_extension.ext.exe [+] Trying to remove C:\\ProgramData\\osquery\\plgx-web-client.dll |

Upgrading the Client 
---------------------

Upgrading the PolyLogyx client is manual process. You must uninstall the
installed version and then install the latest version of the client. For more
information, see [Uninstalling the Client](#uninstalling-the-client) and
[Installing the PolyLogyx Client](#installing-the-polylogyx-client).

Troubleshooting Client Installation Issues
------------------------------------------

If deployment fails, examine logs placed at *c*:\\\\*plgx_cpt_log.txt* to
identify the issues. This section examines the common errors and their
resolution.

To resolve general osquery-related issues, review their [slack
channel](https://osquery-slack.herokuapp.com/).

### Insufficient privileges

The following error message is displayed when you run a command without
administrative privileges or sufficient arguments.

\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#

\#\# Insufficient Privileges \#\#\#\#\#\#\#\#\#\#\#\#\#\#\#

\#\# Need Administrator privileges to run CPT\#

\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#\#

**Resolution**: To resolve this issue, execute the command with administrative
privileges and sufficient arguments.

### Incorrect certificate file name or path

If you execute the command to install the PolyLogyx client with an incorrect
certificate path, the following error is displayed.

[+] Server's public key input file: [c:\\certs\\certificate.cr]

[-] Failed to open Server's public key file c:\\certs\\certificate.cr , Error: 2

[+] Failed to read server's public key from input file [c]

**Resolution**: To resolve this error, execute the command with the correct
path.

### Invalid certificate

If you execute the command to install the PolyLogyx client with administrative
privileges but with an invalid certificate, the following error is displayed.

[+] Server's public key input file: [c:\\certs\\server.crt]

[+] Output Directory [c:\\plgx-temp\\] already exists.

[-] Transfer failed for [c:\\plgx-temp\\osquery.msi] , Error Code: 60
(?????????????????????????????????s)

[-] Downloading files from Server failed , Error Code: 60
(?????????????????????????????????s)

[+] Cleanup of all the downloaded files from [c:\\plgx-temp\\] directory.

[-] Failed to delete extension plgx_win_extension.ext.exe from downloaded
directory. Error: 2

[+] Failed to delete server cert certificate.crt from downloaded directory.
Error: 2

[-] Failed to delete server cert certificate.crt deleted from downloaded
directory. Error: 2

[-] Failed to delete secret file secret.txt from downloaded directory. Error: 2

[+] msi file c:\\plgx-temp\\osquery.msi deleted from downloaded directory.

[-] Failed to delete osquery flags file osquery.flags from downloaded directory.
Error: 2

[-] === Failed to configure Osquery ===

**Resolution**: To resolve this error, execute the command with a valid
certificate.

### Failed to configure Osquery

If you try to install the PolyLogyx client when osquery already installed, the
following error message is displayed.

[+] Server's public key input file: [c:\\certs\\certificate.crt]

[-] Osquery is already installed, please uninstall before proceeding. Using
plgx_cpt.exe -u \<d/s\> option

**Resolution**: Follow these steps to resolve the issue:

1.  If osquery is already installed, use tool with -u option to uninstall
    osquery. For more information, see [Uninstalling the
    Client](#uninstalling-the-client).

2.  Execute the command to install the PolyLogyx client with administrative
    rights and a valid certificate. For more information, see [Deploying the
    PolyLogyx Client](#deploying-the-polylogyx-client).
