---
layout: inner
title: NiFi Features
lead_text: ''
permalink: /nifi-features
---
# Controller Service

## MarkLogicDatabaseClientService

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

## QueryMarkLogic Processor

### Properties

DatabaseClient Service
 : The DatabaseClient Controller Service that provides the MarkLogic connection.

Batch Size
 : The number of documents per batch - sets the batch size on the Batcher.

Thread Count
 : The number of threads - sets the thread count on the Batcher.

Consistent Snapshot
 : Boolean used to indicate that the matching documents were retrieved from a consistent snapshot.

Collections
 : Comma-separated list of collections to query from a MarkLogic server.


