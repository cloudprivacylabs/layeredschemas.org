---
title: "Compiling Schemas"
weight: 55
menu:
  docs:
    parent: spec
---

{{% status %}}
This specification is in **alpha** stage. It is still being actively
developed, but the basic structure is not likely to change. 
Use the  [GitHub](https://github.com/cloudprivacylabs/lsa-spec) repository to contribute.
{{% /status %}}

A **compiled schema** is a schema that has a single layer (no
overlays) with no `Reference` or `Composite` type attributes. A
compiled schema can be used for data capture and data ingestion. The
algorithm for compiling a schema is as follows:

Given a schema `root = base + overlays`:
  * Compose `root`
  * Recursively compile all `Reference` attributes and replace them with
    `Object` attributes
  * Recursively compile all `Composite` attributes and replace them
    with `Object` attributes

A compiled schema is no longer loop-free, so the recursion must detect loops.

## Compiling Composite attributes

The elements of a composition must be all `Object` or `Value`
attributes. The result of compiling a `Composite` attribute is an
`Object`. The algorithm is as follows:
 
  * Create an empty object node `result`
  * For each element of the composition:
    * If the element is an `Object`, add all attributes of the object into the `result`
    * If the element is a `Value`, add the attribute into the `result` with attribute id as its key
  * Replace the `Composite` node with `result`
  
Example:

Input:

```
{
  "@type": "Composite`,
  "@id": "attrId",
  "allOf": [
    {
      "@type": "Object",
      "attributes": {
         {attr1},
         {attr2}
      }
    },
    {
      "@type": "Value",
      "@id": "valueId"
    }
  ]
}
```

Compiled:
```
{
  "@type": "Object",
  "@id": "attrId",
  "attributes": {
      {attr1},
      {attr2},
      {
        "@id": "valueId",
        "@type": "Value"
      }
  }
}
```
   
## Compiling Reference attributes

References are resolved using a schema repository component, or a
schema bundle. A schema bundle restricts and specifies the
schemas/overlays that can be used to resolve references by schema
name. If there is not a schema bundle specified for the compilation
process, then it is up to the schema repository to resolve the
reference based on the processing context.

The algorithm to compile a reference is as follows:

  * If the reference is compiled before, use the same `Object`
    instance. Compilation is complete.
  * Load the referenced schema and overlays
  * Compose the loaded schema
  * Replace the compiled node with the composed schema. (The schema **is** the root node of an `Object`) 
  * Store the composed schema in a compilation context so further
    references to the same object can reuse the compiled instance
    
    
