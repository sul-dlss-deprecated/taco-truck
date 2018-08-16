---
layout: default
---

# Fedora Assessment for TACO Truck

WIP.

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
