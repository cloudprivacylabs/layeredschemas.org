---
title: "Overview"
weight: 1
menu:
  docs:
    parent: introduction
toc: false
---

A **schema** describes data. This is a JSON schema:

```
{
  "type": "object",
  "properties": {
     "firstName": {
        "type": "string"
     },
     "age": {
        "type": "number"
     }
  }
}
```

It defines an object containing two attributes, `firstName` which is a
`string` value, and `age` which is a number. Traditional schemas
usually define structure (attributes, nested attributes, repetitive
attributes, etc.) and constraints (min/max values, pattern,format,
etc.). A data object that conforms to a schema is an **instance** of
that schema. The following JSON object is an instance of the above
schema:

```
{
  "firstName": "John",
  "age": 21
}
```

Schemas are primarily used for data validation. Once data objects are
validated using a schema, they can be processed easily using native
data values. In the above example, a data processing application can
convert the `firstName` to its native string representation and the
`age` to a platform-specific numeric value after the object is
validated.

Traditional schemas usually do not contain much semantic
information. For instance, the schema above does not include the fact
that `firstName` and `age` are personally identifiable
information. 

Layered schemas use an open-ended schema language to describe
data. The above schema can be written as:

```
{
  "@type": "Schema",
  "attributes": {
    "firstName": {
       "@type": "Value",
       "type": "string",
    },
    "age": {
       "@type": "Value",
       "type": "number"
    }
  }
}
```

Here, `@type: Value` defines the attribute as a value attribute.  A
layered schema makes a distinction between attribute kinds like
`Value`, `Object`, `Array`, etc. Everything else are simply semantic
annotations. Above, `type: string` is a semantic annotation whose
meaning depends on the system processing it.  For instance, we can add
the fact that the two fields are personally identifiable information
into this schema by defining a new term, `privacyClassifications`:

```
{
  "@type": "Schema",
  "attributes": {
    "firstName": {
       "@type": "Value",
       "type": "string",
       "privacyClassifications": ["PII"]
    },
    "age": {
       "@type": "Value",
       "type": "number",
       "privacyClassifications": ["PII"]
    }
  }
}
```

This type of extensions to the core schema language is possible
because layered schema specification describes how to define
purpose-built terminologies and the semantics for its terms, and how
to incorporate it into the schema ecosystem. In this case,
`privacyClassifications` is a term that uses set semantics.

We can now **slice** this schema into layers. We can put the `type` and
`privacyClassification` annotations into separate overlays.

```
{
  "@type": "Schema",
  "attributes": {
    "firstName": {
      "@type": "Value"
    },
    "age": {
      "@type": "Value"
    }
  }
}

{
  "@type": "Overlay",
  "attributes": {
    "firstName": {
       "@type": "Value",
       "type": "string"
    },
    "age": {
       "@type": "Value",
       "type": "number"
    }
  }
}

{
  "@type": "Overlay",
  "attributes": {
    "firstName": {
       "@type": "Value",
       "privacyClassifications": ["PII"]
    },
    "age": {
       "@type": "Value",
       "privacyClassifications": ["PII"]
    }
  }
}
```

We can **compose** a new schema using the base schema and the privacy
overlay:

```
{
  "@type": "Schema",
  "attributes": {
    "firstName": {
       "@type": "Value",
       "privacyClassifications": ["PII"]
    },
    "age": { 
       "@type": "Value",
       "privacyClassifications": ["PII"]
    }
  }
}
```

Now, we can **ingest** the following data object using this schema:

```
{
  "firstName": "John",
  "age": "21
}
```

The result of this process is:
```
{
  "firstName": {
     "@type": "Value",
     "name": "firstName",
     "privacyClassifications": ["PII"]
     "value": "John",
  },
  "age": {
     "@type": "Value",
     "name": "age",
     "privacyClassifications": ["PII"]
     "value": 21
  }
}
```

The result of data ingestion is this linked object containing the
input values as well as all the semantic annotations declared in the
schema used to ingest the data. An application can work with this data
without an explicit knowledge of the schema used to ingest it.

Suppose the data is ingested from a system with limitations on string
lengths. This can be represented using an overlay:

```
{
  "@type": "Overlay",
  "attributes": {
     "firstName": {
        "@type": "Value",
        "maxLength": 32
     }
  }
}
```

A new schema can be composed by adding this overlay:

```
{
  "@type": "Schema",
  "attributes": {
    "firstName": {
       "@type": "Value",
       "privacyClassifications": ["PII"],
       "maxLength": 32
    },
    "age": { 
       "@type": "Value",
       "privacyClassifications": ["PII"]
    }
  }
}
```

When data is ingested using this schema, the `maxLength: 32` constraint will be validated, and data ingestion will fail if the `firstName` is longer than 32.
  
