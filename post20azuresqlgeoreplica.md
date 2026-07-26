## Azure SQL Geo-replication

In this post, geo-replication of Azure SQL is being tested. An Azure SQL server is set up on Australia-East with 2 databases - geosqldb1 and TutorialDB. Only the TutorialDB database is replicated to another Azure SQL server in Japan East, but the geosqldb1 database is not replicated. 

> Note that replication is configured per-database. A SQL server can have multiple databases, and replication can be configured selectively for each database.


**1. Overview of setup - Azure SQL server and databases**

As setting up Azure SQL server is relatively straightforward, the step-by-step details of doing so are not shown for brevity. The below resources are all created in the same resource-group. The Azure SQL server in Australia East has 2 databases - geosqldb1 and TutorialDB. The Azure SQL server in Japan has the replicated instance of TutorialDB database, but not geosqldb1 which is not configured for geo-replication. To simplify testing, the database firewalls are configured to allow access from my public IP and basic user/password for SQL authentication is being used. 

![azuresqlreplica1.png](azuresqlreplica1.png)

**2. Primary and Read-only replica**

As seen from the TutorialDB database in Australia East, it is the primary database with its read replica in Japan East

![azuresqlreplica2.png](azuresqlreplica2.png)


**3. Peform SQL query against the TutorialDB database in Australia East**

Using Azure Data Studio to connect to both the SQL servers in Australia East and Japan. For the TutorialDB in Australia East, it shows only 2 initial database entries in TutorialDB database.

![azuresqlreplica3.png](azuresqlreplica3.png)

Insert in another record into TutorialDB database in Australia East

![azuresqlreplica4.png](azuresqlreplica4.png)


**4. Read the replicated database in Japan**

The replicated database in Japan contains the updated table

![azuresqlreplica5.png](azuresqlreplica5.png)



**5. Attempt to update the TutorialDB database in Japan**

Attempting to update the TutorialDB database in Japan fails because it is read-only

![azuresqlreplica6.png](azuresqlreplica6.png)


**6. Trigger Azure SQL failover from Australia East to Japan by making SQL server in Japan the primary server**  

![azuresqlreplica7.png](azuresqlreplica7.png)

![azuresqlreplica8.png](azuresqlreplica8.png)

![azuresqlreplica9.png](azuresqlreplica9.png)

After about 1 minute, the SQL server in Japan becomes the primary server with Australia East holding the read-only replica.

![azuresqlreplica10.png](azuresqlreplica10.png)


**7. Perform insert operation on TutorialDB database in Japan SQL server and observe replication to Australia East**  

Now that the SQL server in Japan is primary, it is now writable and hence we can insert a new record into TutorialDB database in Japan and observe the replication taking place to Australia

Insert new record into TutorialDB database in Japan
![azuresqlreplica11.png](azuresqlreplica11.png)

Verify that the record was successfully inserted
![azuresqlreplica12.png](azuresqlreplica12.png)

Verify that the record was successfully replicated to Australia by querying the TutorialDB database in Australia East
![azuresqlreplica13.png](azuresqlreplica13.png)


**8. Attempt to write to TutorialDB in Australia East is unsuccessful as it is read-only**  

Now that TutorialDB database in Australia East is read-only replica, any attempts to modify it directly will not be successful

Attempt to insert new record into TutorialDB database in Australia East
![azuresqlreplica14.png](azuresqlreplica14.png)

Error message showing failure to insert new record in Australia East as it is read-only
![azuresqlreplica15.png](azuresqlreplica15.png)



