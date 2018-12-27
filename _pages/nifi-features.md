---
layout: inner
title: NiFi Features
lead_text: ''
permalink: /nifi-features
---
# Controller Service

## MarkLogicDatabaseClientService

Provides a MarkLogic DatabaseClient instance for use by other processors.

### Properties
Host
 : The host with the REST server for which a DatabaseClient instance needs to be created

Port
 : The port on which the REST server is hosted

Load Balancer
 : Is the host specified a load balancer?

Security Context Type
 : The type of the Security Context that needs to be used for authentication. The options are:
 * DIGEST
 * BASIC
 * CERTIFICATE

Username
 : The user with read, write, or admin privileges - Required for Basic and Digest authentication

Password
 : The password for the user - Required for Basic and Digest authentication

Database
 : The database to access. By default, the configured database for the REST server would be accessed.

SSL Context Service
 : The SSL Context Service used to provide KeyStore and TrustManager information for secure connections.

Client Authentication
 : Client authentication policy when connecting via a secure connection. This property is only used when an SSL Context has been defined and enabled.

# MarkLogic Processors

## ApplyTransformMarkLogic Processor

Creates FlowFiles from batches of documents, matching the given criteria, transformed from a MarkLogic server using the MarkLogic Data Movement SDK (DMSDK).

This allows an input which can used in the `Query` property with the NiFi Expression Language.

### Relationships

#### success

FlowFiles are generated for each document URI read out of MarkLogic.

#### failure

If a query fails a FlowFile goes to the failure relationship. If an input is provided to the QueryMarkLogic processor, the input FlowFile is penalized and passed. Otherwise a new FlowFile is generated and passed.

### Properties

DatabaseClient Service
 : The DatabaseClient Controller Service that provides the MarkLogic connection.

Batch Size
 : The number of documents per batch - sets the batch size on the Batcher.

Thread Count
 : The number of threads - sets the thread count on the Batcher.

Query
 : The query criteria for retrieving documents that corresponds with the `Query Type` selected. **Expression Language Enabled: FlowFile Scope**

Query Type
 : The type of query contained in the `Query` property. Available query types:
 * **Collection Query** Comma-separated list of collections to query from a MarkLogic server.
 * **Combined Query (JSON)** Combine a string or structured query with dynamic query options (Allows JSON serialized cts queries). See [documentation for more details][combined-query].
 * **Combined Query (XML)** Combine a string or structured query with dynamic query options (Allows XML serialized cts queries). See [documentation for more details][combined-query].
 * **String Query** A Google-style query string to search documents and metadata. See [documentation for more details][string-query].
 * **Structured Query (JSON)** A simple and easy way to construct queries as a JSON structure, allowing you to manipulate complex queries.  See [documentation for more details][structured-query].
 * **Structured Query (XML)** A simple and easy way to construct queries as a XML structure, allowing you to manipulate complex queries. See [documentation for more details][structured-query].

Apply Result Type
 : Whether to REPLACE each document with the result of the transform, or run the transform with each document as input, but IGNORE the result. Default: `Replace` Available return types:
 * **Replace** Overwrites documents with the value returned by the transform, just like REST write transforms. This is the default behavior.
 * **Ignore** Run the transform on each document, but ignore the value returned by the transform because the transform will do any necessary database modifications or other processing. For example, a transform might call out to an external REST service or perhaps write multiple additional documents.

Server Transform
 : The name of REST server transform to apply to every document as it's written.

trans:*\<custom-transform-parameter\>*
 : A dynamic parameter with the prefix of `trans:` that will be passed to the transform. **Expression Language Enabled: Variable Scope**

## DeleteMarkLogic Processor

Creates FlowFiles from batches of documents, matching the given criteria, deleted from a MarkLogic server using the MarkLogic Data Movement SDK (DMSDK).

This allows an input which can used in the `Query` property with the NiFi Expression Language.

### Relationships

#### success

FlowFiles are generated for each document URI read out of MarkLogic.

#### failure

