---
title: "Layers"
weight: 20
menu:
  docs:
    parent: reference
---

There are two types of layers:

  * **Schema** (`@type: Schema`) specifies the structure for a data
    object. It defines the minimal set of attributes common to all
    variants of the object
  * **Overlay** (`@type: Overlay`) adds additional information about
  the attributes defined in the schema base.


Multiple layers can be combined to form a new layer. During composition

  * At most one of the layers can be a schema base.
  * If there is a schema base, it must be the first object in
    composition
    
When multiple layers are combined, the composite object is another
`Layer`.  If the first layer is a schema base, the composite object is
a `SchemaBase`.

## Attribute Types

An attribute can be one of the following:

### Value

A value attribute contains a value of an unspecified type. The actual
type and encoding of the underlying data can be specified in other
layers.

```
{
  "@id": "<attributeId">,
  "@type": "Value"
}
```

### Reference

A reference attribute denotes a reference to another object. The
referenced object is open-ended, and the schema resolution method used
in the architecture determines what exactly a reference means. The
reference can be:

  * a hash value or some other DRI of a schema,
  * an IRI specifying an object, or a particular version of that object,
  * an IRI specifying a schema,
  * or some other identifier that can be resolved to a schema.
  
See: [Strong Reference]({{< ref "definitions.md#strong-reference" >}}), 
[Weak Reference]({{< ref "definitions.md#weak-reference" >}})

```
{
  "@id": "<attributeId>",
  "@type": "Reference",
  "reference: "<target object reference>"
}
```

### Object

An object attribute is a nested object, similar to a JSON object.

```
{
  "@id": "<attributeId>",
  "@type": "Object",
  "attributes": {
     ...
  }
}
```
The `attributes` specifies the attributes of the nested object.

### Array

An array attribute describes the types of its elements:

```
{
  "@id": "<attributeId>",
  "@type": "Array",
  "items": {
    // element types
  }
}
```
The element types can be any attribute type. For instance, a value 
array is:
```
{
  "@id": "<attributeId>",
  "@type": "Array",
  "items": {
    "@type": "Value"
  }
}
```
And an object array is:
```
{
  "@id": "<attributeId>",
  "@type": "Array",
  "items": {
    "@type": "Object",
    "attributes": {
      ...
    }
  }
}
```

### Composition

An attribute may be the composition of multiple types. The following
example uses a reference type, and adds a value type to it.

Each element of a composition must be either an object type, a value
type, or another type that can be interpreted as an object type or a
value type. The result of a composition is always an object type.

```
{
  "@id": "<attributeId>",
  "@type": "Composition",
  "allOf": [
     {
       "@type": "Reference",
       "reference": "type1 reference"
     },
     {
       "@id": "<attributeId>",
       "@type": "Value",
     }
  ]
}
```

In the above example, the composition collects the attributes of the
referenced type, and adds a value attribute to it. Adding an array
type as an option to a composition is not allowed.

### Polymorphic

A polymorphic attribute can be one of the given types. A polymorpic
attribute must be an object type.

```
{
  "@id": "<attributeId>",
  "@type": "Polymorphic",
  "oneOf": [
     {
        "@type": "Reference",
        "reference": "<type 1>"
     },
     {
        "@type": "Reference",
        "reference": "<type 2>"
     }
  ]
}
```

The above polymorphic attribute can be one of `type 1` or `type
2`. The actual type of the attribute will be determined based on the
constraint annotations specified in other layers.
        

```
{
  "@context": "https://layeredschemas.org/v1/layer.jsonld",
  "@type": "Layer" or "SchemaBase",
  "@id": "<unique identifier for this layer>",
  "version": "<schema version>",
  "publishedAt": "<schema publish date>",
  "objectType": "<the object type defined by this schema>",
  "objectVersion": "<the version of the object type>",
  "bundle": "<the bundle containing all valid layers and references>",
  "base": "<reference to the schema base">,
  "layers": [
     "reference to layer1",
     "reference to layer2",
     ...
   ]
}
```

## @type

The type of this JSON-LD object is a
`http://layeredschemas.org/Schema`.

## @id

The unique ID for this schema variant.

## version

`@type: string`<br>
`@id: http://layeredschemas.org/Schema/version`

Version of the schema variant. This is the version of the schema
document, which is independent of the version of the object defined by
the schema.

##  publishedAt

`@type: http://schema.org/Date`<br>
`@id: http://layeredschemas.org/Schema/publishedAt`

The schema variant publish date.

## objectType

`@type: @id`<br>
`@id: http://layeredschemas.org/Schema/objectType`

The type of the data object defined by this schema. There can be many
schemas defining different variants of the same data object.

## objectVersion

`@type: string`<br>
`@id: http://layeredschemas.org/Schema/objectVersion`

The version of the data object defined by this schema variant.

## bundle

`@type: @id`<br>
`@id: http://layeredschemas.org/Schema/bundle`

Points to a [schema bundle]({{< ref "bundle.md" >}}) containing 
[strong references]({{<ref "definitions.md#strong-reference" >}})
for layers and schemas to resolve all [weak references]({{<ref "definitions.md#weak-reference" >}}).

## base

`@type: @id`<br>
`@id: http://layeredschemas.org/Schema/base`


Points to the [schema base]({{< ref "definitions.md#schema-base">}}). 
The schema base must declare the same object type as the schema.

## layers

`@type: @id`<br>
`@container: @list` <br>
`@id: http://layeredschemas.org/Schema/layers`

An ordered list of layers composing the schema. Each layer must be of type `Layer`, and declare the same `objectType` as the schema.
