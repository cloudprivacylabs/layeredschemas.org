---
title: "SchemaManifest"
weight: 10
menu:
  docs:
    parent: reference
---

A `SchemaManifest` object defines how a particular variant of a data
object is composed from its layers.

```
{
  "@context": "https://layeredschemas.org/v1/schema.jsonld",
  "@type": "SchemaManifest",
  "@id": "<unique identifier for the schema defined by this manifest>",
  "publishedAt": "<schema publish date>",
  "objectType": "<the object type defined by this schema>",
  "objectVersion": "<the version of the object type>",
  "bundle": "<the bundle containing all valid layers and references>",
  "schema": "<reference to the schema">,
  "overlays": [
     "reference to overlay1",
     "reference to overlay2",
     ...
   ]
}
```

## @type

The type of this JSON-LD object is a `http://layeredschemas.org/SchemaManifest`.

## @id

The unique ID for this schema variant.

##  publishedAt

`@type: http://schema.org/Date`<br>
`@id: http://layeredschemas.org/SchemaManifest.publishedAt`

The schema variant publish date.

## objectType

`@type: @id`<br>
`@id: http://layeredschemas.org/SchemaManifest.objectType`

The type of the data object defined by this schema. There can be many
schemas defining different variants of the same data object.

## objectVersion

`@type: string`<br>
`@id: http://layeredschemas.org/SchemaManifest.objectVersion`

The version of the data object defined by this schema variant.

## bundle

`@type: @id`<br>
`@id: http://layeredschemas.org/SchemaManifest.bundle`

Points to a [schema bundle]({{< ref "bundle.md" >}}) containing 
[strong references]({{<ref "definitions.md#strong-reference" >}})
for layers and schemas to resolve all [weak references]({{<ref "definitions.md#weak-reference" >}}).

## schema

`@type: @id`<br>
`@id: http://layeredschemas.org/SchemaManifest.schema`


Points to the [schema base]({{< ref "definitions.md#schema-base">}}).
The schema base must be of type `Schema` and declare the same object
type as the schema manifest.

## overlays

`@type: @id`<br>
`@container: @list` <br>
`@id: http://layeredschemas.org/SchemaManifest.overlays`

An ordered list of layers composing the schema. Each layer must be of
type `Overlay`, and declare the same `objectType` as the schema manifest.
