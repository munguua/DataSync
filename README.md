DataSync
========

sample program to synchronize mysql "events" table with MongoDB "events" collection, written in java

DOCUMENTATION

Problem
1) Using Java, build a data integration connector that synchs data between a MySQL database and a MongoDB database.

a. Assume you have a “EVENTS” table in MySQL with the following structure:
• EVENT_ID - bigint
• EVENT_NAME – varchar (e.g. Michael Buble’s World Tour)
• EVENT_DATETIME – bigint (e.g. new Date().getTime())
• EVENT_VENUE – varchar (e.g. Madison Square Garden)

b. Assume you have a “EVENTS” collection in MongoDB with a 1-1 transformation. For example:
• events: { “event_id”: 12029, “event_name”: “Michael Buble’s World Tour”,
“event_date”: 1403922910, “event_venue”: “Madison Square Garden” }

c. When a change occurs in the events table, this data integration connector should automatically
update the same document in the MongoDB instance.

SOLUTION METHOD

The solution uses two SQL tables; a "work" table in conjunction with the "events" table. The "work" table
contains records with data containing information required to sync a change to the MongoDB events collection.

There are three triggers in the SQL database corresponding to insert,edit and delete operations on the "events"
table. Each trigger results in a new work record, with a "completed" boolean field set to false. Also, 
the work records contain all the data fields from the event table, as well as a creation timestamp.

Meanwhile, there is a java program called "TableSync" which runs continuously.  TableSync scans the work table
for any incomplete records and processes them in order of creation, according to the operation type ("NEW", "EDIT"
"DELETE".)

The corresponding MongoDB document is then inserted, updated or deleted- and if this operation succeeds, the work
record is set to "complete" status.

TablSync is run as a java TimerTask. The main routine is in an infinite loop which
sleeps for a while and then periodically kicks off the routine to check for new work records.
(The parameters relating to how long the main routine sleeps and how often the timer kicks off can
be adjusted appropriately to the environment.)

STRUCTURE

There is an Event class which represents the event table, and a WorkRecord class which represents the work table.
Mongo operations are encapsulated in the Mongo.java class, and mySQL operations in the SQL.java class.

TESTING

There is a unit test class to test individual methods in Mongo.java and SQL.java.  

There is also an integration test
which clears the events table and runs a group of SQL operations.  The previously started TableSync demon should
then do its work to synch both data representations.

 The Mongo collection and the mySQL events table are then queried
and converted to Events objects which are compared for equality.
