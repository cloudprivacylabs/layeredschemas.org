---
title: "Using the Data Privacy Vocabulary"
weight: 310
menu:
  docs:
    parent: other
draft: false
toc: false
description: Using the Data Privacy Vocabulary with layered schemas to annotate data elements
---

Layered schemas can be integrated with an existing vocabulary to
annotated data objects. As an example, consider the [Data Privacy
Vocabulary](https://dpvcg.github.io/dpv/).  It provides terms (classes
and properties) to describe and represent information related to
processing of personal data.

We will use a simple contact list data model to illustrate how to
use the DPV vocabulary:

```
{
    "firstName": "John",
    "lastName": "Doe",
    "contact": [
        {
            "type": "cell",
            "value": "555-555 5555"
        }
    ]
}
```

The data model contains two elements: Person and Contact:

Person schema:
```
{
    "@context": "http://layeredschemas.org/ls.jsonld",
    "@id": "http://example.org/Person/schemaBase",
    "@type": "Schema",
    "targetType": "http://example.org/Person",
    "attributes": {
        "http://example.org/Person/firstName": {
            "@type": "Value",
            "name":"firstName"
        },
        "http://example.org/Person/lastName": {
            "@type": "Value",
            "name": "lastName"
        },
        "http://example.org/Person/contact": {
            "@type": "Array",
            "name": "contact",
            "items": {
               "@type": "Reference",
                "reference": "http://example.org/Contact"
            }
        }
    }
}
```

Contact schema:
```
{
    "@context": "http://layeredschemas.org/ls.jsonld",
    "@id": "http://example.org/Contact/schema",
    "@type": "Schema",
    "targetType": "http://example.org/Contact",
    "attributes": {
        "http://example.org/Contact/value": {
            "@type": "Value",
            "name": "value"
        },
        "http://example.org/Contact/type": {
            "@type": "Value",
            "name": "type"
        }
    }
}

```

We can create an overlay to assign `dpv:DataSubject` to `Person`, and
`dpv:Name` and `dpv:Identifying` annotations to the names:

```
{
    "@context": [
        "http://layeredschemas.org/ls.jsonld",
        { 
           "dpv": "http://www.w3.org/ns/dpv#",
            "hasPersonalDataCategory": {
              "@id":"dpv:hasPersonalDataCategory",
              "@type":"@id"
            }
        }
    ],
    "@id": "http://example.org/Person/dpv",
    "@type": "Overlay",
    "targetType": ["http://example.org/Person","dpv:DataSubject"],
    "attributes": [
        {
           "@id": "http://example.org/Person/firstName",
           "@type": "Value",
           "hasPersonalDataCategory": [ "dpv:Name", "dpv:Identifying" ]
        },
        {
           "@id": "http://example.org/Person/lastName",
           "@type": "Value",
           "hasPersonalDataCategory": [ "dpv:Name", "dpv:Identifying" ]
        }
    ]
}
```
Line 4: Here we define the few terms we used from the DPV in a context<br>
Line 5: The `dpv` namespace<br>
Line 6: `hasPersonalDataCategory` is defined here so that its type can be set to `@id`.
 If this is not done, the values of `hasPersonalDataCategory` will be literal values instead of `@id`s.<br>
Line 15: With this declaration, the `Person` object is now also a `dpv:DataSubject`<br>

Ingesting the JSON document using the composition of
`http://example.org/Person/schemaBase` and
`http://example.org/Person/dpv` will give:

{{< figure src="../dpvls_person.png" >}}

We can define another overlay for the `Contact`:
```
{
    "@context": [
        "http://layeredschemas.org/ls.jsonld",
        { 
           "dpv": "http://www.w3.org/ns/dpv#",
            "hasPersonalDataCategory": {
              "@id":"dpv:hasPersonalDataCategory",
              "@type":"@id"
            }
        }
    ],
    "@id": "http://example.org/Contact/dpv",
    "@type": "Overlay",
    "targetType": ["http://example.org/Contact"],
    "attributes": [
        {
           "@id": "http://example.org/Contact/value",
           "@type": "Value",
           "hasPersonalDataCategory": [ "dpv:Contact", "dpv:Identifying" ]
        }
    ]
}
```

Adding this overlay to the `Contact` schema, we get:

{{< figure src="../dpvls_personcontact.png" >}}

In the above examples, the `@context` is defined inline. That context
can be externalized and placed into a separate file, which enables
schemas and overlays using a context containing two files instead of
one.
