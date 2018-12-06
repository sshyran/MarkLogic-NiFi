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

## PutMarkLogic Processor

Write batches of FlowFiles as documents to a MarkLogic server using the MarkLogic Data Movement SDK (DMSDK).

### Properties

DatabaseClient Service
 : The DatabaseClient Controller Service that provides the MarkLogic connection.

Batch Size
 : The number of documents per batch - sets the batch size on the Batcher.

Thread Count
 : The number of threads - sets the thread count on the Batcher.

Collections
 : Comma-delimited sequence of collections to add to each document.

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
 : A dynamic parameter with the prefix of `trans:` that will be passed to the transform.

## QueryMarkLogic Processor

Creates FlowFiles from batches of documents, matching the given criteria, retrieved from a MarkLogic server using the MarkLogic Data Movement SDK (DMSDK).

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
 : The query criteria for retrieving documents that corresponds with the `Query Type` selected.

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
 * **Metadata** adds metadata with the `meta:` prefix and properties with the `property:` prefix to the FlowFile attributes.

Collections
 : **DEPRECATED use Query Type `Collection Query` with Query instead** Comma-separated list of collections to query from a MarkLogic server.

[string-query]: https://docs.marklogic.com/guide/java/searches#id_80640
[structured-query]: https://docs.marklogic.com/guide/java/searches#id_70572
[combined-query]: https://docs.marklogic.com/guide/java/searches#id_76144
