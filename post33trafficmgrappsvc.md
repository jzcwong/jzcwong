## Using Traffic Manager to load balance traffic to App Services

In this example, two App Services are created - one in East US and one in West US. Azure Traffic Manager(ewtrafficmanager.trafficmanager.net) is configured to load balance traffic to both App Services which are configured as endpoints in Traffic Manager, with equal weights of 1 each. An App Service Domain azfasttrack.com is purchased and a CNAME mapping of www.azfasttrack.com to ewtrafficmanager.trafficmanager.net is configured so that external users can access the App services using a friendly domain name of www.azfasttrack.com instead of ewtrafficmanager.trafficmanager.net.

> What is important to note about this example is that after adding both App Services as Traffic Manager endpoints, you will notice that ewtrafficmanager.trafficmanager.net gets automatically added as a custom domain with a SSL binding to both App Services. This allows users to access https://ewtrafficmanager.trafficmanager.net to reach both App Services without getting certificate errors where the CN of the presented certificate does not match the URL. In a similar way, to avoid users getting certificate error when using https://www.azfasttrack.com to access the App Services, you need to manually create custom domain and SSL binding of www.azfasttrack.com for both App Services. 

The normal steps of creating App Services and Traffic Manager are not documented here, but rather the more important points are stated in this article for the setup to work.

**Diagram of test topology**

![appsvc-tm7.png](appsvc-tm7.png)

**Adding both App Services to Traffic Manager as endpoints**

![appsvc-tm4.png](appsvc-tm4.png)

Settings within each endpoint

![appsvc-tm5.png](appsvc-tm5.png)

Health checks for the endpoints

![appsvc-tm6.png](appsvc-tm6.png)



**App Service Domain - add CNAME for www.azfasttrack.com to ewtrafficmanager.trafficmanager.net**

![appsvc-tm3.png](appsvc-tm3.png)



**Add custom domain for www.azfasttrack.com to App Service, repeat for both App Services**

Here the trick is to choose "All other domain services" instead of "Äpp Service Domain". If you choose the latter, Azure will go and configure/add in a CNAME in the azfasttrack.com DNS zone mapping www.azfasttrack.com to the App Service directly and override the mapping of www.azfasttrack.com to eastwesttrafficmanager.trafficmanager.net which is not what you want. Ignore the part about domain validation, you do not need to input these into the azfasttrack.com DNS zone. Click "Validate" and then "Add"

![appsvc-tm1.png](appsvc-tm1.png)

See that there are custom domains for both "ewtrafficmanager.trafficmanager.net" and ""www.azfasttrack.com". The former is auto-added when you add the App Service as an endpoint to Traffic Manager

![appsvc-tm2.png](appsvc-tm2.png)


**Verify access to https://www.azfasttrack.com and the certificate**

![appsvc-tm8.png](appsvc-tm8.png)

Flush the DNS cache and try access the site again, you should see it alternate to the other app service

![appsvc-tm9.png](appsvc-tm9.png)
