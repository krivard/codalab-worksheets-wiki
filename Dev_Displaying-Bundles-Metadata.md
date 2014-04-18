#Displaying Bundles Metadata

**Bundles Metadata**

Bundles can contain metadata properties, for instance, once can expect a bundle to contain:

```
{
    "bundle_type" : "dataset",
    "data_hash" : "0x21a8f5ecb7047a32f31dc7c10bf78a01cc4bcf7b",
    "hard_dependencies" : [  ],
    "metadata" : {
        "created" : 1397759169,
        "data_size" : 11237,
        "description" : "This is an image bundle",
        "name" : "imagebundle",
        "tags" : [ "mytag" ]
    },
    "state" : "ready",
    "uuid" : "0x4d15fe795fa846c88c7729dc1978f6c1"
}
```

**Displaying Bundles Metadata**

This document proposes how to display such metadata and bundle contents for the CLI and the website by extending the markdown of the worksheet.

**Worksheets Extended Markdown**

Several frameworks like DJango and AngularJS use '{{ }}' as a templating mechanism to bind data into HTML. Similarly, the proposed approach is to extend markdown with templating capabilities using a '{{ }}' block. The model that the template can use includes a bundle, it's metadata and contents.

**Formal Definition**

```
<template> ::= "{{" <template-data> "}}"

<template-data> ::= <bundle-uuid> | <bundle-properties>

<bundle-properties> ::= "." <literal> | <bundle-properties> "." <literal>

<literal> ::= '"' <text> '"' | "'" <text> "'" | <text>
```

**Markdown Template Examples**

***Image from Bundle Example***

```
![Bundle Image]({{ bundles.0x4d15fe795fa846c88c7729dc1978f6c1.files.'image.png' }})

[0x4d15fe795fa846c88c7729dc1978f6c1: Bundle]{0x4d15fe795fa846c88c7729dc1978f6c1}
```

***Metadata Table Example***

```
| Properties    | Data                                                   |
| ------------- | ------------------------------------------------------ |
| Name          | {{ 0x4d15fe795fa846c88c7729dc1978f6c1.metadata.name }} |
| Tags          | {{ 0x4d15fe795fa846c88c7729dc1978f6c1.metadata.tags }} |

[0x4d15fe795fa846c88c7729dc1978f6c1: Bundle]{0x4d15fe795fa846c88c7729dc1978f6c1}
```

From the cli one can execute:

```
>>cl print TestWorksheet

| Properties    | Data                                                   |
| ------------- | ------------------------------------------------------ |
| Name          | Vision System based on Neo-Cortex Neural Simulations   |
| Tags          | Machine Learning, Artificial Intelligence, Big Data    |
```

***Displaying ONLY an Image from Bundle***

In some cases, hiding the bundle block could be desirable. This can be accomplished by referencing the bundle without a description:

```
!\[Bundle Image\]({{ bundles.0x4d15fe795fa846c88c7729dc1978f6c1.files.'image.png' }})

[0x4d15fe795fa846c88c7729dc1978f6c1]{0x4d15fe795fa846c88c7729dc1978f6c1}
```

**Implementation Details**

1. **Migrate to Pagedown and Pagedown-Extra:** The current markdown JavaScript provider lacks supports for markdown tables, tables are important to support the data display scenarios. The pagedown project is MIT licensed and provides support for displaying tables. See [Pagedown](https://code.google.com/p/pagedown/) [Pagedown Extra](https://github.com/jmcmanus/pagedown-extra)

2. **Template binding is performed on the bundle-service:** In order to support templates in both: CLI and website. Makes sense to perform the templating operation in the bundle service. This enables clients to request markdown from the server and receive strict-markdown that can be rendered with standard libraries.