---
title: "Slicing a layer"
weight: 60
menu:
  docs:
    parent: spec

---

Slicing operation creates new layers from an existing layer by
selecting a subset of the terms. It uses an `accept` operation that
selects the terms that will be included in the output.

```
SliceAttribute(attr,accept)
  newAttribute:= new Attribute
  For each (term, value) in attr
    If accept(term)
      newAttribue[term]=value
  
  If attr is one of Object, Array, Composite, Polymorphic
    For each nestedComponent under attr
      SliceAttribute(nestedComponent,accept)
      
  If newAttribute is not empty, return newAttribute
```

## Example

Consider the following layer:
```
"attributes": {
  "attr1": {
     "@type": "Value",
     "format": "url",
     "privacyClassifications": ["PII"]
  },
  "attr2": {
    "@type": "Object",
    "attributes": {
       "attr3": {
         "@type": "Value",
         "privacyClassifications": ["BIT"]
       }
    }
  }
}
```

Slicing this schema with an `accept` function that only accepts
`attributes`, `items`, `allOf`, `oneOf`, and `reference`:

```
"attributes": {
  "attr1": {
     "@type": "Value",
  },
  "attr2": {
    "@type": "Object",
    "attributes": {
       "attr3": {
         "@type": "Value"
       }
    }
  }
}
```

Slicing this schema with an `accept` function that accepts `format`:
```
"attributes": {
  "attr1": {
     "@type": "Value",
     "format": "url",
  }
}
```

Slicing this schema with an `accept` function that accepts `privacyClassifications`:
```
"attributes": {
  "attr1": {
     "@type": "Value",
     "privacyClassifications": ["PII"]
  },
  "attr2": {
    "@type": "Object",
    "attributes": {
       "attr3": {
         "@type": "Value",
         "privacyClassifications": ["BIT"]
       }
    }
  }
}
```
