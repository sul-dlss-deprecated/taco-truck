---
layout: default
---

# TACO Truck Glossary
These are the agreed upon definitions used by the TACO Truck Work Teams for terms that can be ambiguous or used in multiple ways around the Stanford Digital Repository (or SDR). This document doesn't negate other definitions for these terms, and should be considered more a working agreement and a subset of the [larger DLSS Glossary](https://consul.stanford.edu/display/DSG/DLSS+Glossary).

* **Assembly, pre-assembly** - tools to create accession-ready objects
* **Accessioning** - Getting stuff into DOR
* **Chimera** - A project name that lead to the Digital Library and SDR2. (retired)
* **Decommission** - remove the File(s) from domain management, and alter the DRO (metadata), but not do delete Object’s File(s) from any preservation sources. DRO’s altered metadata is the trace of existence in management.
    * Reassigned to Graveyard set of visibility, permissions, etc. (i.e. a data profile TBD)
    * Domain identifiers (e.g., druid) that we manage will not be reused.
    * Keep a DRO without Files (i.e. a “citation only object” in SDR2 parlance). Note Files are not destroyed; they still exist in the preserved version of the object just prior to decommissioning.
    * Consider how to inform upstream systems that contribute objects when an object is purged if purge is not initiated via the upstream app.  
* **Digital Library** - SDR + Access environments
* **DOR** - Digital Object Registry. The source of canonical metadata -- all metadata, including descriptive, technical, rights, content, etc. -- for objects in SDR. Uses Fedora 3.
* **DOR Services** - Models and methods for interacting with DOR, including concerns (e.g., editable, indexable, publishable, contentable).
* **Hydra** - The community-sourced (now called Samvera Community) set of Ruby codebases that build a digital library repository solution. This “gobstopper” explanation from our Princeton colleagues of how the various Hydra (now Samvera) codebases relate in terms of scope / functionality helped me grok it: https://docs.google.com/presentation/d/1YiRytI-QY8uvIptqFVksQ3rQ71PsX64OeG_FmQixLMY/edit#slide=id.p and way more information than most care about on the current Hydra architecture decisions in case you’re interested: https://wiki.duraspace.org/display/hydra/Hydra+Stack+-+The+Hierarchy+of+Promises
* **Hydra implementations at Stanford** - we have a lot of codebases that are kind of proto-Hydra (they use Active Fedora, loosely, Rails, then diverge from the Hydra community) or are early Hydra (Hydrus, ETDs) - excluding Access Team work (Blacklight, Spotlight).
* **Hydrus** - Our self-deposit (except for ETDs, which is also proto-hydra) Rails application for the Stanford digital repository. Connects to series of persistence layers (notably, Fedora 3) and other data pipelines within SDR that are beyond the scope of Hydra as understood by the community. The application you interact with at sdr.stanford.edu
* **Hydra-in-a-box (informally, Hybox)** - The name of the overall grant project (not the codebase), funded by IMLS and worked on by Stanford, Duraspace, and DPLA, for getting an instance of the Hydra repository system (see the gobstopper above) in a more shippable form for smaller institutions without the development team support to run a Hydra application stack on their own. http://hydrainabox.samvera.org/
* **Hyku** - An instance of Hyrax, formerly Curation Concerns or Sufia (which is the Hydra community top layer implementation of the gobstopper, see above) built specifically for the Hybox project running with some changes to models, functions, and the support of a AWS-backed infrastructure for running in multi-tenant mode. The community implementation, so to speak, is Hyrax. Hyku is Hybox's implementation (in AWS for multi-tenant mode) of Hyrax.
* **Hyrax** - The top layer of the gobstopper built on top of a Hydra repository stack. A rails application for top level changes, much like you included in your email. Used to be Curation Concerns, and before that Sufia (for sake of understanding helpful if legacy docs).
* **Hydrox** - The SUL DLSS implementation of Hyrax probably but not determined to use AWS and will replace Hydrus (the self deposit application). Doesn’t answer questions about the rest of the stack outside of Hydrus to Fedora 4. An Hydrox Object is, generically, a library digital repository object (metadata plus binaries) - whatever it is you are adding, curating, preserving, managing. These objects are described in RDF using PCDM (Portland Common Data Model).
* **Publication / Publishing** - Moving DOR stuff to PURL and Stacks for downstream consumption.
* **Purge** - remove entirely from all management components and preservation (In SDR2 only implemented for things that haven’t gone to Preservation Core).
    * Domain identifiers (e.g., druid, identifiers that we manage) will not be reused.
    * Action requires legal justification and high level approval.
    * No trace of object left, even though it might create broken links for discovery / publication mechanisms.
* **PURL** - Persistent URL. The online location where each SDR collection and item object is published, and displays associated metadata and accessible content files through a viewer or file list. Formated as: https://purl.stanford.edu/[druid].
* **PURL** - is also the name of the code responsible presenting published resources at the PURL link
* **Registered** - An object to be (has a DRUID from SURI but no representation in F3).
* **Release (for discovery)** - indexing into Searchworks. Not the same as embargo release
* **Samvera** - See Hydra. The name changed from Hydra to Samvera due to a conflict when trying to trademark the original name Hydra.
* **SDR** - Anything involved with the digital repository.
* **SDR1** - The initial buildout of SDR, replaced by SDR2 in 2009/10
    * “Homegrown Java, still has some stuff in it”
    * METS-based
    * Preservation-oriented
* **SDR2** - The current buildout of SDR (see D-Lib article)
    * Includes: DOR, DOR Services, Argo, Hydrus, Preservation Core, Accessioning workflows, robots, PURL, Stacks, purl-fetcher, sw-indexer, discovery-dispatcher, SURI, Revs?
    * Excludes: Spotlight, SearchWorks, EarthWorks, Bassi-Veratti, French Revolutionary Digital Archive
    * Feeders? (the borders are fuzzy here): ETDs, Goobi workflows, maybe Hydrox?
* **SDR3** - That’s what we’re working on. Now called TACO Truck.
* **SDR Preservation Core** - Where objects go to be preserved, dependent on a notion of versioning
* **Stacks (or Digital Stacks)** - the file system where “shelved” files are stored and accessed by PURL and object viewers.
* **Shelving** - Pushing content out to the Stacks
* **Valkyrie** - A Samvera (Hydra) Community Labs Project that attempts to rewrite parts of the Hydra stack (gobstopper) to make, among other changes, the persistence layers and data modeling around persistence of object configurable beyond connecting just to Fedora via ActiveFedora, what is currently presumed and heavily wired into Hyrax / all previous and current Hydra stacks.
