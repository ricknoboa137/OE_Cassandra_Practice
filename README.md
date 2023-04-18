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

We are creating the datastore for the European Traffic Incidents Office. All incidents arrive with the following information

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

##

*Hint: 

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
COPY incidents_by_driver (reg_nr, reg_country, driver_pid, driver_name, incident_time, incident_location, penality_points) 
FROM 'incidents.csv';
```

* Filter by: driver pid
* Sort by: vehicle, incident timestamp (DESC)


