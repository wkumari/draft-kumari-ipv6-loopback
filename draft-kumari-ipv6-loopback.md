---

title: "The IPv6 Loopback Address Prefix"
abbrev: "IPv6 Loopback Prefix"
category: std

docname: draft-kumari-ipv6-loopback-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: Internet
workgroup: WG Working Group
keyword:
 - IPv6
 - Loopback
 - Documentation
venue:
  type: Working Group
  github: wkumari/draft-kumari-ipv6-loopback
  latest: https://wkumari.github.io/draft-kumari-ipv6-loopback/draft-kumari-ipv6-loopback.html

author:
 -
    ins: G. Huston
    fullname: Geoff Huston
    organization: APNIC
    email: gih@apnic.net
 -
    ins: W. Kumari
    organization: Google, Inc.
    email: warren@kumari.net

normative:
  RFC4291: # RFC4291 - "IP Version 6 Addressing Architecture"


informative:
  RFC791:  # RFC791 - "Internet Protocol"
  RFC990:  # RFC990 - "Assigned numbers"
  RFC1122: # RFC1122 - "Requirements for Internet Hosts - Communication Layers"
  RFC1884: # RFC1884 - "IP Version 6 Addressing Architecture"
  RFC2373: # RFC2373 - "IP Version 6 Addressing Architecture"
  RFC3513: # RFC3513 - "IP Version 6 Addressing Architecture"
...

--- abstract

{ **Editor's note:** This document requests the allocation of a new IPv6 address
prefix to be used for loopback instead of expanding into the existing ::/96.
The specific prefix to be allocated is TBD/96, and the document updates the
relevant RFCs and IANA registries to reflect this change. }

This document updates the IP Version 6 Address Architecture to expand the size
of the IPv6 loopback space from a single address to a /96 prefix.

This change allows for a much larger number of loopback addresses in IPv6,
which can be used for inter-process communication within a host and for network
diagnostics.

The document also updates the IANA IPv6 Address registry and the IPv6 Special
Purpose Address registry to reflect this change.

It updates RFC4291 to reflect the new loopback prefix and its functional
semantics.


--- middle

# Introduction

In the IPv4 addressing architecture, the entire 127.0.0.0/8 block is reserved
for loopback routing purposes. This generous allocation allows developers and
system administrators to utilize over 16 million distinct host-internal
addresses. While historically viewed as a byproduct of classful network design,
this large local address space has become fundamental to modern network
operations, enabling complex local testing, containerization, and inter-process
communication without port exhaustion.

By contrast, the IPv6 Addressing Architecture allocates only a single address,
::1/128, for local loopback. While sufficient for basic localhost
identification, this strict limitation creates significant operational friction
in modern IPv6-only and dual-stack environments.

## The Need for Expanded Host-Internal Space

As application architectures have evolved, the restriction of a single IPv6
loopback address has become a tangible bottleneck. Below are some examples of use cases which would benefit from an expanded loopback space:

- Application Testing and Containerization: Developers frequently run multiple
   instances of a service locally. In IPv4, these instances can bind to the
   same port on different 127.x.x.x addresses. In IPv6, developers are forced
   to modify application port numbers, which breaks environment parity and
   complicates test scaffolding.

- Local Proxying and Service Meshes: Complex local routing paradigms (such as
  sidecar proxies) often require distinct IP assignments to securely isolate
  and route traffic locally without exposing services to the external network.

- Controlled Interruption and Name Collisions: Global infrastructure services
  occasionally rely on localized sinkholes to safely manage deprecation or name
  collisions. For example, ICANN has historically utilized 127.0.53.53 for name
  collision controlled interruption. Replicating this fail-safe behavior in
  IPv6 requires a dedicated, local-only prefix.

## Terminology and Functional Semantics
While historically referred to as "loopback" space, the functional requirement
described in this document is a dedicated address block for host-internal
virtual interfaces.

The core semantic of this proposed space is strict isolation. Implementations
MUST ensure that:

- Addresses from this block can be assigned to multiple internal virtual
  interfaces simultaneously.

- Packets with a source or destination address drawn from this block MUST NOT
  be forwarded to any physical network interface.

- These packets MUST never be routed off the local host. If a router or switch
  receives a packet on a physical interface bearing one of these addresses, the
  packet MUST be dropped.

To support these operational realities, this document requests the allocation
of a new, dedicated IPv6 prefix (e.g., a /96 drawn from the IANA IPv6
Special-Purpose Address Registry) to serve as expanded host-internal virtual
interface space. This block will operate with the same fundamental constraints
as the primary ::1/128 loopback address, without overlapping with the
Unspecified Address (::/128).

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Loopback address background

