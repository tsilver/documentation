# AnyLog Commands

## Overview

Commands can be issued in 2 ways:  
a) Using the AnyLog command line - AnyLog instances provide a command line interface. Users can issue commands using the command line interface.  
All the available commands are supported by the command line interface.  
b) Using a REST API - A subset of the commands are supported using a REST API.
The REST API is the main method to issue queries that evaluate data maintained by members of the network. 

## The AnyLog Command Line

The AnyLog command line is a text interface providing the ability to manage how compute instances operate, process data and metadata.  
The commands are listed and detailed below. The help command provides an interactive help on the particular commands including usage examples.    
Commands can be organized in a file and processed using the command ```script``` followed by the path and name of the script.  
Commands organized in a script can be also executed using the command ```thread```. The thread command dedicates a thread to execute the script commands and it is unusably the case for a sequence of commands that are continuously running.    
Commands can be also placed on the blockchain, identified with a unique ID and processed by calling the ID using the command ```policy```.  
This method of organizing commands allows to impact and modify behaiviour of running nodes by declaring policies on the blockchain.  
   

#### The help command

The command: ```help``` lists all the coommand options.
The command ```help``` followed by a specific command, provides information and examples on the specific command.  
Example: ```help blockchain```  provides information and command options to the command ```blockchain```.

#### Scripts, Threads and Policies

These are different methods to execute commands on the command line.

##### script
The command ```script``` folowed by a path and file name will execute all the script commands.

##### thread
The command ```thread``` folowed by a path and file name will execute all the commands that are specified in the script file.  
The command ```thread``` allocates a dedecated thread to execute the commands. This option supports commands that are executed continuously.    
An example would be a script that waits for files to be generated and process the files when identified. When the processing of the identified files is completed, the process restarts to wait for new data files. 
    
##### policy
A set of commands that are placed on the blockchhain.
When the policy is called, the commands associated with the policy are executed.  
Policies allows to initiate and modify processes on nodes by publishing processes on the blockchain.   
Policies impact one or more nodes vs. scripts which are private and maintained on the local drive of each node. 



## Set Command

The ***set*** commands allows to set variables and configuration parameters.  

Options:  

| Option        | Explanation  |
| ------------- | ------------| 
| set [variable name] = [value]  | Assigning a value to a variable. Same as: [variable name] = [value] | 
| set query mode  | Setting execution instructions to the issued queries. |
| set query log  | Initiate a log to record the executed queries. |
| set query log profile [n] seconds  | Applying the Query Log to queries with execution time higher than threshold.  |
| set new query timer | Reset the query timer. |
| set debug [on/off]  | Print the executed commands processed in scripts. |
| set debug interactive  | Waits for the user interactive command \'next\' to move to the next command. |
| set threads pool [n]  | Creates a pool of workers threads that distributes query processing to multiple threads. |
| set threads pool [n]  | Creates a pool of workers threads that distributes query processing to multiple threads. |
| set authentication [on/off]  | Enable / Disable user and message authentication. Default value is ON. |
| set anylog home [absolute path]  | Declare a path to the AnyLog data files. |


#### Set variable

To see the value assigned to a variable use exclamation point prefixed to the variable name.
<pre>
![variable name]
</pre>

To see all assigned values use the command:
<pre>
show dictionary
</pre>

#### Set query mode

The query mode sets a cap on query exection at the Operator Node by setting a limit on execution time or data volume transferred or both.
  
Params options can be the following:

| param        | Explanation  |
| ------------- | ------------| 
| timeout     | limit execution on each server by the provided time limit. | 
| max_volume  | limit data volume returned by each participating operator. |
| send_mode   | use \'all\' to return an error if any of the participating servers is not connected. |
|             | use \'any\' to send the query only to the connected servers. |
|             | The default value is \'all\'. |
| reply_mode  | use \'all\' to return an error if any of the participating servers did not reply after timeout. |
|             | use \'any\' to return the query results using the available data after timeout. |
|             | The default value is \'all\'. |

## Show Command

Returns information on variables setup and state of the node.

Options:  

