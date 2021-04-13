---
title: "SchemaManifest"
weight: 10
menu:
  docs:
    parent: spec
---

A `SchemaManifest` object defines how a particular variant of a data
object is composed from its layers.

```
{
  "@context": "https://layeredschemas.org/v1.0/ls.jsonld",
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

The type of this JSON-LD object is a
`http://layeredschemas.org/SchemaManifest`.

## @id

The unique ID for this schema variant.

##  publishedAt

`@type: http://schema.org/Date`<br>
`@id: http://layeredschemas.org/v1.0/SchemaManifest/publishedAt`

The schema variant publish date.

## objectType

`@type: @id`<br>
`@id: http://layeredschemas.org/v1.0/Layer/objectType`

The type of the data object defined by this schema. There can be many
schemas defining different variants of the same data object. 

Note that this term is identical to the `objectType` term used in layers.

## objectVersion

`@type: string`<br>
`@id: http://layeredschemas.org/v1.0/Layer/objectVersion`

The version of the data object defined by this schema variant.

Note that this term is identical to the `objectVersion` term used in layers.

## bundle

`@type: @id`<br>
`@id: http://layeredschemas.org/v1.0/SchemaManifest/bundle`

Points to a [schema bundle]({{< ref "bundle.md" >}}) containing 
[strong references]({{<ref "definitions.md#strong-reference" >}})
for layers and schemas to resolve all [weak references]({{<ref "definitions.md#weak-reference" >}}).

## schema

`@type: @id`<br>
`@id: http://layeredschemas.org/v1.0/SchemaManifest/schema`


Points to the schema. The schema must be of type `Schema` and declare
the same object type as the schema manifest.

## overlays

`@type: @id`<br>
`@container: @list` <br>
`@id: http://layeredschemas.org/v1.0/SchemaManifest/overlays`

An ordered list of layers composing the schema. Each layer must be of
type `Overlay`, and declare the same `objectType` as the schema manifest.

## Expanded Model

Layered schema implementations must work with the following expanded
JSON-LD representation. 

```[
  {
    "@type": [
      "http://layeredschemas.org/v1.0/SchemaManifest"
    ],
    "http://layeredschemas.org/v1.0/Layer/objectType": [
      {
        "@value": "objType"
      }
    ],
    "http://layeredschemas.org/v1.0/Layer/objectVersion": [
      {
        "@value": "objVersion"
      }
    ],
    "http://layeredschemas.org/v1.0/SchemaManifest/publishedAt": [
      {
        "@value": "20210320T00:00:00Z"
      }
    ],
    "http://layeredschemas.org/v1.0/SchemaManifest/bundle": [
      {
        "@id": "sha256://27634767a887e887f8e87..."
      }
    ],
    "http://layeredschemas.org/v1.0/SchemaManifest/schema": [
      {
        "@id": "sha256://5878a8e8faa9890..."
      }
    ],
    "http://layeredschemas.org/v1.0/SchemaManifest/overlays": [
      {
        "@list": [
          {
            "@id": "sha256://587376a7676f767..."
          },
          {
            "@id": "sha256://ee538866547676f767..."
          }
        ]
      }
    ]
  }
]

```
