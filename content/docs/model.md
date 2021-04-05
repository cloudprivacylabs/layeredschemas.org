---
title: "Model and Semantics"
weight: 5
menu:
  docs:
    parent: introduction

---

Layered schemas use linked data representation. That means, there can
be multiple representations of the same schema. JSON-LD is an
intuitive and readable method to represent schema layers, so we will
use JSON-LD to describe the layer data model.

## Examples

Here is a simple schema with two attributes:

```
{
  "@context": "http://layeredschemas.org/v1.0/ls.jsonld",
  "@type": "Schema",
  "@id": "http://example.org/Example/schema",
  "objectType": "http://example.org/Example",
  "attributes": {
    "attr1": {
       "@type" : "Value"
    },
    "attr2": {
       "@type": "Array",
       "items": {
          "@type": "Value"
       }
    }
  }
}
```

This schema defines `attr1` as a value, and `attr2` as an array of
values. The following JSON document is an instance of this schema:
```
{
  "attr1": "value1",
  "attr2": [ "value2" ]
}
```
So is the following:
```
{
  "attr1": 1,
  "attr2": [ 2, 3, 4 ]
}
```

This is because the schema defines `attr1` as a value, and `attr2` as
an array of values. 

Now let's add the following layer:

```
{
  "@context": "http://layeredschemas.org/v1.0/ls.jsonld",
  "@type": "Overlay",
  "@id": "http://example.org/Example/schema/typeOverlay",
  "objectType": "http://example.org/Example",
  "attributes": {
    "attr1": {
       "type": "string
    }
  }
}
```

When a schema is composed using the schema base and the above overlay,
the definition for `attr1` becomes:
```
"attr1": {
  "@type": "Value",
  "type": "string"
}
```

With this definition, only the first JSON document is an instance of
the schema.

Layered schemas support nested objects:

```
"attributes": {
  "object": {
     "@type": "Object",
     "attributes" : {
        "nestedAttr": {
          "@type": "Value",
          "type": "boolean"
        }
     }
  }
}
```

An instance for the above schema is the following JSON object:
```
{
  "object": {
    "nestedAttr": true
  }
}
```
The following XML document is also an instance of the same schema:
```
<object>
  <nestedAttr>true</nestedAttr>
</object>
```

When a data object is ingested using a layered schema, the attributes
of the data object are linked to its schema definitions. So the above
JSON and XML documents become:

```
{
  "object": {
    "@type": "Object",
    "attributeId": "object",
    "nestedAttr": {
      "@type": "Value",
      "type": "boolean",
      "attributeId": "nestedAttr",
      "value": "true"
    }
  }
}
```

The ingested data object is simply linked data, and can be serialized
using JSON-LD or RDF. This object contains references to the schema
for each recognized attribute and the actual input values. Attributes
that are not in the schema are still present in this expanded form
without the schema reference. An application can process this object
without explicit knowledge of the schema variant used to ingest it or
the exact format of the input object.

For example, if the schema contains a "security overlay" that assigns
security flags to certain fields, an application can process the
linked data solely based on the security flags assigned to the
field. It can use a key to encrypt all field values with a certain
security attributes, or decide if the existing use of the data object
allows revealing the underlying value, and mask it if necessary.

## Syntax

### Layer

A layer has the following structure:

```
{
  "@context": "http://layeredschemas.org/v1.0/ls.jsonld",
  "@type": "Schema", or "Overlay",
  "@id": "http://layeredschemas.org/exampleSchema",
  "objectType": "http://example.org/SomeObject",
  "attributes": [
    {
      "@id": "attribute id,
      "@type": "Value",
      .. // other annotations
    },
    ...
  ]
}
```

`@context`
: The layered schemas context is used to expand the schema. This
context defines the expanded terms for the schema attributes and
schema structure. If the schema includes semantic annotations not
defined in this context, additional contexts can be specified as an
array.

`@type`
: This is a `Schema` object. Valid values are:

 * `Schema` or `http://layeredschemas.org/Schema`
 * `Overlay` or `http://layeredschemas.org/Overlay`
 
 Note that this notation declares the JSON-LD node type as a schema or
 overlay.

`@id`
: The schema identifier. The id is usage specific, it can be a
  globally unique identifier, or an identifier meaningful for the
  domain schema is used in. This identifier can be used to define
  references to the schema, and should be unique for the domain.
  
  Note that this notation declares the JSON-LD node id as the schema
  identifier.
  
`objectType` or `http://layeredschemas.org/Layer/objectType`
: The object type defined by the schema, or the overlay is for. This
  object type can be used to reference to this schema from other
  schemas.

`attributes` or `http://layeredschemas.org/Object/attributes`
: The attributes of the object defined by this schema. The attributes
are defined as a JSON array, so the ordering of the attributes within
the schema is important. The first attribute `attr1` is a value. The
type, encoding, and format of the attribute can be specified in
overlays. That means, there may be schema variants with different
encodings, formats, or types for this attribute.

#### Attributes

