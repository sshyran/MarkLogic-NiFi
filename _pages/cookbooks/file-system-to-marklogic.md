---
layout: inner
title: File System to MarkLogic
lead_text: ''
permalink: /file-system-to-marklogic
---

# File System to MarkLogic

This example watches a directory for files, imports them into MarkLogic, then deletes them. The MarkLogic URI is /files/ followed by the filename. The NiFi filename property is set by the GetFile processor. 

Many NiFi properties support embedded expressions, such as ${filename} in the ml.uri property below. For more about expressions, see [Apache NiFi Expression Language Guide.][nifi-exp-lang]


<a href="./files/FileSystemToMarkLogic.xml" download>Download Template</a>

## Processors:

### GetFile

reads files from a watched directory

**Properties**

Input Directory
 : */some/path*

### UpdateAttribute 

Set the MarkLogic URI attribute with custom property.

**Properties**

ml.uri
 : /files/${filename}

### PutMarkLogic

**Properties**

DatabaseClient Service
 : DefaultMarkLogicDatabaseClientService

URI Attribute Name
 : marklogic.uri

**Settings**

Automatically Terminate Relationships: `failure` and `success`



[nifi-exp-lang]:https://nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html

