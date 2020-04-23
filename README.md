# FreeIPA

*under construction*

## Table of Contents
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
* [References](#references)

## Introduction

FreeIPA (Identity, Policy and Audit) is an integrated security management solution (Identity and Authentication) for Linux/UNIX network environments. A FreeIPA server provides centralized authentication, authorization, and account information by storing data about the user, groups, hosts, and other objects needed to manage the security aspects of a computer network. 

The possibilities that Freeipa can offer are defined by the following 3 features:  

| Identity | Access policy  | Trust |
| ------------- | ------------- | ------------- |
| Manage Linux users and client hosts in a realm from a central host via CLI, Web UI or RPC access. Enables single sign-on authentication for all your systems, services and applications | Defines the Kerberos authentication and authorization policies for your identities. Control services such as DNS, SUDO, SELinux, or autofs | Create mutual trust with other identity management systems such as Microsoft Active Directory |

FreeIPA is built on open source components and well known standard protocols. It is focused on easy use and automation of installation and configuration tasks.  Security aspects related to access control, or administration tasks can be fully centralized and managed through the web interface (Web UI) or the command line tool ipa (CLI). In addition, it allows you to easily configure multiple servers in a FreeIPA domain to provide redundancy and scalability. 

From a time perspective, FreeIPA releases are linked to the Fedora release program, with the current version being the 4th (4.7).

## Core technology

### 389 Directory Server
### Kerberos KDC
### NTP server
### Dogtag certificate system
### PKI
### SSSD
### Web UI
### DNS

## Installation

### Recommendations for deployment
### Installing the FreeIPA server
### Installing the FreeIPA client

## Functionalities

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

#### Other FreeIPA functionalities

https://www.mankier.com/1/ipa-server-install <br>
https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/linux_domain_identity_authentication_and_policy_guide/managing-users-permissions <br>

