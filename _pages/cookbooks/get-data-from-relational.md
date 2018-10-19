---
layout: inner
title: Retrieve Data From A Relational Source
lead_text: ''
permalink: /get-data-from-a-relational-database
---

# Retrieve Data From A Relational Source

There are several available processors for working with relational data. 

## Available Relational Processors

Descriptions for these processors were taken directly from the processor documentation. For our cookbook we will be using `GenerateTableFetch` and `ExecuteSQL` together in order to page over the result set.

### ExecuteSQL

Executes provided SQL select query. Query result will be converted to Avro format. Streaming is used so arbitrarily large result sets are supported. This processor can be scheduled to run on a timer, or cron expression, using the standard scheduling methods, or it can be triggered by an incoming FlowFile. If it is triggered by an incoming FlowFile, then attributes of that FlowFile will be available when evaluating the select query, and the query may use the ? to escape parameters. In this case, the parameters to use must exist as FlowFile attributes with the naming convention sql.args.N.type and sql.args.N.value, where N is a positive integer. The sql.args.N.type is expected to be a number indicating the JDBC Type. The content of the FlowFile is expected to be in UTF-8 format. FlowFile attribute 'executesql.row.count' indicates how many rows were selected.

### GenerateTableFetch

Generates SQL select queries that fetch "pages" of rows from a table. The partition size property, along with the table's row count, determine the size and number of pages and generated FlowFiles. In addition, incremental fetching can be achieved by setting Maximum-Value Columns, which causes the processor to track the columns' maximum values, thus only fetching rows whose columns' values exceed the observed maximums. This processor is intended to be run on the Primary Node only.

This processor can accept incoming connections; the behavior of the processor is different whether incoming connections are provided:
  - If no incoming connection(s) are specified, the processor will generate SQL queries on the specified processor schedule. Expression Language is supported for many fields, but no flow file attributes are available. However the properties will be evaluated using the Variable Registry.
  - If incoming connection(s) are specified and no flow file is available to a processor task, no work will be performed.
  - If incoming connection(s) are specified and a flow file is available to a processor task, the flow file's attributes may be used in Expression Language for such fields as Table Name and others. However, the Max-Value Columns and Columns to Return fields must be empty or refer to columns that are available in each specified table.


### QueryDatabaseTable

Generates a SQL select query, or uses a provided statement, and executes it to fetch all rows whose values in the specified Maximum Value column(s) are larger than the previously-seen maxima. Query result will be converted to Avro format. Expression Language is supported for several properties, but no incoming connections are permitted. The Variable Registry may be used to provide values for any property containing Expression Language. If it is desired to leverage flow file attributes to perform these queries, the GenerateTableFetch and/or ExecuteSQL processors can be used for this purpose. Streaming is used so arbitrarily large result sets are supported. This processor can be scheduled to run on a timer or cron expression, using the standard scheduling methods. This processor is intended to be run on the Primary Node only. FlowFile attribute 'querydbtable.row.count' indicates how many rows were selected.

## NiFi Template Example

You can download a copy of the NiFi template used for this example <a href="./files/RelationalDatabaseToMarkLogic.xml" download>here</a>.

### Setup

To use NiFi with relational you need a relational database and a JDBC driver. This cookbook will be using a dataset stored in MySql. In addition to requiring NiFi and MarkLogic setup (for instructions see [Getting Started][getting-started]), you will need the following software to follow along:

 * [MySql Server][mysql-download]
 * [MySql Connector/J][mysql-connector-j]

### Dataset

Our relational cookbook uses the sample employee database located here:
[https://github.com/datacharmer/test_db](https://github.com/datacharmer/test_db)

Follow the instructions at the linked dataset repository to load your data.

There are about 300,000 employees in the database, so we will assume that that is too big for ExecuteSQL without paging.

Run the following SQL query which will create the `employee_detail` view we will use for extracting data.

```sql
USE employees;
CREATE VIEW `employee_detail` SELECT 
employees.emp_no, employees.birth_date, employees.first_name, employees.last_name,
employees.gender, employees.hire_date, employees.dept_emp.dept_no, employees.departments.dept_name,
employees.salaries.salary, employees.titles.title
FROM 
employees.employees, employees.dept_emp, employees.departments, employees.salaries, employees.titles
WHERE 
employees.employees.emp_no = employees.dept_emp.emp_no AND
employees.dept_emp.to_date > CURDATE() AND
employees.dept_emp.dept_no = employees.departments.dept_no AND
employees.employees.emp_no = employees.salaries.emp_no AND
employees.salaries.to_date > CURDATE() AND
employees.employees.emp_no = employees.titles.emp_no AND
employees.titles.to_date > CURDATE()
```

And here's what the results form the `employee_detail` view will look like:
![Database results](./images/02-007-employee-details-results.png)

## Template Processor Setup

### GenerateTableFetch Settings

**Properties**

Database Connection Pooling Service
 : DBCPConnectionPool

Database Type
 : Generic

Table Name
 : employee_detail

Columns to Return
 : *

**Settings**

Automatically Terminate Relationships: failure

Scheduling

Run Schedule
 : 1 day (prevents infinitely looping)

### ExecuteSQL Settings

**Properties**

Database Connection Pooling Service
 : DBCPConnectionPool

SQL select query
 : *(leave this blank)*

### SplitAvro Settings

**Properties**

*(all default)*

**Settings**

Automatically Terminate Relationships
 : failure, original

### ConvertAvroToJson Settings

**Properties**

*(all default)*

**Settings**

Automatically Terminate Relationships
 : failure

### EvaluateJsonPath Settings

Store values from JSON in FlowFile properties

**Properties**

Destination
 : flowfile-attribute

emp.no
 : $.emp_no (custom property)

**Settings**

Automatically Terminate Relationships
 : failure, unmatched

### UpdateAttribute Settings

**Properties**

marklogic.uri
 : /employees/${emp.no}.json

### PutMarkLogic Settings

**Properties**

DatabaseClient Service
 : DefaultMarkLogicDatabaseClientService

Collections
 : employees

URI Attribute Name
 : marklogic.uri

Settings

Check all check boxes under "Automatically Terminate Relationships"

### Run Processors

Select each of the processors and start them.

[getting-started]: ./getting-started
[mysql-download]: https://dev.mysql.com/downloads/mysql/
[mysql-connector-j]: https://dev.mysql.com/downloads/connector/j/
