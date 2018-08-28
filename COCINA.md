---
layout: default
menus:
  header:
    title: COCINA
    weight: 1
---

# COCINA

COCINA is the conceptual architecture emerging from [TACO Truck](TACO-Truck.html). The architecture is heavily influenced by Stanford Libraries' needs to move our digital repository forward in a piecemeal and scalable fashion. COCINA has the primary goals of being:

* API- & Data-centric enabling flexibility & extensibility of components;
* Profile- & Specification-driven technology;
* Make the data model complex, not the code components;
* Smaller, less complex & less coupled components;
* Easier for users from devs to sysadmins to admins to grok.

Below are the architectural and data model proposals. To see more on implementation, check out our details of the [TACO Prototype work](TACO-Prototype.html), or keep tabs on the [TACO Truck project page.](TACO-Truck.html)

## COCINA High Level Overview & Components

![](https://docs.google.com/drawings/d/e/2PACX-1vQg_8k2fnPhNE7pHKzrFO9jQ1L_Vy00zReRTiQIuIwo5-utYEf2_u-wVUeA_0gn9TslvJ_DvzBlvK7-/pub?w=2414&h=918)

* **Pre-Assembly** - This is a current state component that is a bunch of Ruby scripts for batch, mediated deposit, i.e. prepare batches of resources (metadata and digital assets) for ingest, then kick off that ingest process. Going forward, we'd like this to be built out as a service (perhaps the same as Deposit, underlying then self-deposit functions) that can leverage calls to SOPA.
* **Deposit** - The seam needed between self deposit (and possibly curated, see above) and SOPA. It may become just self-deposit applications calling SOPA; we've yet to have functional requirements surface that need the separation.
* **SOPA (Administration Service)** - This is a service that basically maps shared digital repository lifecycle actions - register, edit, accession, the variations on delete - and data analytics functions - retrieve, query - to calls to TACO or FLAN. It holds the heart of our complex business logic, like information about how to take a full resource and perform recursion to TACO, how to identify if a full collection or object has been saved to TACO and release the state to FLAN that will kick off Preservation and Access hand-offs, and how to return information (administrative and functional) about resources in TACO for management purposes. This could become a library instead of a separate service, embedded in each deposit or administrative level application that needs it. Uncertain at the moment. It is the backbone also of the future of our administration web application (currently, Argo), and the attempt to separate uses of indices for querying needs from uses of indices for notification needs.
* **[TACO (Management Service)](TACO-Prototype.html)** - This is an API that can take explicit resources (singleton metadata following our JSON MAPs or digital assets) and load them into our separate metadata and binary stores. It is intentionally dumb, to not marry any higher-level business logic to our persistence layer selections. Every TACO action emits a history event to FLAN (the Provenance and State Service).
* **[Identifier Service](TACO-Prototype.html)** - A simple service with REST API that takes a request for a DRUID, then returns one, and stores information on what DRUIDs have been minted already. May be extended in the future to mint other identifiers such as DOIs.
* **[FLAN (Provenance & State Service)](TACO-Prototype.html)** - This is a REST API that takes events from TACO and SOPA then appends them to a unified, high-throughput, low-latency message log (of sorts). From that log, we generate various data representations for exposure via API or streams:
  * History: given a DRUID or UUID, return a full history of this resource in TACO & SOPA
  * Provenance (big P): given a DRUID or UUID, return the PREMIS-influenced Provenance for the resource.
  * Status: given a DRUID or UUID, return the status of the resource for processing or persistence actions (see lifecycle statuses below).
  * Status streams: for a given status (ready for preservation, ready for publication, ready for removal from preservation, ready for removal from publication), produce a stream of resources that fit that need, to be consume by downstream systems that need to act on this.
* **NACHO (Permissions Service)** - A lightweight authorization service. We expect authentication will occur at the web application level via shib (for people), or through network configurations between services and machines, but we need a central place to understand what user (or workgroup) can perform what actions on what resources or types of resources. This service stores those authorization dictionaries (modeled on WebACl, see our metadata & data models below) and exposes a REST API to verify an action as needed. We imagine this service will primarily be used at the Deposit applications level, possibly at SOPA level, but not below.
* **Access Publication Service** - a TBD service that consumes FLAN streams for publication of resources, then acts upon them (moves the appropriate TACO resources to the Access filesystem, or kicks off a refritos workflow to do so after transforming the metadata or generating derivatives).
* **Preservation Handshake Service** - a TBD service that consumes FLAN streams for preservation of resources, then acts upon them (moves the appropriate TACO resources to the Preservation service, or kicks off a refritos workflow to do so after transforming the resource to a MOAB).
* **Refritos** - this is a purely dependency graph-driven, asynchronous processing tool that is not coupled to the repository (i.e. multiple systems or users can use this). After defining a DAG (directed acyclic graph) or workflow, and ensuring it maps to functions that available robots code can perform once called upon by refritos, a user can then trigger that processing to occur on a resource by putting the resource in the appropriate refritos workspace.

## COCINA Data Models & Shapes

One of the SDR2 Retrospective lessons learned we picked up was try to make the services simple and the data model complex but consistent. Currently, we have an issue where our data models vary given the originating system or needs appended to a special processing group. This is in part due to the inflexibility of the Fedora 3 data model we largely built our system around. To repair this, for COCINA, we have proposed using JSON (and nominally, JSON-LD) as our data representation. This allows us to build off of RDF mappings, but have the flexibility to extend our model as needed.

Additionally, through the use of JSON Schema, we are able to have the minimally needed validation for processing needs alone in the repository, as well as marry those expectations to our TACO and SOPA APIs. We've also started to use nested hashes or 'microschemas' within this model to support some of the functionalities we liked about Fedora 3's datastreams - namely, more granular control of groups of fields for given contexts.

Here is an overview of our data model, heavily influenced by PCDM, WebACL, and Web Annotations:

![](https://docs.google.com/drawings/d/e/2PACX-1vRMIMJZZYT0U_ntGms36kA020v7r5H3bGfMUUbQY24H7k3jkFOLzi1QnJAmCKiO97b67jN40hubD7CI/pub?w=960&h=720)

And in this repository, you can see some of the prototype specifications for TACO data models: https://github.com/sul-dlss-labs/sdr3-models.

An important split to recognize here is that we have a JSON MAP (or metadata application profile) for each resource type - Collection, Object, Fileset, File, Agent, etc. This allows us to keep TACO extremely simple and flexible, since it doesn't need to know about "full resources", but only takes one of those at a time for management. SOPA is where our "full" or recursive logic lives. The idea behind this is not just flexibility when interacting with persistence, but also easily scoping of what metadata goes where (i.e. no embedded file metadata on the object level) as well as support for piecemeal updates up and downstream.

Here is our prototype (so not complete!) JSON Schema specification for a Digital Repository Object (or DRO):

```
{
  "$schema": "http://json-schema.org/draft-06/schema#",
  "title": "Digital Repository Object",
  "description": "Domain-defined abstraction of a 'work'. Digital Repository Objects' abstraction is describable for our domain’s purposes, i.e. for management needs within our system.",
  "type": "object",
  "required": ["@context", "@type", "externalIdentifier", "label", "tacoIdentifier", "version", "administrative", "access", "identification", "structural"],
  "properties": {
    "@context": {
      "description": "URI for the JSON-LD context definitions.",
      "type": "string"
    },
    "@type": {
      "description": "The content type of the DRO. Selected from an established set of values.",
      "type": "string",
      "enum": [
        "http://sdr.sul.stanford.edu/models/sdr3-object.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-3d.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-agreement.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-book.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-document.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-geo.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-image.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-page.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-photograph.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-manuscript.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-map.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-media.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-track.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-webarchive-binary.jsonld",
        "http://sdr.sul.stanford.edu/models/sdr3-webarchive-seed.jsonld"
      ]
    },
    "citation": {
      "description": "Citation for the resource, including identifier, label, version, and a persistent URL to the object with SDR at the very least.",
      "type": "string"
    },
    "dedupeIdentifier": {
      "description": "Identifier retrieved from identification.sourceId that stands for analog or source identifier that this resource is a digital representation of.",
      "type": "string"
    },
    "depositor": {
      "description": "The Agent (User, Group, Application, Department, other) that deposited the DRO into SDR.",
      "$ref": "Agent.json"
    },
    "externalIdentifier": {
      "description": "Identifier for the resource within SDR but outside of TACO. DRUID or UUID depending on resource type. Constant across resource versions. What clients will use calling TACO. Same as `identification.identifier`",
      "type": "string"
    },
    "label": {
      "description": "Primary processing label (can be same as title) for a DRO.",
      "type": "string"
    },
    "tacoIdentifier": {
      "description": "Identifier for the resource within TACO. UUID, unique for each new version of a TACO resource.",
      "type": "string"
    },
    "version": {
      "description": "Version for the DRO within SDR.",
      "type": "integer"
    },
    "precedingVersion": {
      "description": "Preceding version for the Object within SDR.",
      "type": "string"
    },
    "followingVersion": {
      "description": "Following version for the Object within SDR.",
      "type": "string"
    },
    "access": {
      "description": "Access Metadata for the DRO.",
      "type": "object",
      "required": ["access", "download"],
      "properties": {
        "access": {
          "description": "Access level for the DRO.",
          "type": "string",
          "enum": ["world", "stanford", "location-based", "citation-only", "dark"]
        },
        "copyright": {
          "description": "The human readable copyright statement that applies to the DRO.",
          "type": "string"
        },
        "download": {
          "description": "Download level for the DRO metadata.",
          "type": "string",
          "enum": ["world", "stanford", "location-based", "citation-only", "dark"]
        },
        "embargoReleaseDate": {
          "description": "Date when the DRO is released from an embargo, if an embargo exists.",
          "type": "string",
          "format": "date-time"
        },
        "license": {
          "description": "The license governing reuse of the DRO. Should be an IRI for known licenses (i.e. CC, RightsStatement.org URI, etc.).",
          "type": "string"
        },
        "reuseAndReproductionStatement": {
          "description": "The human readable reuse and reproduction statement that applies to the DRO.",
          "type": "string"
        },
        "termsOfUse": {
          "description": "License or terms of use governing reuse of the DRO. Should be a text statement.",
          "type": "string"
        },
        "visibility": {
          "description": "Percentage of the DRO that is visibility during an embargo period",
          "type": "integer"
        }
      }
    },
    "administrative": {
      "type": "object",
      "description": "Administrative metadata for the SDR resource.",
      "required": ["created", "isDescribedBy", "sdrPreserve"],
      "properties": {
        "created": {
          "description": "When the resource in SDR was created.",
          "type": "string",
          "format": "date-time"
        },
        "deleted": {
          "description": "If the resource has been deleted (but not purged).",
          "type": "boolean"
        },
        "gravestoneMessage": {
          "description": "Message describing why the object was deleted.",
          "type": "string"
        },
        "isDescribedBy": {
          "description": "Pointer to the PURL/XML file that is a traditional representation of the metadata for the DRO.",
          "type": "string"
        },
        "lastUpdated": {
          "description": "When the resource in SDR was last updated.",
          "type": "string",
          "format": "date-time"
        },
        "partOfProject": {
          "description": "Administrative or Internal project this resource is a part of.",
          "type": "string"
        },
        "sdrPreserve": {
          "description": "If this resource should be sent to Preservation.",
          "type": "boolean"
        },
        "remediatedBy": {
          "description": "The Agent (User, Group, Application, Department, other) that remediated a DRO in SDR.",
          "type": "array",
          "items": {
            "$ref": "Agent.json"
          }
        }
      }
    },
    "identification": {
      "description": "Identifying information for the DRO.",
      "type": "object",
      "required": ["identifier"],
      "properties": {
        "catalogLinks": {
          "description": "Links to catalog records for the resource that the DRO represents in whole or part.",
          "type": "array",
          "items": {
            "type": "object",
            "required": ["catalog", "catalogRecordId"],
            "properties": {
              "catalog": {
                "description": "Catalog that is the source of the linked record.",
                "type": "string"
              },
              "catalogRecordId": {
                "description": "Record identifier that is unique within the context of the linked record's catalog.",
                "type": "string"
              },
              "recordSchema": {
                "description": "Metadata schema of the linked record.",
                "type": "string"
              },
              "deriveMetadata": {
                "description": "If the DRO descriptive metadata should be automatically updated when the linked record changes.",
                "type": "boolean"
              },
              "deliverMetadata": {
                "description": "If the linked record should be automatically updated when the DRO descriptive metadata changes.",
                "type": "boolean"
              },
              "recordScope": {
                "description": "Whether the linked record describes a resource that is broader than, equivalent to, or narrower than the resource the DRO represents.",
                "type": "string",
                "enum": ["broader", "equivalent", "narrower"]
              }
            }
          }
        },
        "doi": {
          "description": "Digital Object Identifier (DOI) for the DRO within this repository.",
          "type": "string"
        },
        "identifier": {
          "description": "Identifier for the Digital Repository Object within the Stanford Digital Repository system",
          "type": "string"
        },
        "sameAs": {
          "description": "Another object, either external or internal to the system (if duplication occurs) that is the same digital resource as this DRO.",
          "type": "string"
        },
        "sourceId": {
          "description": "A source resource or object (perhaps but not necessarily analog or physical) that the DRO is a digital representation of.",
          "type": "string"
        },
        "sdrUUID": {
          "type": "string",
          "description": "UUID previously minted for the resource within SDR2 / Fedora 3."
        }
      }
    },
    "permissions": {
      "description": "Permissions Metadata for the DRO.",
      "type": "object",
      "properties": {
        "approvalRequired": {
          "description": "Indicates if approval is required to deposit or manage the resource in SDR.",
          "type": "boolean"
        },
        "approvers": {
          "description": "Agents who are required to approve deposit or management of this resource in SDR.",
          "type": "array",
          "items": {
            "$ref": "Agent.json"
          }
        }
      }
    },
    "structural": {
      "description": "Structural metadata for the DRO.",
      "type": "object",
      "required": ["hasAgreement"],
      "properties": {
        "contains": {
          "description": "Filesets that contain the digital representations (Files) of the DRO.",
          "type": "array",
          "items": {
            "type": "string"
          }
        },
        "hasAgreement": {
          "description": "Agreement that covers the deposit of the DRO into SDR.",
          "type": "string"
        },
        "hasMember": {
          "description": "Component or 'children' digital repository objects that are a part or portion of this 'parent' or aggregate DRO.",
          "type": "array",
          "items": [{
            "type": "string"
          }]
        },
        "isTargetOf": {
          "description": "An Annotation instance that applies to the DRO.",
          "type": "string"
        },
        "hasMemberOrders": {
          "description": "Provided sequences or orderings of members of the Object, including some metadata about each sequence (i.e. sequence label, sequence type, if the sequence is primary, etc.).",
          "type": "array",
          "items": [{
            "$ref": "Sequence.json"
          }]
        }
      }
    }
  }
}
```
