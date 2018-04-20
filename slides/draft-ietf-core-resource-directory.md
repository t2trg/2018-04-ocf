Examples of CoRE resource directory
===================================

All based on [draft-ietf-core-resource-directory-13] ("RD") and
[draft-silverajan-core-coap-protocol-negotiation-08] ("PN").

RD1: Discovery
--------------

Assuming multicast-based discovery here; typical alternatives are SLAAC
announcements or application-specific configuration.

    Req: GET coap://[ff05::TBD]/.well-known/core?rt=core.rd

    Res: 2.05 Content
    </rd>;rt="core.rd"

For lookup:

    Req: GET coap://[ff05::TBD]/.well-known/core?rt=core.rd-lookup-res

    Res: 2.05 Content
    </rd-lookup/res>;rt="core.rd-lookup-res";ct="40 64 504";obs

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

The discoverable information is expressed in web link format;
that is the core of RD's data model.

RD3: Registration renewal
-------------------------

    Req: POST coap://[2001:db8::1]/reg/arduino0815

    Res: 2.04 Changed

Notifying the server of changes in discoverable information can be done by re-registering;
more granular methods are pending.

Alternative addresses can be updated at any time.

RD4: Querying
-------------

Client gets fully combined addresses from an RD that can do
protocol-negotiation:

    Req: GET coap://[2001:db8::1]/rd-lookup/res?ep=arduino0815
                    &tt=tcp&if=core.a

    Res: 2.05 Content
    Payload (in application/link-format):
    <coap+tcp://[2001:db8::815]/flashlight>;rt="some.illumination";
    if="core.a";anchor="coap+tcp://[2001:db8::815]"

Observation is supported: Client can be informed when a suitable resource
appears.

RD5: Querying for endpoints
---------------------------

    Req: GET coap://[2001:db8::1]/rd-lookup/ep?some-arbitrary-additional=purple

    Res: 2.05 Content
    Payload (in application/link-format):
    </reg/arduino0815>;con="coap://[2001:db8::815]";at="coap+tcp://[2001:db8::815]";
    some-arbitrary-additional="purple";lt=3600


Features not covered here
-------------------------

* Other serializations can be supported by the RD
* Registrations can be deleted when EP shuts down
* Groups of endpoints
* Simple registration (for limited clients)
* Management tools can do registrations too
* Some of the published information can be exported to DNS, details are WIP
* Protocol negotiation supports individual alternative links per resource too


[draft-ietf-core-resource-directory-13]: https://tools.ietf.org/html/draft-ietf-core-resource-directory-13
[draft-silverajan-core-coap-protocol-negotiation-08]: https://tools.ietf.org/html/draft-silverajan-core-coap-protocol-negotiation-08

---

Christian's notes for the "finding common grounds and synergies" part
---------------------------------------------------------------------

### Open questions

* RD and PN are largely modelling alternative addresses as host attributes (probably a bit like OCF's "Message Protocol" / "mpro"), but I'm unsure of use cases for per-resource granularity. Can OCF describe some?
* If I read Core 1.3.1 Section 11.3.3 right, some clients do update discoverable information of a server. Is that so, and would it happen through RDs as well?


### Comparing the approaches

* Mechanical differences
  * "ttl" in payload vs. "lt" in query parameter (at registration)
* unsure or to-be-discovered:
  * Device ID (di) vs. additional at="ocf://uuid", would that suffice? (Could query `?at="ocf://uuid"` instead of `?ep=arduino0815`).
  * Differences in lookup: "Multicast to select, then query" vs "Multicast query"
* Conceptual differences ("web links" data model vs. complex data like eps)
