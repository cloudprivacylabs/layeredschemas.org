---
title: "Examples"
weight: 5
menu:
  docs:
    parent: introduction
toc: false
---

Layered schemas use linked data representation. That means, there can
be multiple representations of the same schema. JSON-LD is an
intuitive and readable method to represent schema layers, so we will
use JSON-LD to describe the layer data model.

Here is a simple schema with two attributes:

```
{
  "@context": "http://layeredschemas.org/ls.jsonld",
  "@type": "Schema",
  "@id": "http://example.org/Example/schema",
  "targetType": "http://example.org/Example",
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
  "@context": "http://layeredschemas.org/ls.jsonld",
  "@type": "Overlay",
  "@id": "http://example.org/Example/schema/typeOverlay",
  "targetType": "http://example.org/Example",
  "attributes": {
    "attr1": {
       "@type": "Value",
       "type": "string
    }
  }
}
```

When a schema is composed using the above overlay,
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

