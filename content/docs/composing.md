---
title: "Composing Layers"
weight: 50
menu:
  docs:
    parent: spec

---

Composition operation combines layers to create a new variant of a
schema, or to create a new overlay that is a combination of several
overlays. When composing layers, an overlay can be added into a schema or
another overlay. A schema cannot be composed with another schema. 

When composing schema layers, all layers
must agree on the `objectType`. That means, all non-empty object types
must agree. A layer without an object type can be composed with any
layer.

The following are valid compositions:

 * `Schema(object) + Overlay_1 + Overlay_2(object)`
The result is a schema for `object`.
 * `Overlay_1(object) + Overlay_2(Object)`
The result is an overlay for `object`.
 * `Schema(object) + Overlay_1 + Overlay_2(object)`
The result is a schema for object. The first overlay can be composed with the schema because it is not declared for a particular object type. 

## Options

The composition algorithm uses an `options` parameter containing the
following options:

  * `Union = false`: If `false`, only those attributes that exist in
    the target layer will be in the resulting layer. If `true`,
    attributes that are not in the target layer but that exist in the
    source layer will be added to the target layer.
    
## Terms
    
A key part of the algorithm is composing the values of terms included
in the attribute. It should be possible for an implementation to
define terms that specify term-specific composition methods. The
default composition for terms is as follows: Let `A` and `B` be
attributes, and let `A[t]` denote the value of the term `t` in
attribute `A`.

  * If `t` is a `@set`, then the composition of `A[t]` and `B[t]` is
    `A[t] ∪ B[t]`.  Unless otherwise specified, all JSON-LD terms are
    sets. 
  * If `t` is a `@list`, then the composition of `A[t]` and `B[t]` is 
    `A[t] + B[t]`.
  * The schema implementation should support override semantics.
    That is, the composition of `A[t]` and `B[t]` is:
      * `A[t]`, if `B[t]` is nil
      * `B[t]`, if `B[t]` is not nil
      
      
### Examples

Set/List composition:

```
A: {
  "@id": "attr1",
  "setTerm": [ "a", "b" ],
  "listTerm" : 1
}

B: {
  "@id": "attr1",
  "setTerm": [ "a", "c"],
  "listTerm": [ 1, 2 ]
}

Composition of A and B: {
  "@id": "attr1",
  "setTerm": ["a", "b", "c"],
  "listTerm": [1, 1, 2]
}
```

Override:
```
A: {
  "@id": "attr1",
  "value": "a"
}

B: {
  "@id": "attr1",
  "value": "b"
}

Composition of A and B: {
  "@id": "attr1",
  "value": "b"
}

Composition of B and A: {
  "@id": "attr1",
  "value": "a"
}
```

## Layers

In the following algorithm to compute composition of layers, the
`attr.path` refers to the sequence of `@id`s of attributes starting from
the root attribute node to the attribute `attr`.

```
ComposeLayers(options,target,source)
  ComposeTerms(target,source)
  For each sourceAttr in source.attributes processed depth-first
    Find targetAttr such that targetAttr.path has sourceAttr.path as a suffix
    ComposeAttribute(options,targetAttr,sourceAttr)

  If options.Union
    Add all source attributes that are not in target into target
```

This algorithm allows defining overlays that contains only the leaf
nodes without the intermediate steps. For example:
```
{
  "@type": "Schema",
  "attributes": {
    "obj": {
      "@type": "Object",
      "attributes": {
         "nestedAttr": {
            "@type": "Value"
         }
      }
    }
  }
}

{
  "@type": "Overlay",
  "attributes": {
    "nestedAttr": {
      "@type":"Value",
      "descr": "description"
    }
  }
}
```

The `nestedAttr` in the overlay has path `nestedAttr`, which matches
`obj.nestedAttr`, so the composition becomes:

```
{
  "@type": "Schema",
  "attributes": {
    "obj": {
      "@type": "Object",
      "attributes": {
         "nestedAttr": {
            "@type": "Value",
            "descr": "description"
         }
      }
    }
  }
}
```

Same is also true for the following overlay with path `obj.nestedAttr`:

```
{
  "@type": "Overlay",
  "attributes": {
    "obj": {
      "@type": "Object",
      "attributes": {
         "nestedAttr": {
           "@type":"Value",
           "descr": "description"
         }
      }
    }
  }
}
```

The `ComposeAttribute` algorithm computes the composition of all
annotations, and recursively composes nested attributes. The types of
the attributes must agree. An overlay cannot redefine an attribute
using the same id with a different `@type`.

```
ComposeAttribute(options,target,source)
  ComposeTerms(target,source)
  
  switch target.Type:
    Object: ComposeAttributes(options,target.attributes,source.attributes)
    Array: ComposeAttribute(options,target.items,source.items)
    Composite: ComposeAttribute for all matching attributes
    Polymorphic: ComposeAttribute for all matching attributes
```
