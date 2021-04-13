---
title: "Bundle"
weight: 30
menu:
  docs:
    parent: spec
---

A schema `Bundle` links a group of schema layers together so the
references to the layers in a schema manifest and references to
objects in schemas can be resolved unambiguously. In other words, a
`Bundle` is a mechanism to convert [weak
references]({{<ref "definitions.md#weak-reference">}}) into [strong
references]({{<ref "definitions.md#strong-reference">}}).

```
{
  "@context": "https://layeredschemas.org/v1.0/ls.jsonld",
  "@type": "Bundle",
  "@id": "<unique identifier for the schema bundle>",
  "references": {
     "weak-reference": "strong-reference",
     "weak-reference": [
          "strong-reference",
          "strong-reference",
          ...
       ]
     }
  }
}
```

## @type

The type of this JSON-LD object is a
`http://layeredschemas.org/Bundle`.

## @id

The unique ID for this schema bundle.

## references

`@id: http://layeredschemas.org/Bundle/references`</br>
`@container: @id`

Specifies one or more [strong references]({{<ref
"definitions.md#strong-reference">}}) for each [weak
reference]({{<ref "definitions.md#weak-reference">}}).
