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

Within Kerberos terminology, it is important to note the term __realm__, which refers to a network used by Kerberos, composed of one or more KDCs and a potentially large number of clients.

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

### DNS

## Installation

### Recommendations for deployment
### Installing the FreeIPA server
### Installing the FreeIPA client

## Functionalities

## FreeIPA update

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

