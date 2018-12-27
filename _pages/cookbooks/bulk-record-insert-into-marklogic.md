---
layout: inner
title: Bulk Record Insert Into MarkLogic
lead_text: ''
permalink: /bulk-record-insert-into-marklogic
---

# Bulk Record Insert Into MarkLogic

For this example we'll be using compressed aggregate JSON data. The data is a mock-up of IoT power data from different devices. The data can be downloaded [here][iot-data]. 

If you'd like to skip through the detailed setup, you can import the [NiFi template][nifi-put-template] and fill in the key following key information:

 * Folder location of the `IOT-Data.json.zip` in the ListFile configuration
 * MarkLogic credentials to the DatabaseClient Service associated with the PutMarkLogicRecord Processor

## Add ListFile Processor

Drag the Processor icon next the NiFi logo into the template grid. Filter for the `ListFile` Processor click the `ADD` button.

With the `ListFile` Processor on your grid, right-click the processor select <i class="fas fa-cog"> Configure</i> from the menu. On the `PROPERTIES` tab, set the **Input Directory** property the directory where `IOT-Data.json.zip` lives. If other files are in the same directory, you'll also want to set the **File Filter** to the filename `IOT-Data.json.zip` to ensure additional files aren't processed.

## Add FetchFile Processor

Add the `FetchFile` processor to the grid and go to the processors configure screen. On the `SETTINGS` tab, select the check boxes to automatically terminate the `failure`, `not.found` and `permission.denied` relationships. Apply those changes.

Click and hold the `ListFile` processor and drag an arrow to the `FetchFile` processor. A modal will appear with details of the new relationship you are creating. Click the `ADD` button.

## Add UnpackContent Processor

Add the `UnpackContent` Processor to the grid. Configure the processor so that the `failure` and `original` relationships are automatically terminated and set the `Packaging Format` property to `zip`.

Add a `success` relationship from the `FetchFile` processor to the `UnpackContent` processor.

## Add PutMarkLogicRecord Processor

Add the `PutMarkLogic` Processor to the grid. Configure the processor so that the `failure` and `success` relationships are automatically terminated. 

Set the `DatabaseClient Service` to the Controller Service we created previously in [Setup MarkLogicDatabaseClientService](#setup-marklogicdatabaseclientservice).

Set `Collections` to `iot-data`, `URI Prefix` to `/`, and `URI Suffix` to `.json`.

Set `Record Reader` to a new `JsonTreeReader` with a `Schema Access Strategy` set to `Use 'Schema Text' Property` and set `Schema Text` to the JSON below:

```javascript
{
    "name": "AllObjects",
    "type": "record",
    "fields": [
    {
        "name": "Objects",
        "type":  {
        "type":"array",
        "items":
            {
                "name": "Objects",
                "type": "record",
                "fields": [
                {
                    "name": "Object",
                    "type":
                    {
                        "name":"Object",
                        "type": "record",
                        "fields": [
                        {
                            "name": "type",
                            "type": "string"
                        },
                        {
                            "name": "id",
                            "type": "string"
                        },
                        {
                            "name": "infoItem",
                            "type":
                            {
                                "name": "infoItem",
                                "type": "record",
                                "fields": [
                                {
                                    "name": "name",
                                    "type": "string"
                                },
                                {
                                    "name": "values",
                                    "type": {
                                    "type":"array",
                                    "items":
                                    {
                                        "name": "values",
                                        "type": "record",
                                        "fields": [
                                        {
                                            "name": "value",
                                            "type": "double"
                                        },
                                        {
                                            "name": "dateTime",
                                            "type":"string",
                                            "java-class":"java.util.Date"
                                        }]
                                    }}
                                }]
                            }
                        }]
                    }
                }]
            }
        }
    }]
}
```

Set `Record Writer` as a new `JsonRecordSetWriter` and change the property `Output Grouping` to `One Line Per Object`.

Add a `success` relationship from the `UnpackContent` processor to the `PutMarkLogicRecord` processor.

For more details on the available properties, see [PutMarkLogicRecord Processor][putmarklogicrecord-processor].

## Run Ingest

Hold the `shift` key and click and drag to select all the processors on the grid. In the lower left select the <i class="fas fa-play"> Play</i> button to start ingest. 

[getting-started-page]:./getting-started
[iot-data]: ./files/IOT-Data.json.zip
[nifi-put-template]: ./files/PutMarkLogicRecordExample.xml
[putmarklogicrecord-processor]: ./nifi-features#putmarklogicrecord-processor
[nifi-exp-lang]:https://nifi.apache.org/docs/nifi-docs/html/expression-language-guide.html
