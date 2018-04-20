Examples of CoRE resource directory
===================================

All based on [draft-ietf-core-resource-directory-13] and
[draft-silverajan-core-coap-protocol-negotiation-08].

RD1: Discovery
--------------

Assuming multicast-based discovery here; typical alternatives are SLAAC
announcements or application-specific configuration.

    Req: GET coap://[ff05::TBD]/.well-known/core?rt=core.rd

    Res: 2.05 Content
    </rd>;rt="core.rd";ct=40,

For lookup:

    Req: GET coap://[ff05::TBD]/.well-known/core?rt=core.rd-lookup-res

    Res: 2.05 Content
    </rd-lookup/res>;rt="core.rd-lookup-res";ct=40

Discovery for group registration and endpoints / group lookup is analogous.

All paths announced by server (both here and later in Location)
are up to the server implementation.

RD2: Registration
-----------------

    Req: POST coap://[2001:db8::1]/rd?ep=arduino0815
                    &lt=3600
                    &at=coap+tcp://[2001:db8::815]
                    &some-arbitrary-additional=purple
    Payload (in application/link-format):
    </core-temp>;rt="some.temperature.inner";if="core.s",
    </ambient-temp>;rt="some.temperature.outer";if="core.s",
    </flashlight>;rt="some.illumination";if="core.a"

    Res: 2.01 Created
    Location-Path: /reg/arduino0815


RD3: Registration renewal
-------------------------

    Req: POST coap://[2001:db8::1]/reg/arduino0815

    Res: 2.04 Changed

Features not covered here
-------------------------

* Simple registration (for limited clients)


[draft-ietf-core-resource-directory-13]: https://tools.ietf.org/html/draft-ietf-core-resource-directory-13
[draft-silverajan-core-coap-protocol-negotiation-08]: https://tools.ietf.org/html/draft-silverajan-core-coap-protocol-negotiation-08

Yet to be integrated (Christian's scratch space)
------------------------------------------------

for presentation: 
* use term "discoverable information"

* we're largely modelling alternative addresses as host attributes (like your "Message Protocol" / mpro), don't know use cases for per-resource. can OCF describe use cases?
* if i read Core 1.3.1 Section 11.3.3 right, some clients do update discoverable information. is that so, and does it happen through your RD as well?
* differences in lookup: "multicast to select, then query" vs "multicast query"

how to compare?
* mechanical differences -- ttl in data vs. lt in query parameter at registration
* to-be-discovered (device id (di) vs. additional ocf:// base URI, would that work)?
* conceptual differences ("web links" data model vs. complex data like eps)
