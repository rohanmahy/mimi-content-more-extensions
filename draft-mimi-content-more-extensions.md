---
title: "Additional Extensions for the More Instant Messaging Interoperablility (MIMI) Content Format"
abbrev: "More MIMI Content Extensions"
category: info

docname: draft-mimi-content-more-extensions-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
# area: AREA
# workgroup: WG Working Group
keyword:
 - MIMI content extensions
 - lastSeen
 - sender timestamp
 - outer message ID
venue:
#  group: WG
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG
  github: "rohanmahy/mimi-content-more-extensions"
  latest: "https://rohanmahy.github.io/mimi-content-more-extensions/draft-mimi-content-more-extensions.html"

author:
 -
    fullname: Rohan Mahy
    organization:
    email: rohan.ietf@gmail.com

normative:

informative:

...

--- abstract

This document defines some new useful extensions for the MIMI content format.


--- middle

# Introduction

This document defines a set of new fields for the MIMI content {{?I-D.ietf-mimi-content}} format extensions map.
These particular extensions were chosen to provide functionality already in some other messaging systems, so that they can carry this information safely with the MIMI content format.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Sender Timestamp Extension {#senderTimestamp}

This extension represents the sender asserted sending timestamp.
It is a map containing the whole number of seconds since the start of the UNIX epoch, and optionally the number of additional milliseconds, microseconds, or nanoseconds.

~~~ cbor-diag
/sender_timestamp/  3: {
    /seconds since UNIX epoch/  1: 1762760377,
    /microseconds/             -6: 462917
}
~~~

The Concise Data Definition Language (CDDL) {{!RFC8610}} description of the extension is below.

~~~ cddl
$$otherKnownExtensions //= (
  &(senderTimestamp: TBD1) ^ => {
    1: uint,
    ? fractional
  }
)
fractional = ( -3: millisecs // -6: microsecs // -9: nanosecs)
millisecs = 0..999
microsecs = 0..999999
nanosecs  = 0..999999999

TBD1 = 3  ; recommended value for IANA
~~~


# External Message ID Extension {#externalMessageId}

While the MIMI content format has its own message ID derived from a hash of its content, many messaging systems already have a native message ID format.
This extension allows this "external" message ID to also be included in the MIMI content extensions map.

It consists of an array of two parts, a byte string for the native message ID, and a scope.
The scope could be be an Internet domain name (represented as a text string), a tagged URI, or an IANA-registered Private Enterprise Number (PEN), which is a positive integer.
The PEN option allows for a concise, but unambiguous scope in many organizations.

~~~ cbor-diag
4: [h'08bbeeb8175c4a64a8926a5a23bb2811', 311]
~~~

The CDDL description is below.

~~~ cddl
$$otherKnownExtensions //= (
  &(externalMessageId: TBD2) ^ => ExtMessageId
)
ExtMessageId = [ bstr, scope ]
scope = pen / domain / uri
pen = uint .gt 0    ; IANA Private Enterprise Number
domain = tstr       ; Internet domain name

TBD2 = 4
~~~


# Subject Extension {#subject}

The subject extension allows messaging systems which already have a Subject field to carry a text string of up to 4096 octets of legal UTF-8.

~~~ cbor-diag
5: "This space intentionally left blank"
~~~

The CDDL description is below.

~~~ cddl
$$otherKnownExtensions //= (
  &(subject: TBD3) ^ => tstr .size (1..4096)
)

TBD3 = 5
~~~


# Message Ordering via lastSeen {#lastSeen}

The `lastSeen` data field indicates the latest message(s) the sender was aware of in the group.
It is a list of MIMI message IDs, or a list of external message IDs.

If the sender recently joined the group and has not yet seen any messages,
the list is empty.

If the sender identifies a single message as unambiguously the latest
message in the group, the `lastSeen` list contains a single message id
from that message.

Imagine however that two users (Bob and Cathy) see a message from Alice offering free Hawaiian pizza, and reply at the same time.
Bob and Cathy both send messages with their `lastSeen` including a single message id (Alice's message about pizza).
Their messages don't need to be replies or reactions.
Bob might just send a message saying he doesn't like pineapple on pizza.
Now Doug receives all these messages and replies as well.
Doug's message contains a `lastSeen` including the message id list of both Bob's and Cathy's replies, effectively "merging" the order of messages.

The next message after Doug's message contains a `lastSeen` containing only the message id of Doug's message.

The CDDL description is below.

~~~ cddl
$$otherKnownExtensions //= (
  &(lastSeen: TBD4) ^ => [ * MessageId ] / [ * ExtMessageId ]
)

TBD4 = 256
~~~


# Security Considerations

TODO Security


# IANA Considerations

IANA is requested to add the following entries to the MIMI Content Extension Keys registry.

## senderTimestamp MIMI Content Extension Key

The following completed registration template is provided:

- Key: TBD1 (suggested value 3)
- Name: senderTimestamp
- Type: map
- Recommended: N
- Reference: {{senderTimestamp}} of this document

## externalMessageId MIMI Content Extension Key

The following completed registration template is provided:

- Key: TBD2 (suggested value 4)
- Name: externalMessageId
- Type: array of 2 items
- Recommended: N
- Reference: {{externalMessageId}} of this document

## subject MIMI Content Extension Key

The following completed registration template is provided:

- Key: TBD3 (suggested value 5)
- Name: subject
- Type: tstr
- Recommended: N
- Reference: {{subject}} of this document

## lastSeen MIMI Content Extension Key

The following completed registration template is provided:

- Key: TBD4 (suggested value 256)
- Name: lastSeen
- Type: array
- Recommended: N
- Reference: {{lastSeen}} of this document


--- back

# Complete CDDL Schema {#cddl}

~~~~~~~~~~ cddl
{::include ./extensions.cddl}
~~~~~~~~~~
{: #cddl-schema title="A complete CDDL description of the new extensions"}


# Acknowledgments
{:numbered="false"}

TODO acknowledge.
