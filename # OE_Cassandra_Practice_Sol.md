# OE_Cassandra_Practice_Solution

## Prepare the dockerized Environment

Give execution permission to the bash files
```bash
chmod +x single-start.sh
chmod +x single-cli.sh
```

Create a docker env and run Cassandra
```bash
./single-start.sh
```

Start a single client
``` bash
./single-cli.sh
```

Start CQL Shell from the container
``` bash
cqlsh
```

### Inside Cassandra

create Keyspace for a slingle client
``` sql
CREATE KEYSPACE lab WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1};
```
access the "lab" environment

```sql
USE lab;
```

## Task N_1

We are required to create the table acording each query 

For this exercise we are going to use the data set ```posts.csv ```

* The following query is needed:

```sql
SELECT entry_title, content 
FROM posts 
WHERE userid = 'john doe' AND blog_title='John''s Blog' 
             AND posted_at >= '2012-01-01' AND posted_at < '2012-01-31';

```
* What should be the PRIMARY KEY?

#### Solution Task_1



```sql
CREATE TABLE posts ( 
userid text, 
blog_title text, 
posted_at timestamp, 
entry_title text, 
content text, 
category int,
PRIMARY KEY (userid, blog_title, posted_at));
```

* Based on the previously created table. Is the following query allowed

```sql
SELECT entry_title, content
 FROM posts 
WHERE userid = 'john doe' 
             AND posted_at >= '2012-01-01' AND posted_at < '2012-01-31';
```

It is not allowed, because it does not select a contiguous set of rows (and we suppose no secondary indexes are set). Needs a blog_title to be set in order to select ranges of posted_at.


* Based on the previously created table. Is the following query allowed?

```sql
SELECT * FROM posts 
WHERE userid = 'john doe' 
         AND (blog_title, posted_at) > ('John''s Blog', '2012-01-01');

```


Yes, and the following queries are also allowed

```sql
SELECT * FROM posts WHERE userid = 'john doe' AND (blog_title, posted_at) IN (('John''s Blog', '2012-01-01'), ('Extreme Chess', '2014-06-01'));
 
```

```sql
SELECT * FROM posts WHERE userid = 'john doe' AND blog_title = 'John''s Blog' AND posted_at > '2012-01-01';
```

## Task N_2

We are creating the datastore for the European Traffic Incidents Office. All incidents arrive with the following information (You can check the atrubutes/fields in the ```incidents.csv``` file)

## Fields
* Registration country code
* Registration number
* Driver PID
* Driver name
* Incident's date and time
* Incident’s location
* Penalty points (if any)

## Queries

### By Vehicle

Provide a list of incidents of a vehicle for a specific date range, sorted by incident's date and time (newest first).

* Filter by: Vehicle & incident timestamp
* Sort by: incident timestamp

## Solution 

```sql
CREATE TABLE incidents_by_vehicle (
    reg_country text,
    reg_nr text,
    driver_pid text,
    driver_name text,
    incident_time timestamp,
    incident_location text,
    penality_points int,
    PRIMARY KEY ((reg_country, reg_nr), incident_time)
)
WITH CLUSTERING ORDER BY (incident_time DESC);
```

Add data to the table: 

```sql
COPY incidents_by_vehicle (reg_nr, reg_country, driver_pid, driver_name, incident_time, incident_location, penality_points) 
FROM 'incidents.csv';
```

```sql
SELECT * FROM incidents_by_vehicle 
WHERE 
  reg_country = 'H' AND reg_nr = 'ABC-123'  
  AND incident_time >= '2020-01-01' AND incident_time < '2020-03-01'
ORDER BY incident_time DESC;
```



### By Driver

Provide the same list for a driver (identified by Driver PID), sorted by vehicle, then by date and time (newest first).

* Filter by: driver pid
* Sort by: vehicle, incident timestamp (DESC)

```sql
CREATE TABLE incidents_by_driver (
    driver_pid text,
    driver_name text static,
    reg_country text,
    reg_nr text,
    incident_time timestamp,
    incident_location text,
    penality_points int,
    PRIMARY KEY ((driver_pid), reg_country, reg_nr, incident_time)
)
WITH CLUSTERING ORDER BY (reg_country ASC, reg_nr ASC, incident_time DESC);             
```

Add data to the table: 

```sql
COPY incidents_by_driver (reg_nr, reg_country, driver_pid, driver_name, incident_time, incident_location, penality_points) 
FROM 'incidents.csv';
```

```sql
SELECT * FROM incidents_by_driver
WHERE 
  driver_pid = 'H-100';
```

## Sum Penalty Points

```sql
SELECT driver_pid, driver_name, SUM(penality_points) AS penality FROM incidents_by_driver  
WHERE driver_pid = 'H-100';
```

