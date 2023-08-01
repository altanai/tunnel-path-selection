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
    fullname: Altanai Bisht
    organization: Cisco Meraki
    email: albisht@cisco.com

normative:

informative:


--- abstract

TODO Abstract


--- middle

# Introduction

This document identifies the problems around simultaneously multipath and multilevel tunnels. It further highights the interoperability issues with network and application level tunnels.
Traditionaly, tunnels made up a secure interconnection called VPN and proivded a secure and private subnet to pass tarrfic between the tunnelled endpoints. This setup catered to enterprises and small private home networks alike. The egress points provided a gateway for outgoing traffic destinated for the internet but only after annoymizing the source. The protocols supported such network level tunneling include GRE, L2P, IPSec, OpenVPN and even propertiary protocols such as AutoVPN.  
With the advent of  QUIC, application level tunnels such as MASQUE is quickly gaining widespread adoption. However there are many usecases where network tunnels nest application tunnels which leads to large overheads in latency and quality of data. 

# Problem Statement
Many network service providers create sharing and resiliency by making traffic to be split, load balanced across multiple uplinks or failover to standby when any of the exisitng uplinks fail. Many mordern implementations use traffic shaping policies based on the network metrics such as loss, latency. Others can  prioritize flows based on intelligent classification ML models. There are many application specific logic that improve the selection process and also encourage fairness however such system designs lacks reusability on both the control and data plane.

## Path discovery and resulting conflicts 
Networking protocols use hole punching to setup a path between endpoints. In presence of multiple paths available between two endpoints , multiple tunnels may be formed for primary-standby or load sharing setup. However QUIC's path discovery to explore reacahble endpoints for MASQUE proxy there may be 

## Setup and tear down 
Network level tunnels encrypt and encapsulate the payload (data send by client) in UDP with headers designed to route effectively within VPN core network towards the egree point. The treatment of traffic includes ACL checks as well as other IPM( intriusion prevention ) as per the policy or firewall rules.
The VPN gateways can either load share or selectively choose best path to route the traffic through the avaiable tunnels or use split tunnel to selectively bypass the VPN core altogether. These VPN's are often stateless which identify the tunnels using 4 or 5 tupple mappings. 

## Processing overhead 
Nested tunnels greatly impact the traffic quality often resulting in fragmentation and defragmentation, as well as computational overhead in multiple encapsulations which incrreases the processor workload.

## MTU and Fragmentation 
QUIC has set MTU size limits and UDP in contrast has a large limit. As opposed to this a non-tunneled traffic has lesser MTU restrictions and thus saves up processing time on fragmentation and defragmentation.

## Increased Latency
Considering limited bandwidth, with the overhead involved with nested tunnels and path selection the time sensitive streams are impacted.

## Conflicting congestion control 
There are potential issues of 
The inner tunnel carrying multiplexed streams may imply congestion control assuming it contesting with competeing traffic. However in reality could be one of the multiplxed streams. Alternatively even in a standalone tunnel the the individual streams may experience delay in RTT due to queuing in the tunnel's buffers and thus assume competeing traffic and congestion control or rate optimization algorithm could kick in. 

## Conflicting prioritization 

Mainstream techniques such as packet marking( DSCP, ECN so on ) and queuing of other non-critical traffic (Fq-CODEL, CAKE AQM) to optimize for realtime streams is essentially prioritization in practice. However, VPN providers, CSPs and/or ISP may employ polar-opposite algorithms to shape traffic based on their interest which could lead to  an overall non-synchronized approach, where a stream is prioritized in some networks and deprioritized in other networks. 

The VPN can be considered a limited premium network that protects confidentail information of an organization such as buisness communication between retail stores.

Hybrid work and move towards private access has increased the interest in tunneling traffic between endpoints. However at present, the traffic steering decision is made in a limited scoped or rule based manner which is different for various networks and service providers. Instead an alternative dynamic strategy is proposed which gauges the confidence in the various available options dynamically and may choose to send data directly via edge gateway, use one or more of the available tunnels or create a new on-demand tunnel, leveraging any of the tunneling protocols best suited.  

At present, in the case of multiple active uplinks connecting to various ISPs, there are multiple techniques to steer or prioritize traffic across the network, which may include, 

### 1: Full or Split tunnel based on DSCP tags( Diffserv). 
For example in case of dual uplinks connected and two tunnels active, use the one more with better performance for RTP traffic. 

### 2: Multiple Active VPN Uplinks used in weighted round robin order or ECMP 
Traffic Shaping generic rules based on QoS ( such as MOS, jitter other customized score), attributes such as app type or address or even client identifier such as mac address based shaping. 

### 3. Policy-Based routing that use flow preferences to pin traffic to a particular path. 
It could also be Geo or proximity based rules

### 4. Dynamic Path Selection with application identification 
The aplication knows its type and can directly feed the information to the algorithm. If the sender is not aware of the application it can attempt to obtain this information from intelligent ML models as Network Based Application Recognition (NBAR) from Cisco or even rely on Explicitly identifying Provisioning Domain Names through a Router Advertisement (RA) option. Discovering Provisioning Domain Names and Data, its architecture involving the authenticatio and trust model has been decribed in prior work [RFC8801, RFC7556] 

