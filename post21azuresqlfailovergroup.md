## Azure SQL Auto Failover  Group

In this post, auto failover group of Azure SQL is being tested. Azure SQL Database is being used, which it is a fully PAAS service. An SQL Server is set up in Australia East, with failover in Japan. 

> Unlike Geo Replication, failover group is configured at the SQL server level. During the configuration of failover group, there is the option to select which database(s) are selected for failover. Auto failover group also presents a FQDN which functions like a VIP to always point to the primary SQL server, so that in the event of a failover the applications continue to point to this FQDN without having to change.


**1. Overview of setup - Azure SQL server and databases**

As setting up Azure SQL server is relatively straightforward, the step-by-step details of doing so are not shown for brevity. Azure SQL Database is used in this example, as shown below. The below resources are all created in the same resource-group. To simplify testing, the database firewalls are configured to allow access from my public IP and basic user/password for SQL authentication is being used. The sample database available from Azure is used.

![01createsqldb.png](01createsqldb.png)

**2. Create failover group**

Once the primary SQL server is created, the failover group can be configured.

![02addfailovergrp.png](02addfailovergrp.png)

Give the failover group a name, and you can create the corresponding secondary SQL server

![03createfailovergrp.png](03createfailovergrp.png)

The secondary server details, it is set up in Japan

![04newsvrfailovergrp.png](04newsvrfailovergrp.png)

The database which is selected to be included in the failover group

![05dbforfailover.png](05dbforfailover.png)


**3. Verify that the failover group is set up successfully**

From the GUI, you can see the primary and secondary SQL servers in Australia and Japan respectively.

![06failovergrpcomplete.png](06failovergrpcomplete.png)

View the FQDN for accessing the failover group

![07failovergrpcomplete1.png](07failovergrpcomplete1.png)

Check the primary and secondary SQL server

![08failovergrpcomplete2.png](08failovergrpcomplete2.png)

![12sqlsvrsummary.png](12sqlsvrsummary.png)


**4. Use Azure Data Studio to connect to SQL servers**

The FQDN of the primary and secondary SQL servers are used  here. Before this, make sure that the database firewalls are configured to allow access from your public IP.

![09azdatastudioconnect.png](09azdatastudioconnect.png)



**5. Attempt to read from the SQL servers - primary, secondary and failover group FQDN**

Read from secondary SQL server in Japan

![14readsqlsvrsecresults.png](14readsqlsvrsecresults.png)

Connect to failover group FQDN

![15connectsqlvip.png](15connectsqlvip.png)

Read from failover group FQDN

![17readsqlvipresults.png](17readsqlvipresults.png)


**6. Insert entry via failover group FQDN**  

Here we insert a new entry via the failover group FQDN. This should direct to the primary database in Australia for the insertion, which asynchronously updates the secondary database in Japan.

![18insertsqlvip.png](18insertsqlvip.png)

Read from failover group FQDN

![19readsqlvip.png](19readsqlvip.png)

Read from secondary SQL server

![20readsecsql.png](20readsecsql.png)

Read from primary SQL server

![21readprisql.png](21readprisql.png)


**7. Perform insert operation on database in Japan SQL server and observe the failure since it is read-only in secondary state**  

![22insertsecsqlfailure.png](22insertsecsqlfailure.png)



**8. Trigger failover via UI**  

Using Azure GUI to trigger failover

![27triggerfailover.png](27triggerfailover.png)

![28triggerfailover1.png](28triggerfailover1.png)

Failover in progress

![29failoverinprogress.png](29failoverinprogress.png)

Failover successful

![30failoversuccess.png](30failoversuccess.png)

![31failoversuccess1.png](31failoversuccess1.png)


**8. Perform insertion operation via failover group FQDN after failover**  

Insert via failover group FQDN

![32insertsqlvippostfailure.png](32insertsqlvippostfailure.png)

Read is successful from Japan database

![33readsecsqlpostfailure.png](33readsecsqlpostfailure.png)

Read is successful from Australia database

![34readprisqlpostfailure.png](34readprisqlpostfailure.png)


**9. Now the Australia database is secondary, any insert operation on it fails as it is read-only**  

![35insertprisqlpostfailure.png](35insertprisqlpostfailure.png)
