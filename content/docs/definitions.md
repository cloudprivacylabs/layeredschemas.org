---
title: "Definitions"
weight: 5
menu:
  docs:
    parent: reference

---

## Schema base

A schema base is a schema layer that specifies the structure of the
data object defined by the schema. A schema base is defined by 

```
{
  "@type": "http://layeredschemas.org/Schema",
  "http://layeredschemas.org/Schema.attributes": [
    ...
  ]
}
```

or

```
{
  "@context:"http://layeredschemas.org/schema.jsonld",
  "@type": "Schema",
  "attributes": {
    ...
  }
}
```

Variations of a schema can be composed by adding different overlays to
a schem.

## Strong reference

A strong reference to a schema layer or schema resolves to a unique
object. Strong references use a decentralized resource identifier
(DRI) scheme to address the object (usually a hash).

```
{
  "@type": "Schema",
  "objectType": "SomeObject",
  "layers": [
     "sha256:a948904f2f0f479b8f8197694b30184b0d2ed1c1cd2a1ec0fb85d299a192a447"
  ]
}
```

This is a valid reference only if the referenced object is a layer or
another schema, and it has the same `objectType`.

Similarly:

```
{
  "@type": "Layer",
  "objectType": "SomeObject",
  "attributes": {
    "attr1": {
       "reference": "sha256:a948904f2f0f479b8f8197694b30184b0d2ed1c1cd2a1ec0fb85d299a192a447"
    } 
  }
}
```

This reference is valid only if it points to a schema. That schema can
be for a different type of object.

## Weak reference

A weak reference is any kind of reference to another object that can
resolve to multiple objects.

This kind of reference may refer to multiple schemas:

```
{
  "@type": "Schema",
  "objectType": "SomeObject",
  "layers": [
     "http://example.org/SomeObject/layer/v1.2"
  ]
}
```

The schema registry can resolve this link based on its own
configuration. For instance, if a registry allows only unique version
numbers, the above link would resolve to a definite schema. The link
resolution can be dependent on the processing context. For instance,
when processing data in a specific jurisdiction, layers tagged with
that jurisdiction can be selected.