MASQUE (QUIC multiplexing)

# Proposal to standardise the prioritization algorithm

By dynamically deciding the tunnel type for a stream or packet, we could avoid the non-performing or counter-productive use-cases such as 
* added latency on real time streaming 
* added encryption for already end-to-end encrypted VoIP calls 
* NAT traversal nightmare 
* nested tunneling and double congestion control 
* exhausting limited bandwidth available from VPN providers 

The proposal is to standardize an algorithm that computes multiple available options and decides whether, on-demand tunnels are created (via  MASQUE, IPSec, SSH, GRE other proprietary protocols such as AutoVPN), an existing set of tunnels be reused or any other route, based on the current network dynamics and vulnerability of the traffic. 
Standardized Path selection decision making making algorithm would ensure same treatment of the stream across heterogeneous networks. 

Input Datapoints for the algorithm :

1. Entropy headers
Entropy headers are extension to traditional packet header that include information about the randomness of rthe packet's payload. These help distributing traffic more evenly in a a ultipath network, mitigating the risk of hotspots and potential congestion points.
2. Provisioning domains
3. Network state :
 YANG models telemetry information 
4. Vulnerabilty of Data : End to end encrypted data would have a low vulnerabilty score than plaintext data.
5. Time sensitivity of data : Many drafts and proposals reserve networ resources or prioritze critical support traffic such as E911. While the time sensitivity is subjected to application's decision and a machine learning models can be supervised to classify imposters, the proposed algorithm does not suggest a way to compute this value itself.
6.    
7. carbon footprint is an optional data point that may be added to algorithim. The data for the carbon footprint can be based on multiple factors which may include Datacenter's carbon footprint, energy grid's instantenous carbon footprint for fuel mix, sender's application or network provider's carbon footprint among other options.
8. 


On the other hand the cons may include 
ambuiguity 

## Design goals 

Application do not need to understand Failover Groups with multiple uplinks.
Avoid strict priority ordering of multiple paths
Avoid static scheduling algorithms such as weighted round robin which do not benifit mnay usecases such as low latency path for time-sensitive data. 
Other indirect impacts of the algorithm may also be to overcome strategies which unfairly maximize bandwidth usage in the public internet. 

## Algorithm Requirements

The algorithm has primary goal of optimization the network path for the traffic stream for achieving the best result in terms of fairness and criticality. 
This algorithm must be implemented on a stateful system where the sender can make decisions on the path to be traversed. 


The algorithm requires prior categorization pf paths such as uplinks based on their characteristics as type and bandwidth for example ethernet/10 megabit per second or cellular/5 megabit per second. The algorithm also requires available tunneling protocols for data transfer such as GRE, L2P, IPSec, OpenVPN and even propertiary protocols such as AutoVPN as avaiable.

The algorithm doesnt involve the approach to break out critical traffic from non-critical traffic. Some approaches to do that have been summarized in following documents:-

The algorithm should fairly suggest critical traffic to be passed through the available best path or failover to best path when experincing issues such as loss, jitter on current path.

It should 
- encourage load sharing between available paths
- collect all data points in realtime
- weights for various data points must be adjustable
- have the ability to input feedback from observed performance which may be due to nested congestion control or multi-layer redudnant security etc

It should not 
- cause a surge of unnecessary traffic
- be impacted by NAT setups
- impact the outbound firewall policies


##  Implementation Strategies 
The simplest venue for the implementation of the Path selection algorithm is within the application itself. 
This algorithm require no specific support from the operating system beyond the commonly available APIs that provide transport service.
The algorithm has feedback on the path consumed by all applications for this sender and tries to balance the utilization by load balacing between them.
The proposed path selection algorithm is only tasked with suggesting the protocol and path and can be overridden by the application.

Examples of the decision that may be taken by the standardized algorithm could include; 
Example 1 : Resource intensive ultra low latency application benefit from direct internet connection such as multiplayer games and if the algorithm's path suggestion doesn't meet the latency target the application can select its own path.
Example 2 : Tunneling the VoIP traffic via separate routes, for example signaling plane data on VPN tunnel, and media via SRTP/DTLS tunnel. 
Example 3 : SIP trunk calls may actually benefit from a dedicated IPSec tunnel, pre NATed, pre authenticated and secure, as it would avoid the delay in resetting the path given the volume of calls expected between two endpoints.  
Example 4 : Heavy file downloads such as VoD could benefit by load sharing between multiple tunnels.  

### Edge cases 
In the edge case where  there are no other data points to compute the selection logic then every avaiable concurrent path would get a weighted proportion of traffic based on its bandwidth cap. For example if a system has 2 uplinks capable of tunneling trafic with 25Mbps and 75Mbps then uplink 1 will get 25% of the flows while uplink 2 gets 75%.   

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
