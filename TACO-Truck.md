---
layout: default
---

# TACO Truck

Stanford Libraries' prototype and implementation of COCINA is called TACO Truck. This was formerly called SDR3 (standing for the third iteration of the Stanford Digital Repository). We've moved away from the term SDR3 as there won't be a clear cut-over between systems. In fact, TACO Truck emerged from needing something that could be replaced piecemeal, and the implementation is part maintenance of existing components we know we'll be keeping, part prototype of the components we know we need to replace.

### Stanford's Requirements

See a backlog of tickets generated here: https://waffle.io/sul-dlss/taco-truck

Our rough, first pass of requirements were gathered here: https://docs.google.com/document/d/186XkuL_gpd6mhCss9-PC8LofqSNuD7E-Q24wBZIVb4Y/edit

### TACO, SOPA, FLAN, NACHOS, Identifier Service & Refritos Prototypes

In Winter-Spring 2018, we have a time-boxed work cycle to create a prototype of TACO.

* https://github.com/sul-dlss-labs/taco
* https://github.com/sul-dlss-labs/identifier-service
* https://github.com/sul-dlss-labs/sdr3-models

### Community OSS Overlaps

**Fedora Review**

* **Incompleteness:** F4? F5? API is incomplete; unclear about Fixity & Notifications; community guidance uncertainty.
* **Complexity & Comprehensibility:** Fedora API is very complex; encompasses LDP, WebACL, Memento.
* **Graph Store Limitations:** Need record management, not just statement management. See Europeana Case.
* **Performance & Extensibility:** Number of calls required for building a resource is higher with LDP & Fedora API.
  * LDP: Goes beyond using RDF to publishing Linked Data. We are not publishing at the TACO level.
  * WebACL & Complex Permission Handling: SDR3 has separate Permission Service for multiple systems to use.
    * Permissions as resource properties (metadata) vs WebACL requests - consider for use cases like Argo.
* **Data & Resource Handling:** JSON-LD support; Do not want quad store or split graphs, but need record management.
  * Resource Ordering is already hard in RDF; more so with LDP & Fedora API.
  * TACO semantically validates our complex data shapes; Fedora API does not do this flexibly.
  * No query, filtering, constraint support. See SDR3 use cases requiring this (ex: de-duplicate sourceIDs).
* **System Expectations:** Monoliths? Need multiple systems with differing contexts (Admin, Process) to work w/TACO.

See our blog posts on our Fedora analysis here: TBD


**Hyrax Review**


**Valkyrie Review**


**PCDM**


**IIIF**


### TACO Truck Roadmap & Migration Plans