If a query fails a FlowFile goes to the failure relationship. If an input is provided to the QueryMarkLogic processor, the input FlowFile is penalized and passed. Otherwise a new FlowFile is generated and passed.

### Properties

DatabaseClient Service
 : The DatabaseClient Controller Service that provides the MarkLogic connection.

Batch Size
 : The number of documents per batch - sets the batch size on the Batcher.

Thread Count
 : The number of threads - sets the thread count on the Batcher.

Query
 : The query criteria for retrieving documents that corresponds with the `Query Type` selected. **Expression Language Enabled: FlowFile Scope**

Query Type
 : The type of query contained in the `Query` property. Available query types:
 * **Collection Query** Comma-separated list of collections to query from a MarkLogic server.
 * **Combined Query (JSON)** Combine a string or structured query with dynamic query options (Allows JSON serialized cts queries). See [documentation for more details][combined-query].
 * **Combined Query (XML)** Combine a string or structured query with dynamic query options (Allows XML serialized cts queries). See [documentation for more details][combined-query].
 * **String Query** A Google-style query string to search documents and metadata. See [documentation for more details][string-query].
 * **Structured Query (JSON)** A simple and easy way to construct queries as a JSON structure, allowing you to manipulate complex queries.  See [documentation for more details][structured-query].
 * **Structured Query (XML)** A simple and easy way to construct queries as a XML structure, allowing you to manipulate complex queries. See [documentation for more details][structured-query].

## ExecuteScriptMarkLogic Processor

Executes server-side code in MarkLogic, either in JavaScript or XQuery. Code can be given in a Script Body property or can be invoked as a path to a module installed on the server.

### Relationships

#### results

FlowFiles are generated for each script result.

#### first result

FlowFile is generated for first script result.

#### last result

FlowFile is generated for last script result.

#### original

Input FlowFile is passed to this relationship.

#### failure

Input FlowFile is passed to this relationship, if failure occurs.

### Properties

DatabaseClient Service
 : The DatabaseClient Controller Service that provides the MarkLogic connection.

Execution Type
 : What will be executed: ad-hoc XQuery or JavaScript, or a path to a module on the server:
 * **XQuery** Execute XQuery supplied in the Script Body property.
 * **JavaScript** Execute JavaScript supplied in the Script Body property.
 * **Module Path** Execute the module specified in the Module Path property.

Script Body
 : Body of script to execute. Only one of Module Path or Script Body may be used. **Expression Language Enabled: FlowFile Scope**

Module Path
 : Path of module to execute. Only one of Module Path or Script Body may be used. **Expression Language Enabled: FlowFile Scope**

Results Destination
 : Where each result will be written in the FlowFile. If Attribute, the result will be written to the `marklogic.result` attribute:
 * **Content** Write the MarkLogic result to the FlowFile content.
 * **Attribute** Write the MarkLogic result to the marklogic.result attribute.
 * **Attributes from JSON Properties** Parse a MarkLogic JSON result into attributes with the same names as the top-level JSON properties, where the values are simple types, not objects or arrays.

Skip First Result
 : If true, first result is not sent to results relationship or last result relationship, but is sent to the first result relationship.

Content Variable
 : The name of the external variable where the incoming content will be sent to the script. (optional) **Expression Language Enabled: FlowFile Scope**

*\<custom-external-variable\>*
 : A dynamic parameter that will be passed as an external variable. **Expression Language Enabled: Variable Scope**


## ExtensionCallMarkLogic Processor

Allows MarkLogic REST extensions to be called.

### Relationships

#### success

FlowFiles are generated for each document URI read out of MarkLogic.

#### failure

If a REST call fails, a FlowFile goes to the failure relationship.

### Properties

DatabaseClient Service
 : The DatabaseClient Controller Service that provides the MarkLogic connection.

Extension Name
 : Name of MarkLogic REST extension.

Requires Input
 : Whether a FlowFile is required to run.

