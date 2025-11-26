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

This document updates the IP Version 6 Address Architecture to define the IPv6
address prefix ::/96 as the Loopback address prefix.


--- middle

# Introduction

In the IP address architecture, a loopback address is a special IP address used
by hosts to send data to itself. Packets directed to a loopback address are
automatically routed back to the sending host’s network software stack without
ever reaching the physical network interface. This has use in some forms of
testing and is used to support a non-network method to facilitate local
inter-process communication within a host.

This document updates the IP Version 6 Address Architecture to define the IPv6
address prefix ::/96 as the Loopback address prefix.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Loopback addresses

The IPv4 network 127.0.0.0/8 was reserved by the IANA in {{RFC791}} where tha
class-based address architecture was described. It is understood that it was
the IANA's policy at the time to reserve the first and last network of each
class, and the address prefixes 0.0.0.0/8 and 127.0.0.0/8 from the Class A
space were reserved in accordance with this practice. {{RFC990}} listed the
127.0.0.0/8 address prefix as being used by the loopback function, and this
function was listed as a requirement for all Internet hosts in {{RFC1122}}.

The "loopback" function is defined such that an outbound packet whose
destination address triggers this loopback function should loop the packet back
to the packet ingress queue for processing by the same host. No packet that is
addresses to a loopback address should ever to passed to any network.

{{RFC1884}}, the original IPv6 Addressing Architecture document, allocates a
single local loopback address, ::1. This single address allocation has been
preserved in all subsequent revision to the IPv6 addressing specification
({{RFC2373}}, {{RFC3513}}, {{RFC4291}})

Loopback addresses enable localhost communication, network diagnostics, and
inter-process connections, making them essential for various local functions.

Multiple loopback addresses can increase the number of distinct sockets that
can be used for inter-process communication within a host. A larger local
loopback address prefix in IPv6 can permit large numbers of distinct concurrent
loopback TCP connections within a single host, which is comparable the the
functionality supported by the IPv4 loopback address prefix.

# The IPv6 Loopback Prefix

The IANA IPv6 Address registry denotes the address prefix ::/8 as being
reserved by the IETF in {{RFC3513}} {{RFC4291}}. This range has been partially
allocated with the prefix ::FFFF:0:0/96 being used in the context of an IPv6
transition technology to map IPv4 addresses into IPv6 addresses.

The document expands the set of IPv6 loopback addresses to span the address
prefix range ::0 through to ::FFFF:FFFF (or ::/96 in prefix notation).

This RFC replaces section 2.5.2 and 2.5.3 of {{RFC4291}} as follows:

>The Loopback prefix
>
>The unicast address prefix ::/96 is called the loopback address prefix.
>
>The first address of this address prefix block, 0:0:0:0:0:0:0:0 (::/128), is
>also termed the "unspecified address". This address MUST NOT be assigned to
>any node, as it indicates the absence of an address. One example of the use of
>this address is in the Source Address field of any IPv6 packets sent by an
>initializing host before it has learned its own address.
>
>All other loopback addresses drawn from this loopback address prefix may be
>used by a node as a destination address to send an IPv6 packet to itself.
>These addresses MUST NOT be assigned to any physical interface. These
>addresses are treated as having Link-Local scope, and may be thought of as the
>Link-Local unicast address prefix of a virtual interface (typically called the
>"loopback interface") to an imaginary link that goes nowhere.
>
>All loopback addresses other than the unspecified address MUST NOT be used as
>the source address in IPv6 packets that are sent outside of a single node. An
>IPv6 packet with a destination address of loopback address MUST NOT be sent
>outside of a single node and must never be forwarded by an IPv6 router. A
>packet received on an interface with a destination loopback address MUST be
>dropped.

((Geoff: I have gone for proposing a simple prefix that sits below the
IPv4-mapped address block of 0:0:0:0:0:FFFF::/96 - the complication is that the
prefix then includes the "unspecified address" as well, so the RFC4291 text
relating to the unspecificed address is reproduced in this proposed amendment,
as this text proposes replacing the entirety of sections 2.5.2 and 2.5.3 of
RFC4291.))

((Geoff: David Farmer has proposed adding additional text noting that this
proposed address designation clashes with the now deprecated IPv4-Compatible
IPv6 Address designation in section 2.5.5.1. It is noted that this was
deprecated in RFC4291 twenty years ago and I'm proposing no further mention of
this deprecated historic address designation. David  suggests that this old
designation should be explicity noted in this text.))

# Security Considerations

IPv6 addressing documents do not have any direct impact on Internet
infrastructure security.

((WK: I don't think that we need to add anything about the "Unspecified
Address", nor the behavior of "packets with source address of ::" since this is
already covered in RFC 4291, but figured I'd mention it here for
completeness.))

# IANA Considerations

The IANA is requested to amend the IPv6 Address registry and the IPv6 Special
Purpose Address registry to record the designation of the IPv6 address prefix
::/96 as denoting the IPV6 Loopback function.

The IANA is also requested to add an entry to the IPv6 Locally-Served DNS Zone
Registry for the entry 0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.IP6.ARPA

--- back

# Acknowledgments
{:numbered="false"}

The authors would like to thank Alejandro Acosta, Brian Carpenter, Antonis
Chariton, Owen DeLong, Gert Doering, Jeremy Duncan, David Farmer, Steinar Haug,
Gábor Lencse, Terry Sweetser, Ole Trøan, and Maciej Żenczykowski for their
comments, discussions, and suggestions on this topic.

We would also like to thank Mark Smith for an earlier (2013) effort:
draft-smith-v6ops-larger-ipv6-loopback-prefix-04, which proposed a /32
designation.

The need for a loopback address prefix has long been a topic of discussion in
various forums, and we would like to acknowledge the contributions of many
individuals who have participated in these discussions over the years.
Unfortunately, at least one of the authors has a terrible memory, and has lost
track of all those who have contributed to this topic over the years, and will
be more than happy to acknowledge their input if reminded of this :-)
