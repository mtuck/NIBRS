 
 
## Overview
The following document will provide necessary information for a developer to understand the NIBRS database and the architecture used by Hexagon for NIBRS validation and the file creation process.  The document will describe the Federal NIBRS processes and then how the interface team needs to branch code and create data transfer scripts necessary for State Specific NIBRS implementations.  Both Federal NIBRS and State Specific NIBRS use the following 

1. NIBRS Validation Web Service 
2. NIBRS Data Transfer Service 
3. NIBRS Transfer Scripts and 
4. NIBRS Reporting Client.  These subjects will be discussed from both a Federal NIBRS and State Specific point of view


## NIBRS and WebRMS
NIBRS is an acronym used in law enforcement to describe National Incident Based Reporting.  It is how all law enforcement agencies report their crime stats to the Federal Government.  Federal NIBRS has “standard” reporting validation rules and formats that they all agencies use when reporting crime data.  
Some states, have also decided that they want to keep their own crime stats and therefore have come up with their own “State Specific NIBRS” rules and reporting formats that they use.  For these states, law enforcement agencies will report the information the state and the state will then send the information on using the Federal NIBRS rules and formats.
Because Hexagon has customers in many states, we need to support both the Federal NIBRS and State Specific NIBRS for those states that have their own.
While most agencies will use the NIBRS Reporting Client as a single place to validate incidents and create their monthly output files, NIBRS Validation can be performed on individual incident records from with the WebRMS product by going into “Edit” mode on an incident in WebRMS and clicking the “NIBRS Validate” button at the top of the incident form.
 
 ![alt text](https://github.com/mtuck/NIBRS/blob/master/img1.png?raw=true)
 ![alt text](https://github.com/mtuck/NIBRS/blob/master/img2.png?raw=true)

This will result in the incident either “Passing” NIBRS validation or the validation will return a series of Errors or Warnings about the incidents.  Incidents and can be submitted to the state with Warnings but not with Errors.  If errors are returned, the user must then take appropriate actions to fix the errors before then trying to validate the incident again.  This process must be repeated until all errors are cleared for the incident.  Below is a sample of what a validation that returns errors may look like
  

#### Behind the scenes, here is what is happening:

-	NIBRS Validate button is clicked in WebRMS
-	Data transfer scripts that are in the NIBRS database (NIB_SQL_STAGING table) are ran to move relative incident information from WebRMS database into NIBRS database
-	NIBRS Validation web service is called and either the Federal NIBRS or the State Specific NIBRS validation .dll file is ran against the data in the NIBRS database for the given incident
-	NIBRS Validation returns status of Passed, Passed with Warnings or Errors


## NIBRS Data Transfer Service
The NIBRS Data Transfer Service should be installed on the WebRMS database server where the WebRMS and NIBRS database is installed.

#### NIB_CODES
The NIBRS Data Transfer Service transfers incident information to the NIBRS Database.  When transferring the data, the services uses abbreviated codes, to collect WebRMS data, called NIB codes.  NIB codes are a set of standardized codes. (E.X 40 = Personal Weapon) These codes are specified in the federal documentation, but they can vary by state. The NIBRS codes are stored in the WebRMS database in the MASTCODE table under the NIB_CODE column. The NIB codes can also be found in the NIBRS database in the NIB_CODES table. Sometimes our clients do not configure these codes correctly causing errors.  

#### ADMIN_REC
When the Data Transfer Service transfers an incident, an admin record is created in the NIB_ADMIN table. The admin record holds some high-level information about the incident.  Most importantly it holds the ADMIN_REC of the incident.  The ADMIN_REC is how the incident will be referenced in the NIBRS database.  You can use the NIB_ADMIN table to find the ADMIN_REC for an INCIDENT_NUM.

<INSERT CONFIG FILE VALUE HERE>

## NIBRS Validation Web Service
The NIBRS Validation Web Service should be installed on the WebRMS application server where the WebRMS product and services are installed.
<INSERT CONFIG FILE VALUES HERE>

## NIBRS Data Transfer Scripts
The data transfer scripts reside in the NIBRS database in the NIB_SQL_STAGING table.  When the data transfer code is run it basically gets all the “Active” scripts where the state field value is “FD” or the state where the customer resides (i.e. Alexandria Virginia state specific scripts would have a “VA” in the state field).  Each script has an ordernum field value and the scripts are ordered by the ordernum field.  As the scripts are ran and depending on the configuration setup for logging, the exact scripts that are ran are put into the NIBRS.DEBUG_CLOB table.  These are very useful during debugging what error may have occurred or why data was transferred into the NIBRS database a certain way. The SEQUENCE_ID will correspond to the [ADMIN_REC](#admin_rec)

## NIBRS Reporting Client
The NIBRS Reporting Client can initially be installed on the WebRMS database server where the WebRMS and NIBRS databases are installed.  However, it can be installed on any machine (i.e. records personnel local machine) as long as it is configured correctly as for the database connection string and where the output files will be generated.  It would be preferred to have all the reporting client installs to point to a single share on the network so that all clients are outputting files to the same shared directory.
<INSERT CONFIG FILE VALUES HERE>


