# rfc2131 (DHCP):

### Purpose 
Provides configuration parameters to Internet hosts.

# Table of Contents
1. [Introduction](#1-introduction)
    1. [Changes to RFC 1541](#11-changes-to-rfc-1541)
    2. [Related work](#12-related-work)
    3. [Problem definition and issues](#13-problem-definition-and-issues)
    4. [Requirements](#14-requirements)
    5. [Terminology](#15-terminology)
    6. [Design goals](#16-design-goals)
2. [Protocol Summary](#2-protocol summary)


## 1. Introduction

### Two components 
1) protocol for delivering host-specific configuration parameters from a
   DHCP server to a host
2) a mechanism for allocation of network addresses to hosts.

### Vulnerability
"A host should not act as a DHCP server unless explicitly configured
to do so by a sys admin." This is due to specific IP settings. 
Doesn't say "cannot" act. Possibility for second, malicious, DHCP server

### Three mechanisms for IP address allocation
(networks may use one or all of these)
1) Automatic Allocation: DHCP assigns a permanent IP to a client
2) Dynamic Allocation: DHCP leases an IP to a client
3) Manual Allocation: Network administrator assigns IP to client, and DHCP
   simply conveys the assigned address to the client

### Message format 
- based on BOOTP message format for interoperability
- BOOTP is a UDP message

## 1.1 Changes to RFC 1541
- new DHCP message type, DHCPINFORM, has been added
- classing mechanism for identifying clients to servers 
  now includes "vendor"
- minimum lease time restriction has been removed
- clarifications on previous text

## 1.2 Related work
- Reverse Address Resolution Protocol (RARP) addresses problem of network
  address discovery. Includes automatic IP address assignment mechanism
- Trivial File Transfer Protocol (TFTP) provides for transport 
  of a boot image from a boot server
- Internet Control Message Protocol (ICMP) informs hosts of 
  additional routers, provides subnet mask info, and more
- BOOTP, a transport mechanism for a collection of configuration info
- Path minimum transmission unit (MTU), a discovery algorithm which can
  determine the MTU of an arbitrary internet path
- Address Resolution Protocol (ARP), a transport protocol for 
  resource location and selection

## 1.3 Problem definition and issues
- DHCP should supply clients with required configuration parameters.
  With this, a DHCP client should be able to exchange packets 
  with any other host on the internet
- Not all parameters are required for a new client. Client and
  server negotiate parameters (based on client's or subnet's needs)
- DHCP only requires config parameters related to IP protocol
- DHCP is not DNS

## 1.4 Requirements
- Must, must not, should, etc are all defined

## 1.5 Terminology
- DHCP client: Internet host using DHCP to obtain config parameters
- DHCP sever: Internet host that returns config parameters to DHCP client
- BOOTP relay agent: Internet host or router that passes 
  DHCP messages between DHCP clients and DHCP servers. 
  DHCP uses same relay agent behavior as in BOOTP protocol
- Binding: collection of configuration parameters. 
  The minimum params are an IP address bound to a DHCP client

## 1.6 Design goals
### General goals
- DHCP should be mechanism rather than policy. Must allow 
  customization for sys admins (reserved IPs, etc)
- Clients should require no manual configuration
- Networks should require no manual configuration for individual clients
- DHCP should not require a server on each subnet (that's what BOOTP relay
  agents are for)
- DHCP client must be able to handle multiple DHCP server responses
  to a request for config params
- DHCP must coexist with staticically configured hosts
- DHCP must interoperate with BOOTP relay agent behavior
- DHCP must provide service to existing BOOTP clients 

### Goals specific to the transmission of network layer parameters
DHCP must:
- Guarantee that any specific network address will not be 
  in use by more than one client at a time
- Retain client configuration across DHCP client reboot
- Retain client configuration across server reboots
- Allow automated assignment of config params to new clients
- Support fixed/permanent allocation of config params to specific clients

# 2. Protocol Summary

- From the client's point of view, DHCP is an extension of the
  BOOTP mechanism.
- Figure 1 gives the format of a DHCP message and table 1 describes
  each of the fields in the DHCP message. The numbers in parentheses
  indicate the size of each field in octets. The names for the fields
  given in the figure will be used throughout this document to refer to
  the fields in DHCP messages.
- Two primary differences between DHCP and BOOTP:
  1. DHCP defines mechanisms through which clients can be
     assigned a network address for a finite lease
  2. DHCP provides the mechanism for a client to acquire all 
     IP config params that it needs in order to operate
- Change in terminology: changed "vendor extensions" field in 
  BOOTP to "options" field in DHCP

Figure 1:  Format of a DHCP message


