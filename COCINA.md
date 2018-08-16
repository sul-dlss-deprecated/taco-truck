---
layout: default
---

# COCINA

COCINA is the conceptual architecture emerging from TACO Truck. The architecture is heavily influenced by Stanford Libraries' needs to move our digital repository forward in a piecemeal and scalable fashion. COCINA has the primary goals of being:

* API- & Data-centric enabling flexibility & extensibility of components
* Profile- & Specification-driven technology
* Make the data model complex, not the code components
* Smaller, less complex & less coupled components
* Easier for users from devs to sysadmins to admins to grok

## COCINA High Level Overview & Components

![](https://docs.google.com/drawings/d/e/2PACX-1vQg_8k2fnPhNE7pHKzrFO9jQ1L_Vy00zReRTiQIuIwo5-utYEf2_u-wVUeA_0gn9TslvJ_DvzBlvK7-/pub?w=2414&h=918)

* **Pre-Assembly** - This is a current state component that is a bunch of Ruby scripts for batch, mediated deposit, i.e. prepare batches of resources (metadata and digital assets) for ingest, then kick off that ingest process. Going forward, we'd like this to be built out as a service (perhaps the same as Deposit, underlying then self-deposit functions) that can leverage calls to SOPA.
* **Deposit** - The seam needed between self deposit (and possibly curated, see above) and SOPA. It may become just self-deposit applications calling SOPA; we've yet to have functional requirements surface that need the separation.
* **SOPA (Administration Service)** - This is a service that basically maps shared digital repository lifecycle actions - register, edit, accession, the variations on delete - and data analytics functions - retrieve, query - to calls to TACO or FLAN. It holds the heart of our complex business logic, like information about how to take a full resource and perform recursion to TACO, how to identify if a full collection or object has been saved to TACO and release the state to FLAN that will kick off Preservation and Access hand-offs, and how to return information (administrative and functional) about resources in TACO for management purposes. This could become a library instead of a separate service, embedded in each deposit or administrative level application that needs it. Uncertain at the moment. It is the backbone also of the future of our administration web application (currently, Argo), and the attempt to separate uses of indices for querying needs from uses of indices for notification needs.
* **[TACO (Management Service)](TACO-Prototype.html)** - This is an API that can take explicit resources (singleton metadata following our JSON MAPs or digital assets) and load them into our separate metadata and binary stores. It is intentionally dumb, to not marry any higher-level business logic to our persistence layer selections. Every TACO action emits a history event to FLAN (the Provenance and State Service).
* **[Identifier Service](TACO-Prototype.html#identifier-service)** - A simple service with REST API that takes a request for a DRUID, then returns one, and stores information on what DRUIDs have been minted already. May be extended in the future to mint other identifiers such as DOIs.
* **[FLAN (Provenance & State Service)](TACO-Prototype.html#flan-service)** - This is a REST API that takes events from TACO and SOPA then appends them to a unified, high-throughput, low-latency message log (of sorts). From that log, we generate various data representations for exposure via API or streams:
  * History: given a DRUID or UUID, return a full history of this resource in TACO & SOPA
  * Provenance (big P): given a DRUID or UUID, return the PREMIS-influenced Provenance for the resource.
  * Status: given a DRUID or UUID, return the status of the resource for processing or persistence actions (see lifecycle statuses below).
  * Status streams: for a given status (ready for preservation, ready for publication, ready for removal from preservation, ready for removal from publication), produce a stream of resources that fit that need, to be consume by downstream systems that need to act on this.
* **NACHO (Permissions Service)** - A lightweight authorization service. We expect authentication will occur at the web application level via shib (for people), or through network configurations between services and machines, but we need a central place to understand what user (or workgroup) can perform what actions on what resources or types of resources. This service stores those authorization dictionaries (modeled on WebACl, see our metadata & data models below) and exposes a REST API to verify an action as needed. We imagine this service will primarily be used at the Deposit applications level, possibly at SOPA level, but not below.
* **Access Publication Service** - a TBD service that consumes FLAN streams for publication of resources, then acts upon them (moves the appropriate TACO resources to the Access filesystem, or kicks off a refritos workflow to do so after transforming the metadata or generating derivatives).
* **Preservation Handshake Service** - a TBD service that consumes FLAN streams for preservation of resources, then acts upon them (moves the appropriate TACO resources to the Preservation service, or kicks off a refritos workflow to do so after transforming the resource to a MOAB).
* **Refritos** - this is purely a dependency graph-driven, asynchronous processing tool that is not coupled to the repository (i.e. multiple systems or users can use this). After defining a DAG (directed acyclic graph) or workflow, and ensuring it maps to functions that available robots code can perform once called upon by refritos, a user can then trigger that processing to occur on a resource by putting the resource in the appropriate refritos workspace.

## COCINA Data Models & Shapes

![](https://docs.google.com/drawings/d/e/2PACX-1vRMIMJZZYT0U_ntGms36kA020v7r5H3bGfMUUbQY24H7k3jkFOLzi1QnJAmCKiO97b67jN40hubD7CI/pub?w=960&h=720)

## COCINA Conceptual Architecture & APIs

![](https://docs.google.com/drawings/d/e/2PACX-1vRyZ6JdoQygYTssgj6lXj_tjslBvf_a2t_2i6HbFUGnlpORmS_JGBIuleqqts5Dnzjwsd36TPS2q5og/pub?w=2570&h=1140)
