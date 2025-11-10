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

TODO Abstract


--- middle

# Introduction

This document defines ome MIMI content


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Sender Timestamp Extension




~~~ cbor-diag
/sender_timestamp/  3: {
    /seconds since UNIX epoch/  1: 1762760377,
    /microseconds/             -6: 462917
}
~~~


~~~ cddl
sender_timestamp = {
  TBD1: {
    1: uint,
    ? fractional
  }
}

fractional = ( -3: millisecs / -6: microsecs / -9: nanosecs)
millisecs = 0..999
microsecs = 0..999999
nanosecs  = 0..999999999

TBD1 = 3  ; recommended value for IANA
~~~






# External Message ID Extension

Many messaging systems already have a native message ID format.

~~~ cddl
external_message_id = {
  TBD2: ExtMessageId
}

ExtMessageId = [ bstr, scope ]
scope = pen / domain / uri
pen = uint .gt 0 ; IANA Private Enterprise Number
domain = tstr    ; Internet domain name

TBD2 = 4
~~~

# Subject Extension

~~~ cddl
subject = {
  TBD3: tstr .size 1..4096
}

TBD3 = 5
~~~


# Message Ordering via lastSeen

The `lastSeen` data field indicates the latest message the sender was aware of in the group.
It is a list of MIMI message IDs, or a list of external message IDs.

If the sender recently joined the group and has not yet seen any messages,
the list is empty.

If the sender identifies a single message as unambiguously the latest
message in the group, the `lastSeen` list contains a single message id
from that message.

Imagine however that two users (Bob and Cathy) see a message from Alice offering free Hawaiian pizza, and reply at the same time.
Bob and Cathy both send messages with their `lastSeen` including a single message id (Alice's)
message about pizza.  Their messages don't need to be replies or reactions.
Bob might just send a message saying he doesn't like pineapple on pizza.
Now Doug receives all these messages and replies
as well. Doug's message contains a `lastSeen` including the message id
list of both Bob's and Cathy's replies, effectively "merging" the order
of messages.

The next message after Doug's message contains a `lastSeen` containing
only the message id of Doug's message.

~~~ cddl
ordering = {
  TBD4: [ * MessageId ] / [ * ExtMessageId ]
}

TBD4 = 256
~~~


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
