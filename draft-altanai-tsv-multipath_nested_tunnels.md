---
title: "Multipath Nested Tunnels"
abbrev: "TODO - Abbreviation"
category: info

docname: draft-altanai-tsv-multipath_nested_tunnels-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Internet Engineering Steering Group"
workgroup: "Transport Area"
keyword:
 - MASQUE
 - VPN
 - Network Tunnelling protocols
venue:
  group: "Transport Area"
  type: "Area"
  mail: ""
  arch: ""
  github: "altanai/multipath-nested-tunnels"
  latest: "https://altanai.github.io/multipath-nested-tunnels/draft-altanai-tsv-multipath_nested_tunnels.html"

author:
 -
    fullname: Altanai B
    organization: Cisco Meraki
    email: altanai@outlook.com

normative:

informative:


--- abstract

TODO Abstract


--- middle

# Introduction

This document identifies the problems around simultaneously multipath and multilevel tunnels. It further highights the interoperability issues with network and application level tunnels.
Traditionaly, tunnels made up a secure interconnection called VPN and proivded a secure and private subnet to pass tarrfic with the tunnel endpoints. This setup catered to enterprises and homes alike. The egress points provided a gateway for outgoing traffic destinated for the internet but only after annoymizing the source. The protocols supported such network level tunneling include IPSec, OpenVPN and even propertiary protocols such as AutoVPN.  
With the advent of  QUIC, application level tunnels such as MASQUE is quickly gaining widespread adoption. However there are many usecases where network tunnels nest application tunnels which leads to large overheads in latency and quality of data. 

## Path discovery and resulting conflicts 

Networking protocols use hole punching to setup a path between endpoints. In presence of multiple paths available between two endpoints , multiple tunnels may be formed for primary-standby or load sharing setup. However QUIC's path discovery to explore reacahble endpoints for MASQUE proxy there may be 

## Setup and tear down 

Network level tunnels encrypt and encapsulate the payload (data send by client) in UDP with headers designed to route effectively within VPN core network towards the egree point. The treatment of traffic includes ACL checks as well as other IPM( intriusion prevention ) as per the policy or firewall rules.
The VPN gateways can either load share or selectively choose best path to route the traffic through the avaiable tunnels or use split tunnel to selectively bypass the VPN core altogether. These VPN's are often stateless which identify the tunnels using 4 or 5 tupple mappings. 

## Latency overhead

With the overhead involved with nested tunnels and path selection the time sensitive streams are impacted.

## Conflicting congestion control 

The inner tunnel carrying multiplexed streams may imply congestion control assuming it contesting with competeing traffic. However in reality could be one of the multiplxed streams. Alternatively even in a standalone tunnel the the individual streams may experience delay in RTT due to queuing in the tunnel's buffers and thus assume competeing traffic and congestion control or rate optimization algorithm could kick in. 

## Conflicting prioritization 

Mainstream techniques such as packet marking( DSCP, ECN so on ) and queuing of other non-critical traffic (Fq-CODEL, CAKE AQM) to optimize for realtime streams is essentially prioritization in practice. However, VPN providers, CSPs and/or ISP may employ polar-opposite algorithms to shape traffic based on their interest which could lead to  an overall non-synchronized approach, where a stream is prioritized in some networks and deprioritized in other networks. 

The VPN can be considered a limited premium network that protects confidentail information of an organization such as buisness communication between retail stores.

# Proposal to standardise the prioritization algorithm

Standardized Path selection decision making making algorithm would ensure same treatment of the stream across heterogeneous network environments. 

# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
