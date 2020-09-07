# Using Grafana

## Overview

AnyLog users can leverage Grafana as a visualization tier using a build-in/transparent interface that maps Grafana calls to queries over data maintained in the AnyLog Network.  
Using a HTTP and JSON API, Grafana communicates with AnyLog to retrieve data such that the Grafana visualization can be leveraged.

## Prerequisites

* Grafana instance installed.
* An AnyLog Node that provides a REST connection.
To configure an AnyLog Node to satisfy REST calls, issue the following command on the AnyLog command line:  
```run rest server [ip] [port] [max time]```  
[ip] and [port] are the IP and Port that would be available to REST calls.  
[max time] is an optional value that determines the max execution time in seconds for a call before being aborted.  
A 0 value means a call would never be aborted and the default time is 20 seconds.  
 
## Establishing a connection

Open the Grafana ***Data Sources*** configuration page.

* select a JSON data source.
* On the name tab provide a unique name to the connection.
* On the URL Tab add the REST address offered by the AnyLog node (i.e. http://10.0.0.25:2049)
* On the ***Custom HTTP Headers***, name the default database to use as follows:  
```al.dbms.[dbms name]```  For example: al.dbms.lsl_demo  
Declaring the database connects Grafana to the specified database on the http connection and makes the database tables available to query.  
***Note:*** to interact with a different database, create a new JSON data source and declare a different database name in the headers.

Select the ***Save and Test*** option that should return a green banner message: ***Data source is working***.  
Failure to connect may be the result of one of the following:
* AnyLog instance is not running or not configured to support REST calls.
* Wrong IP and Port.
* Firewalls are not properly configured and make the IP and Port not available.
* AnyLog is configured with authentication detection that is not being satisfied.

## Using Grafana to visualize AnyLog data

* On the Grafana menu, use the ***+*** sign to create a new panel.
* Or, open an existing panel in Edit mode.
* The Metric window shows the list of tables supported by the database. Select a table from the options provided.

Grafana allows to present data in 2 modes:
* In a ***Time Series*** format and with reference to the time selection (on the upper right side of the panel) .
* In a ***Table*** format.

#### Using the Time Series format
The time series format collects and visualize reading data as a function of time.
AnyLog offers 2 predefined queries and users cab specify additional queries using the ***Additional JSON Data*** options on the panel.  

***The increments query*** (The default query)   
A query to the selected table for the data in the selected time range.
Depending on the number of data point requested, the query time range is divided to intervals and the min, max and average are collected for each interval and graphically presented.  
In the default behavior, AnyLog makes the best guess to determine the relevant column representing the time and the relevant value column.  
The default behaviour can be modified by updating ***Additional JSON Data*** section (on the lower left side of the panel).  

***The period query***  
A query to retrieve reading information at the end of the provided time range (or, if not available, before and nearest to the end of the time range).      
From the derived time, the query will determine a time interval that ends at the derived time and provides the avg, min and max values.    
To execute a period query, include the key: 'type' and the value: 'period' in the Additional JSON Data section.
  
 #### Using the Table format
The default behaviour shows the data provided to the ***time series format*** with the default query. 
The default behaviour can be modified by updating ***Additional JSON Data*** section (on the lower left side of the panel).

## Modifying the default behaviour

Updating the ***Additional JSON Data*** section provides additional information to the query process.  
The information provided overrides the default behaviour and can pull data from any database managed by AnyLog (as long as the user maintains valid permissions).  
The additional information is provided using a JSON script with the following attribute names:

<pre>
dbms            - The name of the logical database to use. Overrides the dbms name in the configuration page.
table           - The name of the table to use. Overrides the table name in the sql statement.
sql             - A sql statement to use.
where           - A "WHERE" condition added to the SQL statement. Can add filter or other conditions to the executed SQL.
time_column     - The name of the time column in the Time Series format.
value_column    - The name of the value column in the Time Series format.
servers         - Replacing the network determined servers with a list of destinations servers to use.
instructions    - Additional AnyLog query instructions.
</pre>


Example 1 - executing a 'period' query
<pre>
{
"type" : "period",
"time_column" : "timestamp",
"value_column" : "value",
}
</pre>

Example 2 - adding filter conditions
<pre>
{
"dbms" : "lsl_demo",
"where" : "device_name = 'EG258' or device_name = KM256",
"time_column" : "timestamp",
"value_column" : "value",
"servers" : "10.0.0.25:2048"
}
</pre>

Example 3 - changing the SQL statement to retrieve source data
<pre>
{
"dbms" : "lsl_demo",
"sql" : "select * from ping_sensor",
"time_column" : "timestamp",
"value_column" : "value",
"servers" : "10.0.0.25:2048"
}
</pre>

## Modifying the default behaviour using the Grafana Panel

A query issued using ***Time Series*** format is always bounded by the time range specified on the panel.  
Both types of queries - ***Time Series*** and ***Table*** are always bounded by ***Max data points*** that determine the number of entries returned.  
This value is configured by modifying the value ***Max data points*** in the Grafana ***Query Options*** on the panel.
 