The `attributes` term expands to
`http://layeredschemas.org/Object/attributes`. It is an idmap, so
attributes can be defined as an array of attributes if ordering is
significant, or as a JSON object. That is, the following two
definitions are valid, with the difference that the first one
guarantees consistent ordering of the attributes in the schema. 

```
"attributes": [
  { 
    "@id": "attr1"
  },
  { 
    "@id": "attr2" 
  },
  ...
]
```

```
"attributes": {
  "attr1": {},
  "attr2": {},
  ...
}
```

Layered schema support the following types of attributes:

`@type: Value` or `@type: http://layeredschemas.org/Value`
: A sequence of bytes. Examples are `string`, `int`, `xs:Date`,
  etc. The schema may specify the encoding, format, and architecture
  or representation specific type.
  
``` 
{ 
  "@id": "attribute1",
  "@type": "Value" 
}
```

`@type: Object` or `@type: http://layeredschemas.org/Object`
: Key-attribute pairs, similar to a JSON object or an XML element. An
  `Object` attribute has `attributes` that list the nested attributes.

```
{
  "@id": "object1",
  "@type": "Object",
  "attributes": {
     "nestedAttr1": {
       "@type": "Value"
     },
     ...
  }
}
```

`@type: Array` or `@type: http://layeredschemas.org/Object`
: An ordered list of attributes. An `Array` attribute has `items` that
specifies the structure of array items. The following example is a
value array.

```
{
  "@id": "array1",
  "@type": "Array",
  "items": {
    "@id": "arrayElementsID",
    "@type": "Value"
  }
}
```

`@type: Reference` or `@type: http://layeredschemas.org/Reference`
: A reference to another object. How this reference is resolved is
  implementation dependent. It can be a [strong
  reference]({{<ref "definitions.md#strong-reference">}}) that selects a
  particular schema variant, or a [weak
  reference]({{<ref "definitions.md#weak-reference">}}) that will be resolved at
  runtime based on the existing context. When compiled, the resulting schema will have all `Reference`
attributes replaced with the actual referenced schema.
  
```
{
  "@id": "weakref",
  "@type": "Reference",
  "reference": "http://example.org/ExampleObject"
}
```

This is a weak reference to an `ExampleObject` that will be resolved
based on the current processing context.

```
{
  "@id": "strongref",
  "@type": "Reference",
  "reference": "sha256://748736a7fde293...."
}
```
This is a strong reference that directly addresses a schema variant using its hash.


`@type: Composite` or `@type: http://layeredschemas.org/Composite`
: A composition of multiple attributes. The result of a composition is an `Object`, so the elements of a composition are limited to `Value`, `Object`, and `Reference` types.

```
{
  "@id": "compositeAttr",
  "@type": "Composite",
  "allOf": [
     {
       "@id":"part1",
       "@type": "Reference",
       "reference": "http://example.org/SomeObject"
     },
     {
       "@id": "part2",
       "@type": "Object",
       "attributes": {
          "attr1": {
             "@type": "Value"
          },
          "attr2": {
             "@type": "Value
          }
        }
     },
     {
       "@id": "part3",
       "@type": "Value"
     }
  ]
}
```

When compiled, all `Composite` attributes are replaced with `Object`
attributes containing all the attributes of the compsite attribute. In
the above example, the `compositeAttr` will be an `Object` containing
all attributes of `SomeObject`, `attr1`, `attr2`, and `part3`.

`@type: Polymorphic` or `@type: http://layeredschemas.org/Polymorphic`
: A polymorphic type that can be one of the types listed in the
  attribute definition. This type of attribute requires constraint
  annotations to decide the actual type of the object at
  runtime.
  
```
{
  "@id": "polyAttr",
  "@type": "Polymorphic",
  "oneOf": [
     {
       "@type": "Reference",
       "reference": "sha256://2566efadfe9843..."
     },
     {
       "@type": "Reference",
       "reference": "sha256://874658d0a9e8f.."
     }
  ]
}
```

This attribute can be one of two types defined by strong
references. When data object is ingested using this schema, the
`polyAttr` attribute will be checked if any one of the options
validate the input data. If one of them validates, then the type is
decided and the selected schema will be used to process the
attribute. If none matches, or more than one option matches, an error
will be raised.

## Composing Layers

Each layer is defined with an `objectType`. This is the type of the
object defined by the schema. When composing schema layers, all layers
must agree on the `objectType`. That means, all non-empty object types
must agree. A layer without an object type can be composed with any
layer.

When composing layers, an overlay can be added into a schema or
another overlay. A schema cannot be added to another schema. The
following are valid compositions:

 * `Schema(object) + Overlay<sub>1</sub> + Overlay<sub>2</sub>(object)`
The result is a schema for `object`.
 * `Overlay<sub>1</sub>(object) + Overlay<sub>2</sub>(Object)`
The result is an overlay for `object`.
 * `Schema(object) + Overlay<sub>1</sub> + Overlay<sub>2</sub>(object)`
The result is a schema for object. The first overlay can be composed with the schema because it is not declared for a particular object type. 