Payload Source
 : Whether a payload body is passed and if so, from the FlowFile content or the Payload property.
 * **None** No paylod is passed to the request body.
 * **FlowFile Content** The FlowFile content is passed as a payload to the request body.
 * **Payload Property** The Payload property is passed as a payload to the request body.

Payload Format
 : Format of request body payload.
 * **XML**
 * **JSON**
 * **TEXT**
 * **BINARY**
 * **UNKNOWN**

Payload
 : Payload for request body if "Payload Property" is the selected Payload Type. **Expression Language Enabled: FlowFile Scope**

Method Type
 : HTTP method to call the REST extension with.
 * **GET**
 * **DELETE**
 * **POST**
 * **PUT**

param:*\<custom-extension-parameter\>*
 : A dynamic parameter with the prefix of `param:` that will be passed to the REST extension. **Expression Language Enabled: FlowFile Scope**

separator:param:*\<custom-extension-parameter\>*
 : A dynamic parameter with the prefix of `separator:` can reference a way to split values in a `param:` property (e.g., Multiple `uri` parameters can be set with `param:uri` => `uri1.json,uri2.json` and `separator:param:uri` => `,`). **Expression Language Enabled: FlowFile Scope**

## PutMarkLogic Processor

Write batches of FlowFiles as documents to a MarkLogic server using the MarkLogic Data Movement SDK (DMSDK).

### Relationships

#### success

FlowFiles that have been written successfully to MarkLogic are passed to this relationship.

#### batch_success

A FlowFile is created and written to this relationship for each batch. The FlowFile has an attribute of URIs, which is a comma-separated list of URIs successfully written in a batch. This can assist with post-batch processing.

#### failure

FlowFiles that have failed to be written to MarkLogic are passed to this relationship.

### Properties

DatabaseClient Service
 : The DatabaseClient Controller Service that provides the MarkLogic connection.

Batch Size
 : The number of documents per batch - sets the batch size on the Batcher.

Thread Count
 : The number of threads - sets the thread count on the Batcher.

Collections
 : Comma-delimited sequence of collections to add to each document. **Expression Language Enabled: FlowFile Scope**

Format
 : Format for each document; if not specified, MarkLogic will determine the format based on the URI.

Job ID
 : ID for the WriteBatcher job.

Job Name
 : Name for the WriteBatcher job.

MIME Type
 : MIME type for each document; if not specified, MarkLogic will determine the MIME type based on the URI.

Permissions
 : Comma-delimited sequence of permissions - role1, capability1, role2, capability2 - to add to each document

Temporal Collection
 : The temporal collection to use for a temporal document insert.

Server Transform
 : The name of REST server transform to apply to every document as it's written.

URI Attribute Name
 : The name of the FlowFile attribute whose value will be used as the URI.

URI Prefix
 : The prefix to prepend to each URI.

URI Suffix
 : The suffix to append to each URI.

trans:*\<custom-transform-parameter\>*
 : A dynamic parameter with the prefix of `trans:` that will be passed to the transform. **Expression Language Enabled: Variable Scope**

## PutMarkLogicRecord Processor

Breaks down FlowFiles into batches of Records and inserts JSON documents to a MarkLogic server using the MarkLogic Data Movement SDK (DMSDK).

### Relationships

#### success

FlowFiles that have been written successfully to MarkLogic are passed to this relationship.

#### batch_success

A FlowFile is created and written to this relationship for each batch. The FlowFile has an attribute of URIs, which is a comma-separated list of URIs successfully written in a batch. This can assist with post-batch processing.

#### failure

FlowFiles that have failed to be written to MarkLogic are passed to this relationship.

### Properties

DatabaseClient Service
 : The DatabaseClient Controller Service that provides the MarkLogic connection.

Batch Size
 : The number of documents per batch - sets the batch size on the Batcher.

Thread Count
 : The number of threads - sets the thread count on the Batcher.

Record Reader
 : The Record Reader to use for incoming FlowFiles.

Record Writer
 : The Record Writer to use for creating new FlowFiles.

Collections
 : Comma-delimited sequence of collections to add to each document. **Expression Language Enabled: FlowFile Scope**

