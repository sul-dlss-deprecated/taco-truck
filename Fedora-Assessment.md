---
layout: default
menus:
  header:
    title: Fedora Assessment
    weight: 2
---

# Fedora Assessment for TACO Truck

*We are actively writing up a blogpost that is a better representation, with more detail, of the information below. That will be linked here when it is published.*

When restarting SDR3, which became TACO Truck, we had just spent a number of years working on projects that we had thought would be able to fill in some of the changes and gaps we wanted for moving our digital repository forward. The main two we had expected to use were Fedora 4 (the implementation) and [Hyrax](Hyrax-Assessment.html). However, as we revisited [our baseline functional requirements](TACO-Truck.html#baseline-functional-requirements), and [built a cross-department shared understanding of what are current system does](TACO-Truck.html#sdr-current-state), we had new information and eyes to revisit these projects and evaluate their utility for our needs and system.

As part of this, in early 2018, we evaluated Fedora 4 (the implementation) as well as the about-to-be-released Fedora API specification. We had started our [TACO Prototype by this point](TACO-Prototype.html) (uncertain yet if it would be viable), and used that as a point of comparison. Here is what we found.

Three notes before diving in:
* Use our [Glossary](Glossary.html) for any uncertainty on the parade of acronyms and names about to be used;
* This was performed nearly 6 months ago, so there may be updates or changes to Fedora and [COCINA](COCINA.html) not reflected (yet) in these findings.
* This is an evaluation based only on our baseline functional requirements as an institution with a large digital repository supporting many, many different needs and user groups.

## What did we evaluate

### Fedora API Specification

When referring to the [Fedora API](https://fedora.info/2017/11/27/spec/), we mean this Fedora API Specification. As part of that specification, we’re particularly interested in this quote from the linked document:

> “This specification refines the semantics and interaction patterns of LDP in order to better serve the specific needs of those interested in implementing repositories for durable access to digital data. … this specification contains:
 * a reconciliation of LDP and the version identification and navigation scheme delineated in the Memento specification [RFC7089],
 * integration with the Web Access Control proposal [SOLIDWEBAC],
 * a design for the publication of asynchronous events emitted from an implementation, and
 * interaction patterns for use with binary fixity information.
The goal of this specification is to define the behaviors of Fedora server implementations in order to facilitate interoperability with client applications…”

You'll note in that quote that three other specifications (LDP, SolidWEBAC, and Memento) are mentioned. They are highlighted below.

### Fedora Implementations

When referring to the Fedora implementation, we mean the first implementation on this list and the thing usually meant by the term "Fedora 4". However, we have done some experimentation and evaluation, less involved, of the other solutions here. This assessment focused first on the Fedora API, however, seeing that as the proposed path forward for the Fedora implementations, hence, anything that didn't work for us about the API mostly likely indicated something that wasn't ideal about the implementations.

* [Fedora 4 Implementation (aka “Fedora 4”)](https://github.com/fcrepo4/fcrepo4)
* [Trellis (does not follow the full Fedora API specification)](https://github.com/trellis-ldp/trellis)
    * Views FedoraResources as a stream of operations stored as commit log.
    * Everything is asynchronous.
    * No server managed triples. All HTTP headers.
    * LDP resource types are not persisted as triples.
    * Audit will be done with a header, to request the audit triples for a resource.
    * Versioning is automatic, and you don't create or delete them. Still Memento.
    * No referential integrity constraints
    * No tombstones
    * Users/agents must be described with URIs
* [Lambdora (incomplete)](https://github.com/duraspace/lambdora)
* [Cavendish (incomplete)](https://github.com/cavendish-ldp/cavendish)

### Linked Data Platform

As part of evaluating Fedora (API or implementation), we had to revisit and evaluate a W3C Recommendation - Linked Data Platform. LDP, as seen in the Fedora API specification above, is tightly coupled to Fedora's approach to back end management of resources. To wit, from the [Fedora API](https://fedora.info/2017/11/27/spec/) Specification:

> “A conforming Fedora server is a conforming [LDP] 1.0 server that follows the additional rules defined in sections
3. Resource Management, [LDP]
4. Resource Versioning, [Memento]
5. Resource Authorization, [WebACL] and
6. Notifications of this specification [LDN, ActivityVocab].”

To better understand what this means, here is an introductory quote from the [Linked Data Platform specification](https://www.w3.org/TR/ldp/):

> “Linked Data Platform (LDP) defines a set of rules for HTTP operations on web resources, some based on RDF, to provide an architecture for read-write Linked Data on the web.
…
This specification discusses standard HTTP and RDF techniques used when constructing clients and servers that create, read, and write Linked Data Platform Resources.” (Linked Data Protocol)

### Memento Specification

As part of evaluating Fedora (API or implementation), we also revisited and evaluated the [Memento specification](https://tools.ietf.org/html/rfc7089). Memento, liked LDP and as seen in the Fedora API specification quote above, is tightly coupled to Fedora's approach to resource versioning. To understand the Memento spec, we highlight this quote:

> “In the abstract, the Memento framework introduces a mechanism to access versions of Web resources that:
* Is fully distributed in the sense that resource versions may reside on multiple servers, and that any such server is likely only aware of the versions it holds;
* Uses the global notion of datetime as a resource version indicator and access key;
* Leverages the following primitives of [W3C.REC-aww-20041215]: resource, resource state, representation, content negotiation, and link.”

### WebACL Specification

As part of evaluating Fedora (API or implementation), we finally also included the [Solid WebACL Specification](https://github.com/solid/web-access-control-spec). Memento, liked LDP and as seen in the Fedora API specification quote above, is tightly coupled to Fedora's approach to resource versioning. To understand the Memento spec, we highlight these two quotes:

> “Web Access Control (WAC) is a decentralized cross-domain access control system. … It's concerned with giving access to agents (users, groups and more) to perform various kinds of operations (read, write, append, etc) on resources. WAC has several key features:
* The resources are identified by URLs, and can refer to any web documents or resources.
* It is declarative -- access control policies live in regular web documents, which can be exported/backed easily, using the same mechanism as you would for backing up the rest of your data.
* Users and groups are also identified by URLs (specifically, by WebIDs)
* It is cross-domain -- all of its components, such as resources, agent WebIDs, and even the documents containing the access control policies, can potentially reside on separate domains. …”

[Solid WebACL Specification](https://github.com/solid/web-access-control-spec); See also [WebACL (web access control list)](https://www.w3.org/wiki/WebAccessControl).  

> “In a system that uses Web Access Control, each web resource has a set of Authorization statements describing:
* Who has access to that resource (that is, who the authorized agents are)
* What types (or modes) of access they have
* These Authorizations are either explicitly set for an individual resource, or (more often) inherited from that resource's parent folder or container. In either case, the Authorization statements are placed into separate WAC documents called Access Control List Resources (or simply ACLs).”

[Solid WebACL Specification](https://github.com/solid/web-access-control-spec); See also [WebACL (web access control list)](https://www.w3.org/wiki/WebAccessControl).

## Questions & Findings

To evaluate the above in the context of the TACO Truck requirements we've gathered and mapped to [TACO](TACO-Prototype.md), we asked (and attempted to answer) the following questions:

### Why not use Fedora API in place of TACO API?

* **Incompleteness:** F4? F5? API is incomplete; unclear about Fixity & Notifications; community guidance uncertainty.
* **Complexity & Comprehensibility:** Fedora API is very complex; encompasses LDP, WebACL, Memento.
* **Graph Store Limitations:** Need record management, not just statement management. See Europeana Case.
* **Performance & Extensibility:** Number of calls required for building a resource is higher with LDP & Fedora API.
  * LDP: Goes beyond using RDF to publishing Linked Data. We are not publishing at the TACO level. See note below on Linked Data vs RDF.
  * WebACL & Complex Permission Handling: SDR3 has separate Permission Service for multiple systems to use.
    * Permissions as resource properties (metadata) vs WebACL requests - consider for use cases like Argo.
* **Data & Resource Handling:** JSON-LD support; Do not want quad store or split graphs, but need record management.
  * Resource Ordering is already hard in RDF; more so with LDP & Fedora API.
  * TACO semantically validates our complex data shapes; Fedora API does not do this flexibly.
  * No query, filtering, constraint support. See SDR3 use cases requiring this (ex: de-duplicate sourceIDs).
* **System Expectations:** Monoliths? Need multiple systems with differing contexts (Admin, Process) to work w/TACO.

### What about Linked Data?
* We are happy to use RDF in our data modeling internally (hence the JSON-LD usage within TACO Truck), but we don't need Linked Data or Semantic Web technologies internally. The RDF modeling is both for data model extensibility internally and later publication handled outside of our management sphere. Coupling Linked Data publication to our internal model introduced complexities and speed concerns for little gain on our requirements.
  * TACO MAPs already use RDF expectant modeling.
  * TACO is not about publishing data, but managing resources. This goes against the tenants of LDP.
* We're using JSON-LD & JSON-Schema
  * We are using JSON in order to support record management.
  * We are using JSON-LD to support Linked Data... on the publication side of the repository system.
  * Let’s learn from IIIF & Princeton work on related issues and modeling approaches.
* We don’t want nor need N-Triples or SPARQL in TACO (required by Fedora API)
  * Inference, SPARQL, other can be in a sidecar analytics space if/as needed, when the requirement arises.

### Why not use Fedora API in tandem with TACO API? Can we use a subset of the API? Can we use Fedora API as our base & extend it? Can we use the Fedora API elsewhere in SDR3?

* We want to keep clear boundaries & APIs
  * Fedora API combines publication concerns (i.e. Linked Data) with data management concerns (i.e. RDF)
  * We would cut out a lot of the core of Fedora API for our needs now (i.e. uncoupling Fedora from LDP, Memento, WebACL)
* Evolution
  * Perhaps our work can go back into or help the Fedora API work in a year or so, if TACO works for us
* As TACO Truck moves towards resource & data publication, we can revisit the above in the context of those requirements.

### Where does this leave us with reuse of community tools that expect Fedora API-compliant layers?
* We already have explicit & consistent mappings & data expectations for exchange, so we’re not bound to TACO forever.
* We can already share data to systems with any sort of space for an interface / API.
* We’re making it easier to reuse community components by expecting interfaces / extensibility.

### What about Community Overlap? Whither Samvera?
* Flexibility & Freedom of APIs:
* Have explicit & consistent expectations; we’re not bound to TACO forever.
* Easier to reuse community components due to these data APIs.
* Can work with communities sharing our data & systems needs like DPLA.
  * Shared Models:
  * Using PCDM. ORE. RDF.
  * Can support BIBFRAME, DPLA MAP, etc.
  * Using & learning from IIIF.
* Improve (Still) Shared Code:
  * Still using Blacklight.
  * Still returning to Hyrax in a year or so as a possible front end (if we can find the appropriate seam to interact with TACO).
  * Can possibly make Hyrax more performant, flexible, comprehensible by untying from morass of various ORM interactions & giving it clearer process steps and boundaries.
  * Valkyrie is an interesting but largely orthogonal (at present) effort to the above.

## TACO API (Prototype) & Fedora API Comparisons

### TACO & LDP

TACO | LDP
---- | ----
Containment & membership via fields. | Containment & membership via protocol.
JSON & JSON-LD representation. | N-triples hard requirement.
“Dumb”, single resource actions. | Single & Iterative resource actions.
Managing digital resources internally. | Managing data publication on web.

### TACO Data Model & WebACL

Our data model is heavily influenced by WebACL when it comes to authentication and authorization, but the authentication aspect is largely handled by the web applications using Stanford University IT services via Shibboleth, and TACO Truck authorization is largely handled by those same web apps or update APIs calling a shared Permissions Service. There isn't a need yet to couple that Permissions service tightly to the hand off to SOPA or TACO, especially if that authorization may be occuring upstream. See our data model below with the proposed overlap and component domains:

![](https://docs.google.com/drawings/d/e/2PACX-1vRMIMJZZYT0U_ntGms36kA020v7r5H3bGfMUUbQY24H7k3jkFOLzi1QnJAmCKiO97b67jN40hubD7CI/pub?w=960&h=720)

### TACO API & Fedora API

TACO API | Fedora API
-------- | ----------
“Dumb”, singleton actions. | Recursion Required
Version via User Action | Version via Timestamps
Async Processing Driven. | Async Processing… Kinda.
Containment via fields. | Requires LDP Containers
JSON-LD Record Mgmt. | Server- vs Client-Managed
Sparkles in spirit. |Hard SPARQL Requirement.
