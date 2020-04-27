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

- Data storage. All FreeIPA identities, policies, configurations or certificates are stored on the Directory Server.  FreeIPA objects are stored in a suffix calculated from the realm name (e.g. dc=example, dc=com for an EXAMPLE.COM realm), certificates are stored in a second suffix, o=ipaca. Access to the different parts of the Directory Server tree is protected by the ACI configuration of the DS.

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

_# vim '192.168.3.3 ipa.example.com ipa' >> /etc/hosts _

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

### Installing the FreeIPA client

## Functionalities

## FreeIPA update

When there is a new updated version of the FreeIPA server to which you want to upgrade, in most cases it is possible to simply upgrade the underlying operating system and FreeIPA software. The FreeIPA upgrade procedure is designed to upgrade the FreeIPA Directory Server instance and also other configured services when needed and does not need to be shut down beforehand. To upgrade an existing installation of FreeIPA (from version 3.3.0 onwards) run the following command:

_# yum update freeipa-server_

It is important to make sure before you start that you have enough redundancy in your FreeIPA deployment (at least 2 working mirrors). If the upgrade procedure fails in any way, another FreeIPA server can maintain functionality until the upgrade process is successfully completed.


## Replication

## Migration

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

