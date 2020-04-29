*under construction*

![FreeIPAlogo](https://blog.ichasco.com/wp-content/uploads/2017/01/freeipalogo.png)

### Table of Contents
====================

* [Introduction](#introduction)
* [Core technology](#core-technology)
  * [389 Directory Server](#389-directory-server)
  * [Kerberos KDC](#kerberos-kdc)
  * [NTP server](#ntp-server)
  * [Dogtag certificate system](#dogtag-certificate-system)
  * [PKI](#pki)
  * [SSSD](#sssd)
  * [Web UI](#web-ui)
  * [DNS](#dns)
* [Installation](#installation)
  * [Recommendations for deployment](#recommendations-for-deployment)
  * [Installing the FreeIPA server](#installing-the-freeIPA-server)
  * [Installing the FreeIPA client](#installing-the-freeIPA-client)
* [Functionalities](#functionalities)
* [FreeIPA update](#freeipa-update)
* [Replication](#replication)
* [Migration](#migration)
* [Back-up & Restore](#back-up--restore)
* [Main FreeIPA files and directories](#main-freeipa-files-and-directories)
* [Troubleshooting](#troubleshooting)
* [References](#references)

====================

## Introduction

FreeIPA (Identity, Policy and Audit) is an integrated security management solution (Identity and Authentication) for Linux/UNIX network environments. A FreeIPA server provides centralized authentication, authorization, and account information by storing data about the user, groups, hosts, and other objects needed to manage the security aspects of a computer network. 

The possibilities that Freeipa can offer are defined by the following 3 features:  

| Identity | Access policy  | Trust |
| ------------- | ------------- | ------------- |
| Manage Linux users and client hosts in a realm from a central host via CLI, Web UI or RPC access. Enables single sign-on authentication for all your systems, services and applications | Defines the Kerberos authentication and authorization policies for your identities. Control services such as DNS, SUDO, SELinux, or autofs | Create mutual trust with other identity management systems such as Microsoft Active Directory |

FreeIPA is built on open source components and well known standard protocols. It is focused on easy use and automation of installation and configuration tasks.  Security aspects related to access control, or administration tasks can be fully centralized and managed through the web interface (Web UI) or the command line tool ipa (CLI). In addition, it allows you to easily configure multiple servers in a FreeIPA domain to provide redundancy and scalability. 

From a time perspective, FreeIPA releases are linked to the Fedora release program, with the current version being the 4th (4.7).

### Key concepts

- __Domain__. The domain consists of a group of machines that share the same configuration, policies and identity stores (includes both servers and clients). These shared properties allow the machines in the domain to be aware of each other and operate together.
- __Realm__. The realm refers to a network used by Kerberos, composed of one or more KDCs and a potentially large number of clients.

## Core technology

The operation of FreeIPA is possible thanks to the integration and combination of different technologies such as Linux (Fedora), 389 Directory Server, MIT Kerberos, NTP, DNS, Web and command line provisioning and administration tools, Dogtag (certificate system), Active Directory Integration or SSSD among others.

### 389 Directory Server

The 389 Directory Server ( previously Fedora Directory Server) is an LDAP server that in FreeIPA serves as the foundation for the entire identity management solution. 

LDAP (Lightweight Directory Access Protocol) is a set of open source protocols used to access centralized remote directory services on a network. LDAP is based on the connection between client and server. Directory data is stored on the LDAP server. These directories are typically used to contain stored user information.

The FreeIPA directory server acts as a back-end where FreeIPA stores all data for all identity, authentication (Kerberos) and authorization services and other policies. This technology allows FreeIPA to also provide a multi-master environment, where the administrator can deploy several FreeIPA mirrors. The name "389" is derived from the port number used by the LDAP.  

- Data storage. All FreeIPA identities, users, groups, policies, configurations or certificates are stored on the Directory Server.  All this FreeIPA data is stored with __suffixes__. There are two types of suffixes; a) Domain Suffix: contains all data related to the domain, and is calculated from the realm name (realm) (e.g. dc=example, dc=com, for a realm EXAMPLE.COM). b) CA Suffix: stores the certificates for the components of the certificate system (o=ipaca). It is only present on servers with a certification authority (CA) installed.

### Kerberos KDC

Kerberos is an authentication protocol created by MIT that uses symmetric key cryptography to validate users with network services (thus avoiding having to send passwords over the network). Kerberos requires a trusted third party (Key Distribution Center, or KDC) consisting of an Authentication Server (AS) and a Ticket Granting Server (TGS) to authenticate users. 

Kerberos is a cornerstone of a FreeIPA server as it provides authentication services for the entire FreeIPA environment. Just as a Kerberos KDC server reads all user identity information from the Directory Server, FreeIPA implements its own ipa-kdb KDC which reads and writes all required information to the LDAP tree.

### NTP server

The Kerberos protocol is very sensitive to the correct timing between the KDC and the authentication nodes. With time differences of more than several minutes, authentication will not work. For this reason, the server installers set up an NTP server (ntpd at the moment) on each FreeIPA server. 

### Dogtag certificate system

The Dogtag Certificate System is an open source Certificate Authority (CA) that supports all aspects of certificate life cycle management. FreeIPA includes an integrated Certificate Authority based on the Dogtag project which increases the authentication capabilities.

### PKI

The Dogtag project provides FreeIPA with the integrated PKI service. PKI signs and publishes certificates for FreeIPA's services and hosts, it also takes care of the renewal since these have a limited validity. For this purpose, the Certmonger daemon runs on all clients and manages the renewal for the services that use it.

### SSSD

System Security Services Daemon (SSSD) is a service that aims to provide a single interface for NSS and PAM, from which to manage remote access to LDAP directories and multiple authentication mechanisms from a single point. 
SSSD is a client-side component that integrates FreeIPA as an authentication and identity provider to multiple different hosts. In addition, additional client-side management functionality such as SUDO, HBAC policies, etc. can be achieved through SSSD. The SSSD is configured by default in FreeIPA.

### Web UI

Web UI is a web application for the administration of FreeIPA. It uses the JSON-RPC interface to access the FreeIPA API. This ensures that available data or changes that can be made are limited to the security context of the LDAP object (user) that is assigned to an active session.

It has virtually the same capabilities as the command line (CLI), so you can freely choose between the two for administration purposes. Web UI has two modes of operation, a limited mode for user use, and a full mode for administration.

The Web UI's URL access point is https://test.example.com/ipa/ui/ where test.example.com is the name of the FreeIPA server.

![FreeIPAWebUI1](https://access.redhat.com/webassets/avalon/d/Red_Hat_Enterprise_Linux-7-Linux_Domain_Identity_Authentication_and_Policy_Guide-en-US/images/0b67bd0c53b2b26b1d9ce416280f1e83/web_ui_login_screen.png)

### DNS

The FreeIPA integrated DNS service allows the administrator to manage and serve the DNS records in a domain. Once the client registers with the domain, it uses the DNS to locate the servers and services within the domain. The DNS component in FreeIPA is optional and the user can choose to manage all DNS records manually or through another DNS server. The DNS integration is based on the bind-dyndb-ldap project, which enhances the BIND name server to be able to use the LDAP instance of the FreeIPA server as a data backend (data is stored in the cn=dns entry, using the schema defined by bind-dyndb-ldap).

The DNS component of FreeIPA was designed and built on several basic assumptions and objectives that should always be considered:

- Assumptions: The DNS is difficult to handle and many administrators who want to deploy FreeIPA would have difficulty configuring the DNS correctly.  DNS is critical to having a decent experience with Kerberos. Single-master DNS is prone to errors, especially for inexperienced administrators.
- Objectives: To provide an integrated DNS server that can be used to facilitate FreeIPA deployment. To provide the ability to stop and remove mirrors without worrying about the special "master" DNS server. Provide an alternative option for users with existing DNS infrastructure: Provide a means to integrate FreeIPA with the existing DNS infrastructure. The goal is NOT to provide a general purpose DNS server. Features that go beyond facilitating the deployment and maintenance of FreeIPA are explicitly out of reach.

## Installation

### Recommendations for deployment

Before deploying FreeIPA it would be convenient to make decisions about the following elements, as it would be very complicated to make some modifications later:

- DNS. Independently of whether the integrated service is used in FreeIPA or not, they play an important role in the identity management functionality, especially in Kerberos, and therefore it is necessary to take into account that: As for the domain, the FreeIPA server must always have its own primary domain (e.g. example.com or ipa.example.com). This domain must not be shared with another Kerberos-based identity management system, otherwise collisions will occur. Client hosts do not need to be in the same domain as the servers. For example, the FreeIPA server can have a domain ipa.example.com and the clients have a domain clients.example.com, there just has to be a clear mapping between the DNS domain and the Kerberos realm.
- Name of the Kerberos realm. When the first FreeIPA server is installed, a Kerberos realm name is always defined for the installation. To properly select the realm name, you must take into account: a) The realm name must not conflict with any other existing Kerberos realm name (for example, the name used by Active Directory), b) The Kerberos realm name must be the uppercase version (EXAMPLE. COM) of the primary DNS domain name (example.com), c) FreeIPA clients from multiple different DNS domains (example.com, example.net, example.org) can join a single Kerberos realm (EXAMPLE.COM), d) A FreeIPA installation always represents a single Kerberos realm.
- It is recommended that you avoid deploying other applications or services on the FreeIPA server. This is for reasons of performance, stability, and ease of migrating the server from FreeIPA.
- FreeIPA runs in a multi-master replicated environment. Generally whenever possible it is recommended to have at least 2-3 mirrors in each data center to obtain adequate load balancing and redundancy (these additional servers are mirrors of the initial master server).
- Each client installation using _ipa-client-install_ requires access to port 443 (HTTPS) on the FreeIPA server. This is because once enrolled, the client uploads its own SSH keys and performs a few more operations. The IPA CLI also uses the same port to communicate with the server. Therefore, access to HTTPS (443) from the client side is required.


### Installing the FreeIPA server

First, before installing FreeIPA, it is recommended to upgrade the system packages:

_# yum -y update_

And in addition it is necessary to make sure that the prerequisites are met such as establishing a static hostname or satisfying certain aspects about the DNS (although in this example the DNS service integrated in FreeIPA is not configured)(to see how it's done, look in the references).  This is necessary because Kerberos authentication is based on a static hostname; if the hostname changes, the authentication may stop working. Therefore, the FreeIPA server should not use a dynamically configured hostname from a DHCP, but a static one configured in /etc/hostname. In addition, the FreeIPA installer is quite demanding with the DNS configuration, and will check that a) the hostname cannot be localhost or localhost6, b) the hostname must be fully qualified, c) the hostname must be resolvable.

To solve all this, establish and check that the host name is in FQDN (fully qualified domain name) format (a FQDN is a name that includes the computer name and the domain name associated with that computer):

_# hostnamectl set-hostname ipa.example.com_ <br>
_# hostname -f_

The host is added to the list of hosts in FQDN format so that it is resolvable (with the condition that it must be the first one)( in case you don't choose the DNS service):

_# vim '192.168.3.3 ipa.example.com ipa' >> /etc/hosts_

The next step is to open the server ports. The ports that FreeIPA uses have to be opened so that remote clients or additional masters can connect to them. Fedora comes with predefined service rules for FreeIPA that open Kerberos, HTTP, HTTPS, DNS, NTP, and LDAP (TCP ports: 80, 88, 389, 464 and UDP: 88, 123, 464).

_# firewall-cmd --add-service=freeipa-ldap --add-service=freeipa-ldaps --permanent_

Once all these prerequisites are ready, we proceed to install FreeIPA (as root):

_# dnf -y install freeipa-server_

The previous command installs all the necessary packages and dependencies (389-ds, krb5, ...), but does not configure FreeIPA, to proceed with the configuration process, you must run the following command (as root):

_# ipa-server-install_

The command will first gather all the necessary information, for that the installer will ask for the parameters (server name, domain and realm) and for the services, later it will configure them (it will also ask for the integrated DNS service, but in this case we will press the "enter" key to skip the process). It will also ask for the passwords to be assigned to the super-users. This command can take all these parameters in the same call (see man _ipa-server-install for details_), of which the most important are:

--realm=REALM_NAME <br>
--domain=DOMAIN_NAME <br>
--admin-password=ADMIN_PASSWORD <br>
--mkhomedir <br>
--hostname=HOST_NAME <br>

The output of the installer is quite long, you can see how all the components are configured, restarted and checked. At the end of the output, there are some steps needed to complete the functionality (but not for the installation process itself) (we have already carried out e.g. opening the ports). 

Finally, the creation of the home directory is enabled with the first user login:

_# authconfig --enablemkhomedir --update_

To uninstall the FreeIPA server, run the following command:

_# ipa-server-install --uninstall_

But in case there are several replicated servers, you must first execute this command on the others to leave them out of the topology:

_# ipa server-del ipa.example.com_

** This installation has been done in Fedora, but the steps to do it in Centos or Ubuntu are the same, in the references there are the FreeIPA installation manuals in both systems **

### Installing the FreeIPA client

On the client, as on the server, the system packages must be updated and the host name must be set in FQDN format. In addition, you must add the address and host name of the freeipa server (as well as your own) in the client's host list.

_# hostnamectl set-hostname client.example.com_ <br>
_# vim '192.168.3.3 ipa.example.com ipa' >> /etc/hosts_ <br> 
_# vim '192.168.3.30 client.example.com client' >> /etc/hosts_ <br>

All ports must also be opened for communication with the server. Once this is done, the Freeipa client can be installed:

_# dnf –y install freeipa-client_

Next, you have to run the FreeIPA client configuration process. During the process, the IPA server name (ipa.example.com) and the domain will be requested among other things, as well as the FreeIPA server username and password in order to enroll the client. This configuration command, just as on the server, can take different parameters in the same call (see _man ipa-client-install_ for details). In this case, the host name has been passed as a parameter and the creation of the user's home is set at the first login.

_# ipa-client-install --hostname='hostname -f' --mkhomedir_

Once the client has been successfully installed, the following message will appear:

_# Client configuration complete_

The installation script does not remove any previous LDAP and SSSD settings from the _/etc/openldap/ldap.conf_ and _/etc/sssd/sssd.conf_ files. If the settings in these files were previously changed, the script adds the new client settings, but comments on them.

To uninstall the client, the command is executed:

_# ipa-client-install --uninstall_

Uninstalling removes the client from the domain, along with all FreeIPA specific settings for system services.

## Functionalities

### Basic commands

To be able to perform any administrative task in FreeIPA you need to authenticate yourself on the server. During the configuration process two users were requested to be created, the first of which, Directory Manager, is the super-user to be used for low-level tasks. For normal administrative activity, an account manager, Admin, has been created. To authenticate as an admin you have to run:

_# kinit admin_ <br>
_Password for admin@EXAMPLE.COM:_

The password established during the installation will be requested. As a result of this operation you will get the Kerberos ticket. This also serves to test whether the installation was successful and therefore it is possible to start authentication with the server. In addition, you can be asked for information on the validity of the logged-in session:

_# klist_ <br>
_Ticket cache: KEYRING:persistent:0:0_ <br>
_Default principal: admin@EXAMPLE.COM_ <br>
_Valid starting     Expires            Service principal_ <br>
_06/29/18 22:52:40  06/30/18 22:52:36  krbtgt/EXAMPLE.COM@EXAMPLE.COM_ <br>

FreeIPA provides a manual from which the functionalities of the main command ipa are shown. 

_# man ipa_

Some of the most common administration tasks that can be performed in FreeIPA are shown below:

Check the status of the FreeIPA service:

_# ipactl status_

Start, stop and restart the FreeIPA service:

_# ipactl start_ <br>
_# ipactl stop_ <br>
_# ipactl restart_ <br>

Show a user:

_# ipa user-show USER_

Search for users:

_# ipa user-find USER_

List all users:

_# ipa user-find --all > users.txt_

Add a new user (the user can log-in using: _# ssh user@freeipahost_)(at the first login you will be asked to change the password):

_# ipa user-add NOMBRE_USUARIO --first=NOMBRE --last=APELLIDOS -uid=UID --gidnumber=GID --email=EMAIL@EMAIL.COM --shell=/bin/bash --password_

Delete a user:

_# ipa user-del USER_NAME_

Change a user's password:

_# ipa user-mod USER -password_

Show the suffixes of the FreeIPA topology:

_# ipa topologysuffix-find_

Show all topology servers along with the services they offer:

_# ipa config-show_

Display a list of features enabled on a particular server.

_# ipa server-show_

Show the current domain level. The domain level indicates what operations and capabilities are available in FreeIPA (domains can be 0 or 1): 

_# ipa domainlevel-get_

### Advanced Management

By default, only the administrator user can manage the lifecycle of the user and deactivate or activate the accounts. But FreeIPA also contains an option that allows a non-admin user to acquire certain permissions that are not assigned to them. Among the privileges related to user account management included in FreeIPA we can highlight among others modifying users and resetting passwords, or user management. For a non-administrator user to acquire administrator privileges, the following steps must be followed:

Create a new role:

_# ipa role-add --desc "Description" "Role Name"_

To this new role, the permissions you want to grant are added:

_# ipa role-add-privilege "Role Name" --privileges=" User Administrators"_

To ensure that the role has been created correctly and has been assigned the privileges it can be executed:

_# ipa role-show "Role Name"_

Grant a non-administrator user the role with the privileges:

_# ipa role-add-member "Role Name" --users=stage_user_admin_

Another functionality offered by FreeIPA is its Web UI. All IPA tasks can be done in the web interface in addition to the ipa command line tool. To access the administration panel, use the URL https://ipa.example.com/ where ipa.example.com is the hostname of the FreeIPA server. In case you want to log-in as administrator, the web access username is admin and the password is the one provided in the configuration step for the admin user. 

But this is in the case of running from the same host and having a browser installed on it, but what about the case where the access is remote from outside the network and has to be passed from a head node? In this case the following steps must be followed:

1st: install firefox in the head server from where we are going to access:

_# sudo dnf install firefox_

2nd: access with ssh -X to the head server 

_# ssh -X root@head.server.es_
				
3rd: launch the web from the FreeIPA server

_# firefox https://ipa.example.com/ipa/ui/ &_


## FreeIPA update

When there is a new updated version of the FreeIPA server to which you want to upgrade, in most cases it is possible to simply upgrade the underlying operating system and FreeIPA software. The FreeIPA upgrade procedure is designed to upgrade the FreeIPA Directory Server instance and also other configured services when needed and does not need to be shut down beforehand. To upgrade an existing installation of FreeIPA (from version 3.3.0 onwards) run the following command:

_# yum update freeipa-server_

It is important to make sure before you start that you have enough redundancy in your FreeIPA deployment (at least 2 working mirrors). If the upgrade procedure fails in any way, another FreeIPA server can maintain functionality until the upgrade process is successfully completed.


## Replication

A FreeIPA server can be replicated by cloning the existing configuration. Servers and their replicas share an identical configuration, since the replica installation process copies the configuration of the existing server and installs the replica based on that configuration. Maintaining multiple replicas is one of the best practices.

![Replica0](https://access.redhat.com/webassets/avalon/d/Red_Hat_Enterprise_Linux-7-Linux_Domain_Identity_Authentication_and_Policy_Guide-en-US/images/f0355d1ae94f1534d49a0f4ba8f4d004/ipa-replica.png)

Data stored on a FreeIPA server is replicated based on replication agreements. This means that two servers with a replication agreement share their data (multi-master). Replication agreements are always bilateral: data is replicated from the first replica to the other, as well as from the other replica to the first. When a replication agreement is configured, topology suffixes of the same type are also shared.  It is advisable to set up at least two agreements per replica, but no more than four. 

![Replica1](https://access.redhat.com/webassets/avalon/d/Red_Hat_Enterprise_Linux-7-Linux_Domain_Identity_Authentication_and_Policy_Guide-en-US/images/feeaec82a9000fba3404cdcd616407a6/replica_recommendations_three.png)

The installation requirements for the replica are the same as for the main server, while the replica must run on the same version of Linux and FreeIPA as the original. In addition, TCP port 22 on the master server should be kept open during the mirror's configuration process. This port is required in order to use SSH to connect to that server. When a new replica is configured, the installer automatically creates Service Resource Records (SRV) in the DNS, allowing customers to automatically discover the replica and its services.

The replica can be installed on a client or on a machine that does not yet belong to the domain. To install the replica on a client, you must ensure that the client is authorized to be promoted. To do this, you must log-in as admin before installing the replica with the command (or pass those credentials as parameters):

_# ipa-replica-install_ <br>
_# ipa-replica-install --main admin --admin-password admin_password_

To install the replica on a non-domain machine, the above command first registers the machine as a client and then installs the replica components.   

Checking that the replica works is as simple as creating a user on one server, and proving on the other that the user has been created.

## Migration

It is possible that FreeIPA has to be migrated, either on the same platform or on the same or different OS. This migration can be done from NIS to FreeIPA, from LDAP to FreeIPA, or from FreeIPA to FreeIPA.

It may be the case that you want to start a new FreeIPA deployment and cannot simply create a replica, since for example the realm is now different.  In this case you have to do a new installation of FreeIPA and migrate the data. Users and groups can be migrated using the migrate-ds command. To do this, just make sure that the FreeIPA Kerberos related attributes are not migrated, as they have to be generated again by the new FreeIPA server along with its configuration and keys. The migration command does not migrate private user groups, and an example would be as follows:

_# ipa migrate-ds --bind-dn="cn=Directory Manager" --user-container=cn=users,cn=accounts --group-container=cn=groups,cn=accounts --group-objectclass=posixgroup --user-ignore-attribute={krbPrincipalName,krbextradata,krblastfailedauth,krblastpwdchange,krblastsuccessfulauth,krbloginfailedcount,krbpasswordexpiration,krbticketflags,krbpwdpolicyreference,mepManagedEntry} --user-ignore-objectclass=mepOriginEntry --with-compat ldap://migrated.freeipa.server.test_

At other times FreeIPA may not be able to be upgraded due to OS issues, and therefore you may want to migrate FreeIPA to another system. To do this, a replica file must be created on one of the servers:

_# ipa-replica-prepare ipa2.example.com_

And copy the replica file to the new server that will host the FreeIPA server. 

_# scp /var/lib/ipa/replica-info-ipa2.example.com.gpg root@ipa2.example.com:/var/lib/ipa/_

Then you have to install FreeIPA on the destination server:

_# ipa-replica-install /var/lib/ipa/replica-info-ipa2.example.com.gpg_

After that, the new server must have all the services that the original server had, all the users must be recognized, it must have the same authentication key in kerberos, etc. Finally, the old server should be stopped and the FreeIPA service removed.

In the following link you can see in more detail a FreeIPA migration from RHEL6 to RHEL7: <br>
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/linux_domain_identity_authentication_and_policy_guide/migrate-6-to-7 <br>

## Back-up & Restore

FreeIPA provides a solution to manually back-up and restore the system. FreeIPA performs backups, in which the system creates a directory containing information about your configuration. This directory can then be used to restore the original configuration.

FreeIPA provides 2 back-up options: a) full server back-up, where a backup of all files on the server is created, and b) data back-up only, where only one backup of the LDAP data and the change log is created. By default, FreeIPA stores the backups created in the _/var/lib/ipa/backup/_ directory.

The following respective commands are used to create a backup of the FreeIPA server, both complete and data only:

_# ipa-backup_ <br>
_# ipa-backup --data_ <br>

As a preliminary step to restoring a server, it is recommended to uninstall it before performing a complete restoration of the server. Once this is done, a server is restored with the following command:

_# ipa-restore /var/lib/ipa/backup/_

To avoid authentication problems on a master, the SSSD cache must be cleared after a restore:

_# systemctl stop sssd_ <br>
_# find /var/lib/sss/ ! -type d | xargs rm -f_ <br>
_# systemctl start sssd_ <br>

Once a server is restored it becomes the only source of information for FreeIPA, and so the other master servers are rebooted from the restored server. 

Any data created after the last backup is lost. If possible, always rebuild the lost server by reinstalling it as a replica.

It is important to note that you cannot restore a backup to a different host than the one on which the backup was originally created.

## Main FreeIPA files and directories

### Server/client configuration files and directories

- /etc/ipa -> FreeIPA main configuration directory
	- /etc/ipa/default.conf -> FreeIPA main configuration file. This is common between the server and the clients. It is read when servers and clients are started and when the user uses the ipa utility. The [global] section is mandatory and is the only one used. An example of a default configuration file is:

## Troubleshooting

## References

#### Main FreeIPA manuals

https://www.freeipa.org/page/Main_Page <br>
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/linux_domain_identity_authentication_and_policy_guide/index <br>

#### Definitions of technologies used by FreeIPA

https://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-rg-es-4/s1-kerberos-works.html <br>
https://www.profesionalreview.com/2019/01/05/ldap/ <br>
https://www.port389.org/docs/389ds/FAQ/getting-started.html <br>

#### FreeIPA Installation Manuals

https://linuxconfig.org/how-to-install-and-configure-freeipa-on-red-hat-linux <br>
https://www.server-world.info/en/note?os=Fedora_31&p=freeipa&f=1 <br>
https://computingforgeeks.com/install-and-configure-freeipa-server-on-ubuntu/ <br>
https://computingforgeeks.com/install-freeipa-server-centos-7/ <br>
https://www.server-world.info/en/note?os=Fedora_31&p=freeipa&f=3 <br>

#### Other FreeIPA functionalities

https://www.mankier.com/1/ipa-server-install <br>
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/linux_domain_identity_authentication_and_policy_guide/managing-users-permissions <br>

