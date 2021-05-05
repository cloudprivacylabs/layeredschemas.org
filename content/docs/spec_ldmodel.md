---
title: "Property Graph Model"
weight: 60
menu:
  docs:
    parent: spec

---

{{% status %}}
This specification is in **alpha** stage. It is still being actively
developed, but the basic structure is not likely to change. 
Use the  [GitHub](https://github.com/cloudprivacylabs/lsa-spec) repository to contribute.
{{% /status %}}

A layered schema defines a linked data model that can be represented
as a property graph. In general, a **data ingestor** component can be
built for a specific purpose to translate an input object into a
specific data model with the help of a layered schema. Because of
this, it is not possible to specify only one **data ingestor**. This
specification is for a property graph ingestor. The JSON document

``` 
object: {
  "key": "value" 
} 
``` 

will be represented as:

{{<figure src="../spec_ldmodel_boxeddata.png">}}

The differences of this representation from an RDF translation are:

  * The property graph data model does not use blank nodes when
    representing data objects,
  * The edge attributes can be specified in the schema,
  * All schema attributes are nodes of the graph (they are not RDF predicates).
  * Nodes are not necessarily IRIs
  * Literals are not necessarily strings

## Ingestion operation

The data ingestion operation converts an object into a property
graph. The operation is declared as:

```
ingest(input_object,baseId)
```
where

  * `input_object` is the data object,
  * `baseId` is the identifier of the node containing the root element
    of the object
    
### Object

Given the schema:

```
{
   "@id": <attrId>,
   "@type": "Object",
   "targetType": <targetType>,
   "targetId": <targetId>,
   "attributes": [
      {attr1},
      {attr2}
   ]
} 
```

executing:

```
ingest_object(input_object,baseId)
```

produces:

{{< figure src="../spec_ldmodel_objectingest.png" >}}

where:

  * `<attrId>` is the node id of the object node in the schema,
  * `<targetType>` is the optional type of the target object,
  * `<targetId>` is the optional identifier of the ingested object. 
  * `objectId` is `baseId + "." + targetId"`. The ingestion algorithm
    may be configured to use a different delimiter than
    ".". `targetId` may be empty, in which case, `attrId` is used. If
    this is the root element (i.e. there is no `attrId`, then `baseId`
    is used).

The attributes may control the labels on the edges using the
`ls:edgeLabel` attribute. For instance, if `attr1` contains 

```
"ls:edgeLabel": "label"
```

then:

{{< figure src="../spec_ldmodel_objectingest2.png" >}}

### Array

Given the schema:

```
{
   "@id": <attrId>,
   "@type": "Array",
   "targetType": <targetType>,
   "targetId": <targetId>,
   "items": {...}
} 
```

executing:

```
ingest_array(input_array,baseId)
```

produces:

{{< figure src="../spec_ldmodel_arrayingest.png" >}}

where:

  * `<attrId>` is the node id of the object node in the schema,
  * `<targetType>` is the optional type of the target object,
  * `<targetId>` is the optional identifier of the ingested object. 
  * `arrayId` is `baseId + "." + targetId"`. The ingestion algorithm
    may be configured to use a different delimiter than
    ".". `targetId` may be empty, in which case, `attrId` is used. If
    this is the root element (i.e. there is no `attrId`, then `baseId`
    is used).

The attributes may control the labels on the edges using the
`ls:edgeLabel` attribute. 

### Value 

Given the schema:

```
{
   "@id": <attrId>,
   "@type": "Value",
   "targetType": <targetType>,
   "targetId": <targetId>
}
```
executing:

```
ingest_value(input_value,baseId)
```

produces:

{{< figure src="../spec_ldmodel_valueingest.png" >}}

In some use cases, it may also make sense to ingest a value as:

{{< figure src="../spec_ldmodel_valueingest2.png" >}}

In the second approach, the annotations are attached to the value. If
the value is moved within the graph, the annotations move with
it. This can be important in security sensitive situations where
values may have attached security contexts. For example, this may
prevent copy/pasting a sensitive value into a context that does not
accept such values.
