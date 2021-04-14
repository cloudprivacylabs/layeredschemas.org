---
title : "Layered Schemas for data capture, processing, and exchange"
lead: "Enable semantic processing for data. Improve interoperability between heterogeneous systems."
date: 2020-10-06T08:47:36+00:00
lastmod: 2020-10-06T08:47:36+00:00
draft: false
images: []
boxes: 
#  - title: Data Capture
#    content: |
#      A schema defines the underlying data elements. Interchangeable overlays add the necessary variations for language, formatting, and encoding. 
      
#  - title: Format independence
#    content: |
#      Layered schemas architecture treat all structured data as linked data. 
---

{{<figure src="layered-schemas-flow.png" class="text-center">}}

Layered schema technology enables unified data semantics to improve
interoperability between heterogeneous systems for data capture,
processing, and exchange. It uses a schema to specify the attributes
of a data object and their translations into linked
data. Interchangeable overlays deal with variability due to
differences in data capture conventions, jurisdictions, locale,
vendors, or other factors.

Traditional schemas (such as JSON/XML schemas) describe the structure
of data with limited semantic information. Layered schemas use
open-ended semantic annotations to describe data in addition to the structural
constraints used in traditional schemas. Some elements of a layered
schema are:

 * Constraints: Required attributes, length limits,...
 * Format: Phone number with area code, date/time,...
 * Language: English, Spanish,...
 * Privacy classifications: Personally identifiable information, sensitive information,...
 * Retention policies: Attributes must be cleared after a period,...
 * Provenance: Data source, signatures, ...
 
Layered schema architecture also provides the framework that enables
applications to define additional metadata.

{{<figure src="slice-compose.png" class="text-center">}}

A schema can be "sliced" into a base schema and multiple overlays. The
schema base contains minimal structural information and a unified
terminology for the data. Each **overlay** adds metadata to enrich
descriptions of data elements. These overlays are interchangeable to
account for the variations in constraints, languages, notational
differences, and more. A schema and a set of overlays are composed to
build a new schema variant. An application can process data without
explicit knowledge of the variant by working with semantic annotations
embedded in the data, so variations due to different data sources are
not "hardwired" into the application logic.

Layered schema architecture includes the following components:

  * Schema compiler: The toolset and the
    associated library to import, slice, compose layered schemas, and
    import/export data using them
  * Schema repository: A centralized or decentralized schema storage
    engine that resolves references to schemas based on the context.
  * Form processor: Builds data entry forms from a schema, and
    translates entered data into linked data. The form processor uses an
    ontology of terms to customize how data entry forms are generated.
  * Semantic processor: Helps working with linked data by evaluating
    semantic queries and operations. Applications can extend the
    semantic processor by defining new terms and their operations.

## Data Capture

{{<figure src="layered-schema-data-capture-application.png" class="text-center">}}

Layered schemas allow building data capture applications that unify
semantics by using overlays to implement variations for language,
locale, formatting, and jurisdiction. A data governing organization
develops and publishes schemas and overlays to a schema repository. A
form processor component uses these schemas and overlays to
generate data entry forms by interpreting the annotations included in
the layered schema. This allows the application to display the correct
form variation to an authenticated end user. Once the user enters
data, the form processor converts it into linked data, which is
enriched by the application with user credentials, data provenance
information, and other metadata specific to the application. A
semantic processor enables the application to validate and
transform the entered data. The application can then slice the
resulting linked data representation to build structured data objects
and overlays that contain the metadata.

The use cases for this architecture include data reporting with manual
entry, request collection, research subject recruitment, and
questionnaires.

## Data Processing

{{<figure src="layered-schema-data-processing-application.png" class="text-center">}}

Layered schemas can be used to improve interoperability in data
processing applications. This architecture uses data ingestion
adapters that use layered schemas to validate and import structured
data. While importing, these adapters convert the input data into
linked data as described by the schema, and annotate the data elements
using schema information. The application can then process the
ingested data using the semantic processor. This allows the
application to execute operations such as "find all attributes with
annotation X and mask their content". An export adapter converts the
processed linked data to the desired format, potentially using another
schema.

The use cases for this architecture include data warehousing, ETL
pipelines, privacy-consciuous data processing, and integrations with
multiple sources and/or targets.

## Data Exchange

{{<figure src="layered-schema-data-exchange-application.png" class="text-center">}}

Layered schemas can be used to implement machine-readable data
exchange agreements, policies, and privacy controls for data
exchange. These agreements and policies can be represented using a set
of overlays with an ontology of terms with associated algorithms to
mask, redact, or modify data. User consent information can also be
added using a set of dynamically generated overlays that mask data
elements that are not allowed to be exchanged. The semantic processor
applies these overlays to data to ensure correct and compliant
exchange. Because all these operations are performed by the semantic
processor, the application does not need to perform additional privacy
or compliance controls during data exchange.
