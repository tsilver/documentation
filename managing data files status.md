# Managing Data files

A Publisher Node sends data to one or multiple servers. A server may be down and the data may not be transferred to the server. In addition, when the data is processed by the Operator, the data load may fail.  
The status of each data file is registered on the Publisher Node and the Operator Node. Each node updates the status and satisfies queries on the status of each file.

The processes described below are optional and activation requires the relevant scripts.
  
## The structure of the data files 
The data files contain Time Series Data (TSD) that is organized in JSON format.
Each file name follows a convention that is described below:

#### The file naming convention

File name is structured as follows:
<pre>
[DBMS name].[Table Name].[ID device].[ID Operator].[Timestamp].[Hash Value of Data].[File Type]
</pre>
[Local File ID] - Sequential File ID
[DBMS name] - The logical database name      
[Table Name] - The data table name    
[ID device] - Unique ID of the data source (i.e. the device generated the data)    
[ID Publisher] - Unique ID of the publisher  
[Time] - Time assigned to the file    
[Hash Value of Data] - Unique ID of the data file  
[File Type] - Describing how data is organized (JSON/SQL)  

## The info table

Nodes can monitor the state of the data files using a local database.
* The local database name is ```almgm``` (AnyLog Management).
* The local table name is ```tsd_info``` (Time Series Data Info).

##### The structure of the ```tsd_info``` table:

* Each segment in the file name is a column in the table.
* The table includes 2 status fields which are updated as described below.
* The column ***Hash Value of Data*** is a 16 bytes number and serves as a unique key in the table.

##### The values on the status fields:

***On the Operator Node:***  
Status Field 1 is not in use.  
Status Field 2 is updated with the value 1 when the data file is loaded.

***On the Publisher Node:***  
Status Field 1 lists the Hosts and Ports to the destination servers where the data file was send.  
Status Field 2 is maintains the status for each destination server as follows:
Status 0 means the data was send  
Status 1 means the destination server processed the data. Status 1 is updated when a destination server reports on sucessful processing.

## Identifying duplicate files

When a Publisher or an Operator process a file, the Hash Value representing the file is calculated.  
If the value exists in the local database, the file is treated as a duplicate file.

The following command determines the hash value of a file:  
```file hash [file name and path]```

## Creating and Updating the ```tsd_info``` table

Managing the data data is with the following commands and processes:

* To create the management table, first connect a database to the ```almgm``` logical database.  
Example: ```'connect dbms psql anylog@127.0.0.1:demo 5432 almgm```

* Creating the ```tsd_mgm``` table:  
```create internal table tsd_mgm```  
This call creates the table with the needed columns.

* Dropping the ```tsd_mgm``` table:  
```drop internal table tsd_mgm```  
This call creates the table with the needed columns.

* Rename a file to satisfy the name convention:
```time file rename [source file path and name] dbms = [dbms name] table = [table name], device = [device ID], publisher = [publisher ID], time = [time], hash = [hash value]```  
If publisher is not provided, the IP and Port of the processing node is used.    
If hash value is not provided, the hash value if calculated.  
If time is not provided, the cyurrent date and time is used.    
The new file name can be assigned to a variable: ```new_name = time file rename ...```  

* Updating a new entry in the ```tsd_mgm``` table:   
```time file new [file name] status```

* Changing the status of a file:  
```time file update [hash value] [status]```

* Retrieving the status of a file:  
```time file get [hash value]```

* Calculating the hash value of a file:  
```file hash [path and file name]```
  
Examples:  

* For a given file, changing the file name to the convention described:  
```given_file = $HOME/AnyLog-Network/data/bkup/dweet_demo.94b54214_d0a9_4e92_8acc_e2b6675da185.2020_03_23_02_24_40_600Z.bldg_pmc.json```  
Changing the file name:  
```new_name = time file rename !given_file dbms = dweet_demo table = bldg_pmc par = 1 device = 265X48X2X34:2787 publisher = 548X23X243X12:2048```  
In this example, the hash value will be calculated and current time would be added to the file such that the new name satisfies the convention.  

* The new file name:  
```dweet_demo.bldg_pmc.1.265X48X2X34:2787.548X23X243X12:2048.2020-04-12T12:57:35Z.78eba9a0938d5f36b0a41135bf55b0e2.json```

* Updating the tsd_mgm table:  
```time file new !new_name delivered```

* Changing the status of a file:  
```time file update 78eba9a0938d5f36b0a41135bf55b0e2 Loaded```

* Retrieving the status of a file:  
```time file get 78eba9a0938d5f36b0a41135bf55b0e2```


