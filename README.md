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

| Identidad | Política de acceso | Confianza |
| ------------- | ------------- | ------------- |
| Administra usuarios de Linux y hosts clientes en un realm (ámbito) desde un host central mediante CLI, Web UI o acceso RPC. Habilita autenticación de inicio de sesión único para todos sus sistemas, servicios y aplicaciones. | Define las políticas de autenticación y autorización de Kerberos para sus identidades. Controla servicios como DNS, SUDO, SELinux o autofs.  | 	Crear confianza mutua con otros sistemas de gestión de identidades como Microsoft Active Directory |



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

