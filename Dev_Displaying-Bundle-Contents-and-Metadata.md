#Displaying Bundle Contents and Metadata

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

The proposed approach is to extend markdown with well-defined comments to describe additional representations of data. The model that the template can use includes a bundle, it's metadata and contents.

**Formal Definition**

```
<template> ::= "// " (<directive> | <data>)

<directive> ::= "display " ("table" | "default" | "inline")

<data> ::= <text> ": " <type> <path>

<type> ::= "image" | "metadata"

<path> ::= "/" (<text> <path> | <text>)
```

**Markdown Template Examples**

***Image from Bundle Example***

```
// display inline
// MyImage: image/image.png

[0x4d15fe795fa846c88c7729dc1978f6c1: Bundle]{0x4d15fe795fa846c88c7729dc1978f6c1}
```

***Metadata Example***

```
// display default
// Name: metadata/name
// Tags: metadata/tags

[0x4d15fe795fa846c88c7729dc1978f6c1: Bundle]{0x4d15fe795fa846c88c7729dc1978f6c1}
```

From the cli one can execute:

```
>>cl print TestWorksheet

Name: Vision System
Tags: Machine Learning, Artificial Intelligence, Big Data
```

***Metadata Table Example***

```
// display table
// Name: metadata/name
// Tags: metadata/tags

[0x4d15fe795fa846c88c7729dc1978f6c1: Bundle]{0x4d15fe795fa846c88c7729dc1978f6c1}
```

From the cli:

```
>>cl print TestWorksheet

| Name          | Tags                                                   |
| ------------- | ------------------------------------------------------ |
| Vision System | Machine Learning, Artificial Intelligence, Big Data    |
```

***Displaying ONLY an Image from Bundle***

In some cases, hiding the bundle block could be desirable. This can be accomplished by referencing the bundle without a description:

```
// display inline
// MyImage: image/image.png

[0x4d15fe795fa846c88c7729dc1978f6c1]{0x4d15fe795fa846c88c7729dc1978f6c1}
```

**Implementation Details**

1. **Migrate to Pagedown and Pagedown-Extra:** The current markdown JavaScript provider lacks supports for markdown tables, tables are important to support the data display scenarios. The pagedown project is MIT licensed and provides support for displaying tables. See [Pagedown](https://code.google.com/p/pagedown/) [Pagedown Extra](https://github.com/jmcmanus/pagedown-extra)

2. **Template binding is performed on the bundle-service:** In order to support templates in both: CLI and website. Makes sense to perform the templating operation in the bundle service. This enables clients to request markdown from the server and receive strict-markdown that can be rendered with standard libraries.