The IPv4 network 127.0.0.0/8 was reserved by the IANA in {{RFC791}} where the
class-based address architecture was described. It is understood that it was
the IANA's policy at the time to reserve the first and last network of each
class, and the address prefixes 0.0.0.0/8 and 127.0.0.0/8 from the Class A
space were reserved in accordance with this practice. {{RFC990}} listed the
127.0.0.0/8 address prefix as being used by the loopback function, and this
function was listed as a requirement for all Internet hosts in {{RFC1122}}.

The "loopback" function is defined such that an outbound packet whose
destination address triggers this loopback function should loop the packet back
to the packet ingress queue for processing by the same host. No packet that is
addressed to a loopback address should ever be passed to any physical network.

{{RFC1884}}, the original IPv6 Addressing Architecture document, allocates a
single local loopback address, ::1. This single address allocation has been
preserved in all subsequent revisions to the IPv6 addressing specification
({{RFC2373}}, {{RFC3513}}, {{RFC4291}})

Loopback addresses enable localhost communication, network diagnostics, and
inter-process connections, making them essential for various local functions.

Multiple loopback addresses can increase the number of distinct sockets that
can be used for inter-process communication within a host. A larger local
loopback prefix in IPv6 can permit large numbers of distinct concurrent
loopback TCP connections within a single host, which is comparable to the
functionality supported by the IPv4 loopback address prefix.

# The IPv6 Loopback Prefix

The IANA IPv6 Address registry denotes the address prefix ::/8 as being
reserved by the IETF in {{RFC3513}} {{RFC4291}}. This range of addresses has
been partially allocated with the prefix ::FFFF:0:0/96 being used in the
context of an IPv6 transition technology to map IPv4 addresses into IPv6
addresses.

The document expands the set of IPv6 loopback addresses by adding an additional
prefix: TBD/96.

This RFC replaces section 2.5.3 of {{RFC4291}} as follows:

> The unicast addresses 0:0:0:0:0:0:0:1 and TBD/96 are called the loopback
> address. These may be used by a node to send an IPv6 packet to itself.  They
> must not be assigned to any physical interface.  They are treated as having
> Link-Local scope, and may be thought of as the Link-Local unicast addresses
> of a virtual interface (typically called the "loopback interface") to an
> imaginary link that goes nowhere.

> The loopback addresses must not be used as the source address in IPv6 packets
> that are sent outside of a single node.  An IPv6 packet with a destination
> address in the loopback space must never be sent outside of a single node
> and must never be forwarded by an IPv6 router.  A packet received on an
> interface with a destination address of loopback must be dropped.


# Security Considerations

IPv6 addressing documents do not have any direct impact on Internet
infrastructure security.

((heas: ::1/32 remains the primary loopback address and MUST (SHOULD?) be
assigned to a loopback interface.))

# IANA Considerations

The IANA is requested to assign a new IPv6 address prefix, TBD/96, to be used
for the loopback function as described in this document. This prefix should be
allocated from the IANA IPv6 Special-Purpose Address registry.

The IANA is requested to amend the IPv6 Address registry and the IPv6 Special
Purpose Address registry to record the designation of this address prefix.

The IANA is also requested to add an entry to the IPv6 Locally-Served DNS Zone
Registry for the new loopback prefix, TBD/96, to ensure that reverse DNS
lookups for addresses within this prefix are properly handled.

--- back

# Acknowledgments
{:numbered="false"}

The authors would like to thank Alejandro Acosta, Brian Carpenter, Antonis
Chariton, Owen DeLong, Gert Doering, Jeremy Duncan, Lorenzo Colitti, David
Farmer, Steinar Haug, Gábor Lencse, Terry Sweetser, Ole Trøan, and Maciej
Żenczykowski for their comments, discussions, and suggestions on this topic.

Additional thanks to John Heasley for submitting Pull Requests. In addition we
would like to thank Jen Linkova for presenting the proposal at IETF 125, as the
authors were participating in other sessions at the time.

We would also like to specifically thank Mark Smith for an earlier (2013)
effort: draft-smith-v6ops-larger-ipv6-loopback-prefix-04, which proposed a /32
designation.

The need for a loopback address prefix has long been a topic of discussion in
various forums, and we would like to acknowledge the contributions of many
individuals who have participated in these discussions over the years.
Unfortunately, at least one of the authors has a terrible memory, and has lost
track of all those who have contributed to this topic over the years, and will
be more than happy to acknowledge their input if reminded of this :-)
