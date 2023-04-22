# OE_Cassandra_Practice

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

* Based on the previously created table. Is the following query allowed?

```sql
SELECT entry_title, content
 FROM posts 
WHERE userid = 'john doe' 
             AND posted_at >= '2012-01-01' AND posted_at < '2012-01-31';
```

* Based on the previously created table. Is the following query allowed?

```sql
SELECT * FROM posts 
WHERE userid = 'john doe' 
         AND (blog_title, posted_at) > ('John''s Blog', '2012-01-01');

```

## Task N_2

We are creating the datastore for the European Traffic Incidents Office. All incidents arrive with the following information

#### The task is to create tables according each query requirements and find the proper PK to run it.

### Fields
* Registration country code
* Registration number
* Driver PID
* Driver name
* Incident's date and time
* Incident’s location
* Penalty points (if any)

### Queries

### By Vehicle

Provide a list of incidents of a vehicle for a specific date range, sorted by incident's date and time (newest first).

##

*Hint: 

```sql
SELECT * FROM incidents_by_vehicle 
WHERE 
  reg_country = 'H' AND reg_nr = 'ABC-123'  
  AND incident_time >= '2020-01-01' AND incident_time < '2020-03-01'
ORDER BY incident_time DESC;
```

```sql
COPY incidents_by_vehicle (reg_nr, reg_country, driver_pid, driver_name, incident_time, incident_location, penality_points) 
FROM 'incidents.csv';
```

* Filter by: Vehicle & incident timestamp
* Sort by: incident timestamp


### By Driver

Provide the same list for a driver (identified by Driver PID), sorted by vehicle, then by date and time (newest first).

*Hint: 

```sql
SELECT * FROM incidents_by_driver
WHERE 
  driver_pid = 'H-100'; 
```

```sql
COPY incidents_by_driver (reg_nr, reg_country, driver_pid, driver_name, incident_time, incident_location, penality_points) 
FROM 'incidents.csv';
```

* Filter by: driver pid
* Sort by: vehicle, incident timestamp (DESC)

## Sum Penalty Points

Create a query to show the total number of penalty points.

##

# Vaccination Data

Play with the COVID vaccination data found in the [country_vaccinations.csv](country_vaccinations.csv) file. 

_The original data source is available on [kaggle](https://www.kaggle.com/gpreda/covid-world-vaccination-progress). 
It was slighly cleaned (some incomplete rows were removed)._

## Hints

* Execute the following statements to create the table
  ```bash
  # Open the Cassandra CLI
  cqlsh
  ```

  ```sql
  -- Create the Cassandra keyspace
  CREATE KEYSPACE covid WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1};
  
  -- Switch to the new keyspace
  USE covid;
  
  -- Example CREATE TABLE statement
  CREATE TABLE vaccinations (
    country text,
    iso_code text,
    date1 date,
    total_vaccinations decimal,
    people_vaccinated decimal,
    people_fully_vaccinated decimal,
    daily_vaccinations_raw decimal,
    daily_vaccinations decimal,
    total_vaccinations_per_hundred decimal,
    people_vaccinated_per_hundred decimal,
    people_fully_vaccinated_per_hundred decimal,
    daily_vaccinations_per_million decimal,
    vaccines text,
    source_name text,
    source_website text,
    PRIMARY KEY (country, iso_code, date1)
  );
  -- you can try different PRIMARY KEYS
  
  -- Import data
  COPY vaccinations (
    country,iso_code,date1,total_vaccinations,people_vaccinated,people_fully_vaccinated,
    daily_vaccinations_raw,daily_vaccinations,total_vaccinations_per_hundred,people_vaccinated_per_hundred,
    people_fully_vaccinated_per_hundred,daily_vaccinations_per_million,vaccines,source_name,source_website)
  FROM 'country_vaccinations.csv'
  WITH HEADER = TRUE;
  ```

* Try to run different queries. Some of them will fail.

```sql
SELECT * FROM vaccinations WHERE country = 'Hungary';
/* 
  There will be many pages, you have to press ENTER several times 
  when the text --- MORE --- apprears. 
  Alternatively, you can press Ctrl+C to quit 
*/

SELECT * FROM vaccinations WHERE country >= 'Hungary';

SELECT * FROM vaccinations 
WHERE country = 'Hungary' AND iso_code = 'HUN' AND date1 >= '2022-03-15';
```
