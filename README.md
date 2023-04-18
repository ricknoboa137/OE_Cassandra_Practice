# OE_Cassandra_Practice
##Prepare the dockerized Environment
./single-start
./single-cli


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

* Filter by: Vehicle & incident timestamp
* Sort by: incident timestamp


### By Driver

Provide the same list for a driver (identified by Driver PID), sorted by vehicle, then by date and time (newest first).

* Filter by: driver pid
* Sort by: vehicle, incident timestamp (DESC)