| Option        | Information provided  |
| ------------- | ------------| 
| show event log  | The Last commands processed by the node. Adding a list of keywords narrows the output to log events containing the keywords. | 
| show error log  | The last commands that returned an error. Adding a list of keywords narrows the output to error events containing the keywords.|
| show file log  | The last data files processed by the node. |
| show query log  | The last queries processed by the node. Enable this log using the ***set query log*** command|
| show databases  | The list of databases managed on the local node. |
| show connections | The list of TCP and REST connections supported by the node. |
| show query mode | The query mode variables assigned by the command ***set query mode***. |
| show queries time | Statistics on queries execution time. The statistics is configurable by the command ***set query log profile [n] seconds***  |
| show watch directories | The list of the Watch directories on the node. |
| show dictionary | The list of the variable names and their assigned values. |
| show threads | The list of the threads executing users scripts. |
| show processes | The list of background processes. More details are available in [background processes](https://github.com/AnyLog-co/documentation/blob/master/background%20processes.md).|
| show synchronizer | Information on the blockchain synchronize process. |
| show scheduler | Information on the scheduled tasks. |
| show operator | Information on the Operator processes. |
| show publisher | Information on the Publisher processes. |
| show rest | Information on the REST processes. |
| show ha | Information on the High Availability processes. |
| show inserts | Statistics on inserts of data to the local database. |
| show partitions | Information on how data is partitioned on the local databases. |
| show partitions where dbms = [dbms_name] and table = [table name] | Partition details on a specific table. |
| show partitions dropped | Information on partitions which were dropped.  |
| show workers pool | Details the number of query workers assigned by the command ***set threads pool [n]***. |
| show tcp pool | Details the number TCP workers thread that execute peer command. The number of threads [n] is set by the command ***run tcp server [n]*** |
| show files [directory path] | Details the files in the specified directory |
| show directories [directory path] | Details the sub-directories in the specified directory |
| show version | The code version |
| show json file structure | Details the convention for JSON file name |

#### Show log instances with keywords

Adding keywords to the ***show log*** command - only events containing one or more of the keywords are presented.

Examples:

<pre>
show event log "SQL Error"
</pre>
Will show only log instances containing the keywords "SQL" or "Error".

<pre>
show query log "timestamp"
</pre>
If query log is enabled, only queries with "timestamp" in the SQL text will be returned.

#### show workers pool & show tcp pool
These commands returns the number of threads aligned to satisfy tasks and a flag indicating if each thread is busy executing a task or in a wait state for a new task.  
For example:
<pre>
show workers pool
</pre>
returns:
<pre>
show workers pool
</pre>
returns:
<pre>
Workers Pool with 3 workers: [0, 1, 1]
</pre>
Meaning that the first thread of the 3 is in rest while 2 threads are busy.

## Get Command

The get command provides information on hardware state and status, files, resources and security of the node. 

Options:  

| Option        | Information provided  |
| ------------- | ------------| 
| get status  | Replies with the string 'running' if the node is active. | 
| get hostname | The name assigned to the node. | 
| get disk [usage/total/used/free] [path]  | Disk statistics about the provided path. |
| get memory info | Info on the memory of the current node. The function depends on psutil installed. |
| get cpu info | Info on the CPU of the current node.  The function depends on psutil installed. |
| get ip info | Get the list of IP addresses available on the node. |
| get database size [database name] | The size of the named database in bytes. |
| get node id | Returns a unique identifier of the node. |
| get hardware id | Returns a unique identfier of the hardware. |
| get servers for dbms [dbms name] and table [table name] | The list of IPs and Ports of the servers supporting the table. |
| get servers for dbms [dbms name] | The list of IPs and Ports of the servers supporting the dbms. |
| get tables for dbms [dbms name] | The list of tables of the named database. |
| get files in [dir name] where type = [file type] and hash = [hash value] | The list of files in the specified dir that satisfy the optional filter criteria. |

Security related options:  

| Option        | Information provided  |
| ------------- | ------------| 
| get public key  | The node\'s public key. | 
| get public key using keys_file = [file name] | Retrieves the public key from the specified file. |
| get permissions | Provide the permissions for the current node using the node public key. |
| get permissions for member [member id] | The permissions for the member identified by its public key. |
| get authentication | Returns ON or OFF depending on the current status. |

## REST Command

The ***rest*** command allows to send REST requestd to a REST server. The Rest server can be an AnyLog node that provides a REST connection or a non-AnyLog node that satisfies REST requests. 

<pre>
rest [operation] where url=[url] and [option] = [value] and [option] = [value] ...
</pre>

Explanation:  
When an AnyLog node is running, it offers a REST API. The REST allows to accept REST calls from users and applications (like Grafana) to the Network members.    
Activating the REST API on a particular node is explained in [REST requests](https://github.com/AnyLog-co/documentation/blob/master/background%20processes.md#rest-requests).  
Using the REST command users can issue REST calls between members of the network and between non-members to members of the network.         
The rest call provides the target URL (of the REST server) and additional values.  
The URL must be provided, the other key value pairs are optional headers and data values.

Supported REST commands:
GET - to retrieve data and metadata from the AnyLog Network.  
PUT - to add data to the AnyLog Network. 

Examples:
<pre>
'rest get where url = http://10.0.0.159:2049 and type = info and details = "get status"\n'
'rest put where url = http://10.0.0.25:2049 and dbms = alioi and table = temperature and mode = file and body = "{"value": 50, "timestamp": "2019-10-14T17:22:13.0510101Z"}"',
</pre>


### Using REST command to retrive data from a data source

Using a ***REST GET*** command data can be pulled from a REST data source and written to a file.  
To pull data, the command results are assigned to a process that directs the data to a file as follows:
<pre>
[File and Data Format Instructions] = rest get where url=[url] and [option] = [value] and [option] = [value] ...
</pre>

File and data format instructions are key value pairs separated by the equal sign and provide several options:


| Key        | Value  |
| ---------- | -------| 
| file       | Path and File Name. |
| key        | If the GET request reurnes a dictionary, output the values of the provided key. |
| show       | True/False - designating to display status as data is written to the output file. The default value is False. |


Example:  
The following example extract data from the [PurpleAir Website](https://www2.purpleair.com/).  
The call to https://www.purpleair.com/json provides latest sensor reading which are organized as a list within a dictionary.    
The following command, pulls the dictionary using a ***GET*** call, from the dictionary the key ***results*** provides the list with the readings and the data is saved to a file called purpleair.json in the prep directory.
<pre>
[file=!prep_dir/purpleair.json, key=results, show=true] = rest get where url = https://www.purpleair.com/json
</pre>
An Example of the usage of the REST GET command is available in the section [Mapping Data to Tables](https://github.com/AnyLog-co/documentation/blob/master/mapping%20data%20to%20tables.md#purpleair-example).

 
## SQL Command

The ***sql*** command allows to execute sql statements on the data hosted by members of the network. 

<pre>
sql [dbms name] [options] [sql statement]
</pre>

Explanation:  
The SQL statement is applied to the logical database and is executed according to the options provided.  
If the sql is send to nodes in the network, there is no need to specify the target servers, the network protocol identifies all the nodes that host data for the logical database.  
The format to distribute the call to all the servers with relevant data is as follows:  

Usage:
<pre>
run client () sql [dbms name] [options] [sql statement]
</pre>
Whereas processing the query on designated servers is done by specifying the IPs and Ports of the servers inside the parenthesis as in the example below:  
<pre>
run client (ip:port, ip:port ...) sql [dbms name] [options] [sql statement]
</pre>
Nodes that receives the SQL statement will execute the statement if a logical database is declared on their node. 
To assign a logical database to a physical database use the command: ```connect dbms```.     
To view the list of logical databases on a particular node use the command: ```show databases```.  
If a logical database is not declared on the node, the node will return an error message.  
A node that issues a query needs to declare a logical database to host the query results and the name of the logical database is ***system_query***.  
An example of using SQLite as the physical database for ***system_query*** is the following:
<pre>  
connect dbms sqlite !db_user !db_port system_query memory
</pre>
The ***memory*** keyword is optional and directs the database to reside in RAM.  
The ***sqlite*** keyword can be replaced with ***psql*** to leverage PostgreSQL to host the results sets.  

### Options

Options are provided in the format ```key = value``` and multiple options are seperated by the```and``` keyword.  

| Key        | Value  |
| ---------- | -------| 
| include    | Adding data from a different database and table to the query result. The value is specified as ***dbms.table***. |
| table      | A designated table to maintain the query results. |
| dest       | Output destination like stdout (default), rest, file, none (to only updates results dbms).  |
| max_time   | Cap the query execution time.  |
| drop       | True/False - drop the output table when query starts (default is True).  |
| file       | With key and value: ```dest = file```, file path and name to accumulate the result set.  |
| format     | Output format, the default being JSON. Options: json, table |


### Example
<pre>  
run client () sql purpleair file = !prep_dir/my_data.json and dest = file and format = json "select * from readings limit 10"
</pre>

### Predefined SQL functions
Details on queries executed against time series data are available in [Queries and info requests to the AnyLog Network](https://github.com/AnyLog-co/documentation/blob/master/queries%20and%20info%20requests.md#queries-and-info-requests-to-the-anylog-network).
### Monitoring queries  
Details on profiling and monitoring queries are available in [Profiling and Monitoring Queries](https://github.com/AnyLog-co/documentation/blob/master/profiling%20and%20monitoring%20queries.md#profiling-and-monitoring-queries)

# Backup Command

The ***backup*** command transfers data from local database to a file for archival.  
If the table's data is not partitioned, the backup includes the entire table's data set.  
If the table data is partitioned, the backup operates at a partition level and can include one partition or all the partitions of the table.

Usage:
<pre>
backup table where dbms = [dbms name] and table = [table name] and partition = [partition name] and dest = [output directory]
</pre>

Explanation:

Backup the data of a particular partition or all the partitions of a table.  
Partition name is optional, if omitted all the partitions of the table participate in the backup process.  
If the table is not partitioned, the entire table participates in the backup process.
The data of each partition is written into a file at the location specified with the keyword 'dest'.    
The file data is organized in a JSON format which can be processed and ingested by a node in the network. 

Examples:
<pre>
backup table where dbms = purpleair and table = readings and dest = !bkup_dir
backup partition where dbms = purpleair and table = readings and partition = par_readings_2018_08_00_d07_timestamp and dest = !bkup_dir
</pre>

