---
layout: default
---

# TACO Prototype (Spring 2018)

In Spring 2018, we held a time-boxed work cycle to create a prototype of TACO plus some related components. This was primarily to test our presumptions so far in designing what became [COCINA](COCINA.html) based on the information gathered in the early phases of [TACO Truck](TACO-Truck.html).

## Goals

With this somewhat greenfield project, we tried to encapsulate what goals we had. Some of this information gets reported out in TACO Truck, in particular aspects of [Hyrax and Fedora assessments with TACO work](TACO-Truck.html#community-overlaps).

Functional Goals | Technological Goals | Process Goals
---------------- | ------------------- | --------------
Deposit resources (binaries & metadata) into repository via API. | Drive forward Department API specifications, implementations, & practices. | Work towards new core with something visible to limited stakeholders to make it real-er.
Retrieve deposited resources from repository via API. | Test implementation options for our current SDR3 design. | Get feedback on SDR3 design, & check for roadblocks.
Persist resources. | Vet our data models & metadata profiles. | Keep to high-level, extensible functional blocks.
Perform skeletal resource processing (i.e. workflows). | Test feasibility of possible technologies: Hyrax integration points; Test throughput / scalability; Inform cloud practices.; Cloud first but Cloud neutral.  | Showcase internal / lower stack rewrites needed for moving middle and end-user codebases forward.


## Codebases

Here are the codebases & related work done as part of this prototype:

* TACO: https://github.com/sul-dlss-labs/taco
* Identifier Service (skeleton): https://github.com/sul-dlss-labs/identifier-service
* Permissions Service (skeleton): https://github.com/sul-dlss-labs/permissions-service
* Early Refritos Prototype (no longer kept)
* COCINA Data Models & Application Profiles: https://sul-dlss.github.io/cocina-models/
* Dorilocos (benchmarking library): https://github.com/sul-dlss-labs/dorilocos
* TACO Terraform to AWS work (private repository): https://github.com/sul-dlss/terraform-aws/blob/master/providers/demo/us-east-1/container_definitions/taco.json

Some details on how we approached this work and what we learned are below. Otherwise, the TACO Github Repository has a ton of developer documentation, in order to capture what we learned or found issues with while work, and some information on how to spin this up if you want to try out TACO.

## API Specifications

TACO API [Specification](https://github.com/sul-dlss-labs/taco/blob/master/swagger.json) and [documentation](https://sul-dlss-labs.github.io/taco/) are publicly available.

We started an [Identifier Service](https://github.com/sul-dlss-labs/identifier-service/blob/master/swagger.json) & [Permissions Service API](https://github.com/sul-dlss-labs/permissions-service/blob/master/swagger.json) specifications as well. These are skeletons at present.

## Technology Selections

*Go language*

* Ability to be modular, with APIs as clean boundaries & work in Cloud (AWS).
* Decision to use compiled language coupled Docker for deployment.
* Efficient Docker container deployment with small, executable binaries (as opposed to platforms that require an operating system and server).
* Focusing on compilable language for small, efficient services led us to Go language.

*Docker containers*

* Docker containers for sending off the codebase binary.
* Efficient deployment with Go's small, executable binaries.

*Circle-CI*

* CircleCI for Continuous Integration with AWS ECS & Docker due to its use by industry for similar set-ups.

*AWS, in particular ECS, DynamoDB, S3, Kinesis*

* AWS ECS (elastic container service) for running this image.
* Terraform for building out AWS infrastructure
* AWS DynamoDb for metadata persistence for the prototype. Very likely to use RDS in production.
* AWS S3 for binaries for the prototype.

*OpenAPI Specification (formerly, SWAGGER) and go-swagger*

* rapid development and delivery;
* SWAGGER specification support for consistent API to Code translations & share-ability of APIs across languages;

*Early Kinesis - Refritos fail*

* Early design had event driven system for managing resource state & asych, DAG-based processing
* Put too much intelligence into TACO
* Kinesis deemed not suitable
* Re-designing to use Kafka-inspired event system within our Provenance & State Service
* Our asychronous, DAG-driven processing inspired by [Airflow](https://airflow.apache.org/) becomes parallel to TACO Truck


## Diagrams & Other information

An early TACO sequence diagrams:

![](assets/img/TACOsequence1.svg)


## To be added here:
* AWS Architecture diagram
* updated information on development remaining
* docs from taquito workshop on how to set up and run TACO, FLAN, Identifier Service, etc.
