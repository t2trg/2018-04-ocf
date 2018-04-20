Examples of CoRE resource directory
===================================

All based on [draft-ietf-core-resource-directory-13] and
[draft-silverajan-core-coap-protocol-negotiation-08].

RD1: Discovery
--------------

Assuming multicast-based discovery here; typical alternatives are SLAAC
announcements or application-specific configuration.

    Req: GET coap://[MCD1]/.well-known/core?rt=core.rd

    Res: 2.05 Content
    </rd>;rt="core.rd";ct=40,

For lookup:

    Req: GET coap://[MCD1]/.well-known/core?rt=core.rd-lookup-res

    Res: 2.05 Content
    </rd-lookup/res>;rt="core.rd-lookup-res";ct=40

Discovery for group registration and endpoints / group lookup is analogous.


[draft-ietf-core-resource-directory-13]: https://tools.ietf.org/html/draft-ietf-core-resource-directory-13
[draft-silverajan-core-coap-protocol-negotiation-08]: https://tools.ietf.org/html/draft-silverajan-core-coap-protocol-negotiation-08
