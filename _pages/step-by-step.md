---
layout: inner
title: Step-by-Step MarkLogic Processor Guide
lead_text: ''
permalink: /step-by-step
---

# Step-by-Step MarkLogic Processor Guide


## Initial Setup for Guide

See the [Getting Started page][getting-started-page] for instructions on setting your environment up for this tutorial.

## Review MarkLogic Database State

In order to view the status of the MarkLogic database, we'll be using the MarkLogic QConsole. You can read more about the tool in the [Query Console User Guide][qconsole-user-guide].

Go to the MarkLogic QConsole at [http://localhost:8000/qconsole](http://localhost:8000/qconsole) and run the following script. 

```javascript
'use strict';

let collection = 'iot-data';
let collectionQuery = cts.collectionQuery(collection);
[
  `${collection} count ${cts.estimate(collectionQuery)}`,
  'Sample of data',
  fn.subsequence(cts.search(collectionQuery), 1, 3)
];
```

You should see output similar to the following.

```javascript
["iot-data count 0", "Sample of data", null]
```

## IoT Example Dataset

For this step-by-step guide we'll be using compressed aggregate JSON data. The data is a mock-up of IoT power data from different devices. The data can be downloaded [here][iot-data].  

Go to the NiFi interface at [http://localhost:8080/nifi](http://localhost:8080/nifi). 

## MarkLogic Put Processor

First, we'll step through using the `PutMarkLogic` processor. The following are detailed steps. If you'd like to skip through the detailed setup, you can import the [NiFi template][nifi-put-template] and fill in the key following key information:

 * Folder location of the IOT-Data.json.zip in the ListFile configuration
 * MarkLogic credentials to the DatabaseClient Service associated with the PutMarkLogic Processor

### Add ListFile Processor

### Add FetchFile Processor

### Add UnpackContent Processor

### Add SplitText Processor

### Add PutMarkLogic Processor

## MarkLogic Query Processor

### Add QueryMarkLogic Processor


[getting-started-page]:./getting-started/
[qconsole-user-guide]:http://docs.marklogic.com/guide/qconsole/intro
[iot-data]:../files/IOT-Data.json.zip
[nifi-put-template]: ../files/PutMarkLogicExample.xml
