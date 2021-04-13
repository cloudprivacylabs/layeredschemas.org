---
title: "Overview"
weight: 1
menu:
  docs:
    parent: introduction
draft: true
---

Layered Schemas is developed to help machines understand the
underlying meaning of information by embedding semantic annotations
into data. It provides a mechanism to define unified data semantics as
a schema base and to add additional layers to represent variations for
different data sources, contexts, and jurisdictions. These embedded
semantics offer significant advantages to traditional systems where
the meaning is “hardwired” into the processing logic. With traditional
systems, processing logic must be modified when two systems need to
interoperate in a new way or new information needs to be
exchanged. With Layered Schemas, integrating new systems or changing
data models is achieved by simply adding schema variations.

Traditional schemas describe the structure of data with limited
semantic information. A Layered schema slices a traditional schema
into multiple layers with each layer adding both structural and
semantic information. The schema base is the first layer that defines
the structure and the vocabulary used to capture and process data. The
data model can be flat for tabular, linked with other schemas, or
nested to represent more elaborate structures such as health
data. Schema layers add semantic annotations to the base model. These
annotations include:

 * Constraints: Required attributes, length limits,...
 * Format: Phone number with area code, date/time,...
 * Language: English, Spanish,...
 * Privacy classifications: Personally identifiable information, sensitive information,...
 * Retention policies: Attributes must be cleared after a period,...
 * Provenance: Data source, signatures, ... 
 
Layered schemas improve interoperability between different
applications by enabling semantic processing of data coming from
heterogeneous sources. Each data source uses a variation of the schema
to interpret data based on a common vocabulary. Privacy and security
layers mark attributes for encryption, de-identification, and consent
validation during data exchange. Data provenance layers add metadata
to keep track of data source and ownership. For data warehousing and
data pooling operations, layered schema architecture unifies the
vocabulary for the ingested data and adds the necessary semantic
annotations to decouple meaning from the processing logic.

Layered schemas enable auto-generated data entry forms to capture data
using a common vocabulary. These forms can be built in multiple
languages and with different data entry conventions. Since captured
data will include the semantic annotations of the schema layers, data
processing applications do not have to deal with hardwired data
semantics.

## Use Case: Data Capture

In this use case, semantic annotations defined in a layered schema are
used to generate data capture forms to collect data from end
users. One user can fill out the form in one language and another user
can access the same form in another language.

The applications for this use-case include but are not limited
to data reporting with manual entry, data collection, research subject
recruitment, and questionnaires. This collection process accounts for
variations among data collection points without additional effort for
customization while still attaining a unified data vocabulary as a
result.

{{< figure src="../layered-schemas-usecase-data-collection.png" >}}

## Use Case: API Integration

This use-case of layered schemas relies on adapters to collect data
from heterogeneous sources using API or database integrations. The
applications for this use-case include data consolidation, data
warehousing, information exchange hubs, and health information
exchanges. Data captured from multiple sources can be unified using a
common vocabulary and enriched with semantic annotations. These
annotations can vary from data source to data source to add the
necessary metadata to enable correct interpretation of data.

{{< figure src="../layered-schemas-usecase-data-transfer.png" >}}

## Use Case: Data Exchange

In this use case, data exchange between organizations is governed by
agreements that specify what data items will be exchanged, whether or
not certain data elements such as personally identifiable information
or sensitive data can be exchanged, and if so under what
conditions. Layered schemas are used to implement machine-readable
data exchange agreements to filter data elements during exchange.

{{< figure src="../layered-schemas-usecase-data-exchange.png" >}}
