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
2. [Protocol Summary](#2-protocol-summary)
    1. [Configuration parameters repository](#21-configuration-parameters-repository)
    2. [Dynamic allocation of network addresses](#22-Dynamic-allocation-of-network-addresses)
3. [The Client-Server Protocol](#3-the-client-server-protocol)


## 1. Introduction

### Two components 
1) protocol for delivering host-specific configuration parameters from a
   DHCP server to a host
2) a mechanism for allocation of network addresses to hosts.

**:warning: Vulnerability: "A host should not act as a DHCP server unless 
explicitly configured to do so by a sys admin." This is due to specific 
IP settings. Doesn't say "cannot" act. 
Possibility for second, malicious, DHCP server :warning:**

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
- New 'client identifier' option, which passes explicit client ID to a DHCP
  server. Eliminates overloading of the 'chaddr' field in BOOTP. ('chaddr' is
  used both as hardware address and as a client ID
- This client ID (chosen by the DHCP client), must be unique within the subnet
- DHCP clarifies interpretation of 'siaddr' field: address of next server to
  use in bootstrap. Returned in DHCPOFFER, DHCPACK by server
- 'options' field is now variable length (312 - 576 octets)
- DHCP clients can negotiate the use of larger DHCP messages
- For initial client configuration, DHCP requires use of the client's TCP/IP
  software. The software SHOULD accept and forward to the IP layer any packets
  delivered to the client's hardware address before the IP address is
  configured. DHCP servers and BOOTP relay agents may not be able to deliver
  DHCP messages to clients that cannot accept hardware unicast datagrams before
  the TCP/IP software is configured
- To work around this, DHCP uses the 'flags' field. The leftmost bit is
  defined as the BROADCAST (B) flag. Remaining bits must be set to 0 at this
  point and are reserved for future use

## 2.1 Configuration parameters repository
- In this stage, DHCP provides persistent storage of network parameters for
  network clients. All this means is it creates a key/value pair where the key
  a unique identifier for the client (e.g. IP-subnet-number and
  hardware-address) and the value is the set of configuration parameters for
  that client.

## 2.2 Dynamic allocation of network addresses
- Client requests the use of an address for some period of time (a "lease")
- The collection of DHCP servers guarentees not to reallocate that address
  within the requested time and attempts to return the same network address
  each time the client requests an address
- Client may extend lease with subsequent requests
- Client may issue a message to release the address back to the server
- Client may ask for a permanent assignment by asking for an infinite lease
- Even when assigning "permanent" addresses, a server may choose to give out
  lengthy but non-infinite leases in case client eventually retires
- Sometimes network addresses might need to be reassigned due to exhaustion of
  available addresses. In such cases, the allocation mechanism will reuse
  addresses whose lease has expired. The server SHOULD probe the reused addres
  before allocating, e.g., with an ICMP echo request, and the client SHOULD
  probe the newly received address, e.g., with ARP

# 3. The Client-Server Protocol
- DHCP uses the BOOTP message format
- The 'op' field of each DHCP message sent from client to server contains
  BOOTREQUEST
- The 'op' field of each DHCP message sent from server to client contains
  BOOTREPLY
- First four octets of the 'options' field of DHCP message contain the decimal
  values 99, 130, 83, and 99
- The "DHCP message type" option must be included in every DHCP message
- Throughout this document, DHCP messages that include a 'DHCP message type'
  option will be referred to by the type of the message; e.g., a DHCP message
  with 'DHCP message type' option type 1 will be referred to as a "DHCPDISCOVER"
  message

## 3.1 Client-server interaction - allocating a network address

1. Client broadcasts a DHCPDISCOVER message on its local physical
   subnet. DHCPDISCOVER message MAY include options that suggest values for
   network address and lease duration. BOOTP relay agents may pass the message on
   to DHCP servers not on the same physical subnet
2. Each server may respond with a DHCPOFFER message that includes an available
   network address in the 'yiaddr' field (and other config parameters in DHCP
   options). Might use BOOTP relay agent if necessary
3. Client receives one or more DHCPOFFER messages from one or more servers.
   Client chooses one server from which to request config params, based on
   config params offered in the DHCPOFFER messages. Then, the client broadcasts
   a DHCPREQUEST message that MUST include the 'server identifier' option to
   indicicate which server it has selected. 
   **:warning: Vulnerability: since this is a broadcast message, couldn't 
   a malicious DHCP server spoof it's ID to match the configuration parameters 
   in in the DHCPREQUEST message? :warning:** 
   The message MAY include other options specifying desired configuration values. 
   The 'requested IP address'
   option MUST be set to the value of 'yiaddr' in the DHCP message from the
   server. In order to ensure that any BOOTP relay agents forward the DHCPREQUEST
   message to the same set of DHCP servers that received the original DHCPDISCOVER
   message, the DHCPREQUEST message MUST use the same value int he DHCP message
   header's 'secs' field and be sent to the same IP broadcast address as the
   original DHCPDISCOVER message.
4. Servers receive the DHCPREQUEST broadcast. Non-selected servers use this
   message as a notification that the client has declined that server's offer.
   Selected server commits the binding for the client to persistent storage and
   responds with a DHCPACK message contatining the config params. The 'yiaddr'
   field in the DHCPACK message is filled in with the selected network address.
   If the server is unable to satisfy the DHCPREQUEST message, the server SHOULD
   respond with a DHCPNAK message
5. Client receives the DHCPACK message with config params. Client SHOULD
   perform a final check on the params (e.g., ARP for allocated network
   address), and note the duration of the lease specified in the DHCPACK. At
   this point, the client is configured. If, after ARP, the client detects that
   the address is already in use, the client MUST send a DHCPDECLINE message to
   the server and, after at least 10 seconds, restart the config process. If the
   client receives a DHCPNAK message, the client restarts the config process. If
   the client neither receives a DHCPACK or a DHCPNAK, it will retransmit the
   DHCPREQUEST.
6. Client may send a DHCPRELEASE message to server to relinquish lease

## 3.2 Client-server interaction - reusing a previously allocated network
address

If a client wishes to renew a lease, some of the steps above can be omitted.

1. Client broadcasts a DHCPREQUEST message on its local subnet. This includes
   the client's network address in the 'requested IP address' option. As the
   client has not received its network address, it MUST NOT fill in the 'ciaddr'
   field.
2. Servers with knowledge of the client's config params respond with a DHCPACK
   message to the client. Servers SHOULD NOT check that the client's network
address is already in use; the client may respond to ICMP Echo Request messages
at this point **:warning: Does this mean any DHCP server on the network can
respond with a DHCPACK? :warning:** 
If the request is invalid (e.g., the client has moved to a new subnet), 
servers SHOULD respond with a DHCPNAK message