Format
 : Format for each document; if not specified, MarkLogic will determine the format based on the URI.

Job ID
 : ID for the WriteBatcher job.

Job Name
 : Name for the WriteBatcher job.

MIME Type
 : MIME type for each document; if not specified, MarkLogic will determine the MIME type based on the URI.

Permissions
 : Comma-delimited sequence of permissions - role1, capability1, role2, capability2 - to add to each document

Temporal Collection
 : The temporal collection to use for a temporal document insert.

Server Transform
 : The name of REST server transform to apply to every document as it's written.

URI Field Name
 : The name of the record field whose value will be used as the URI. If not specified, a UUID will be generated.

URI Prefix
 : The prefix to prepend to each URI.

URI Suffix
 : The suffix to append to each URI.

trans:*\<custom-transform-parameter\>*
 : A dynamic parameter with the prefix of `trans:` that will be passed to the transform. **Expression Language Enabled: Variable Scope**

## QueryMarkLogic Processor

Creates FlowFiles from batches of documents, matching the given criteria, retrieved from a MarkLogic server using the MarkLogic Data Movement SDK (DMSDK).

This allows an input which can used in the `Query` property with the NiFi Expression Language.

### Relationships

#### success

FlowFiles are generated for each document URI read out of MarkLogic.

#### failure

If a query fails a FlowFile goes to the failure relationship. If an input is provided to the QueryMarkLogic processor, the input FlowFile is penalized and passed. Otherwise a new FlowFile is generated and passed.

### Properties

DatabaseClient Service
 : The DatabaseClient Controller Service that provides the MarkLogic connection.

Batch Size
 : The number of documents per batch - sets the batch size on the Batcher.

Thread Count
 : The number of threads - sets the thread count on the Batcher.

Consistent Snapshot
 : Boolean used to indicate that the matching documents were retrieved from a consistent snapshot.

Query
 : The query criteria for retrieving documents that corresponds with the `Query Type` selected. **Expression Language Enabled: FlowFile Scope**

Query Type
 : The type of query contained in the `Query` property. Available query types:
 * **Collection Query** Comma-separated list of collections to query from a MarkLogic server.
 * **Combined Query (JSON)** Combine a string or structured query with dynamic query options (Allows JSON serialized cts queries). See [documentation for more details][combined-query].
 * **Combined Query (XML)** Combine a string or structured query with dynamic query options (Allows XML serialized cts queries). See [documentation for more details][combined-query].
 * **String Query** A Google-style query string to search documents and metadata. See [documentation for more details][string-query].
 * **Structured Query (JSON)** A simple and easy way to construct queries as a JSON structure, allowing you to manipulate complex queries.  See [documentation for more details][structured-query].
 * **Structured Query (XML)** A simple and easy way to construct queries as a XML structure, allowing you to manipulate complex queries. See [documentation for more details][structured-query].

Return Type
 : The type of data that is returned. Default: `Documents` Available return types:
 * **URIs Only** Passes FlowFiles with just `filename` attribute with the matching document URIs.
 * **Documents** Adds document in FlowFile content.
 * **Documents + Metadata** Adds document in FlowFile content and adds metadata with the `meta:` prefix and properties with the `property:` prefix to the FlowFile attributes.
 * **Metadata** Adds metadata with the `meta:` prefix and properties with the `property:` prefix to the FlowFile attributes.

Collections
 : **DEPRECATED use Query Type `Collection Query` with Query instead.** Comma-separated list of collections to query from a MarkLogic server.

Server Transform
 : The name of REST server transform to apply to every document as it's read.

trans:*\<custom-transform-parameter\>*
 : A dynamic parameter with the prefix of `trans:` that will be passed to the transform. **Expression Language Enabled: Variable Scope**

[string-query]: https://docs.marklogic.com/guide/java/searches#id_80640
[structured-query]: https://docs.marklogic.com/guide/java/searches#id_70572
[combined-query]: https://docs.marklogic.com/guide/java/searches#id_76144
