# Microsoft Entra Private Access - basic test setup

In this example, [Global Secure Access](https://learn.microsoft.com/en-gb/azure/global-secure-access/overview-what-is-global-secure-access) is used to provide private secured access to an IIS web server in a virtual network that is used to simulate an on-premises network. Also in the same *on-premises network* is a [Connector](https://learn.microsoft.com/en-gb/azure/global-secure-access/how-to-configure-connectors) which is essentially a Windows Server that has Proxy connector software installed. One or more connectors can be deployed on-premises to be part of a [Connector Group](https://learn.microsoft.com/en-us/azure/active-directory/app-proxy/application-proxy-connector-groups) for high availability and for separating different groups of applications for access. The Connector will make outbound **HTTP 80 (only for downloading CRLs if OCSP is not supported)** and HTTPS 443 connections to Microsoft Cloud.

The remote client is a VM in another virtual network that has the Global Secure Access (GSA) client installed. This VM **must** be Azure AD joined, not just registered. The GSA client will make outbound encrypted HTTP/2 GRPC connections to Microsoft Cloud. 

![Entra Private Access](entraprivateaccess.png)

## Advantages
- there is no need to open up inbound ports on the on-premise network to allow remote clients to access the resources within it
- part of the traffic is carried out Microsoft Global Backbone which spans across 61 regions and 185 global points-of-presence, with mesh edge nodes around the world to optimally connect users
- there is no need to manage VPN tunnels, VPN address pools
- you can easily layer on additional security capabilities like conditional access, multi-factor authentication for remote application access

## Pre-requisites
The following are pre-requisites for the solution:

 - Entra Premium P1 licenses 
 - Azure-AD **joined** remote clients which must be 64bit Windows 10 or Windows 11
 - Minimum Windows 2012R2 server or later with minimum .NET v4.7.1+ to intall the Application Proxy Connector
 - [Global Secure Administrator](https://learn.microsoft.com/en-us/azure/active-directory/roles/permissions-reference#global-secure-access-administrator) to perform required configuration on Entra Admin portal

### Step 1 - Download the application proxy connector from Entra Admin Center
The Entra Admin Center is accessible at https://entra.microsoft.com and you have to log in with your credentials as a user with at least Global Secure Administrator role. Download the application proxy connector and note the installation pre-requisites.

![entraprivateaccess1.png](entraprivateaccess1.png)

### Step 2 - install the application proxy connector
Before installing application proxy connector, first disable IE security configuration. 

![entraprivateaccess5.png](entraprivateaccess5.png)

During installation you'll be prompted to sign into Entra

![entraprivateaccess6.png](entraprivateaccess6.png)

After successful login, the installation of application proxy connector should be successful.

![entraprivateaccess8.png](entraprivateaccess8.png)

### Step 3 - Verify successful application proxy connector installation
You can check on Entra admin center that the connector is registered.

![entraprivateaccess9.png](entraprivateaccess9.png)

You can also check on the Windows services of the host that the connector is running.

![entraprivateaccess10.png](entraprivateaccess10.png)

### Step 4 - Create connector group and add the connector to it
Here we create a connector group and add the connector to it. We also verify that the connector status is Active

![entraprivateaccess11.png](entraprivateaccess11.png)

Connector group with connector status as Active. Notice the recommendation to have at least 2 connectors in a group. Since this is for testing we will only use 1 connector

![entraprivateaccess12.png](entraprivateaccess12.png)

### Step 5 - Create new enterprise application and add application segment, followed by adding Azure AD user to the application
Here we create a new application to represent the on-premise IIS web server. Associate it with the connector group created earlier and define the IP/port of the on-premise IIS webserver in the application segment. 

![entraprivateaccess29.png](entraprivateaccess29.png)

After the enterprise application is created, click on it and go to ***Users and groups*** to add the users or groups that are allowed to use the application

![entraprivateaccess19.png](entraprivateaccess19.png)

### Step 6 - Enable Private Access Profile traffic forwarding

From Entra Admin portal, enable Private Access Profile traffic forwarding and check that it is linked to the Enterprise application created

![entraprivateaccess30.png](entraprivateaccess30.png)

### Step 7 - Join the remote access client to Azure AD

On the remote access client, go to ***Set up work or school account*** and the choose the option of ***Join this device to Azure Active Directory***. Follow the rest of the steps in the wizard making sure you sign in as the user who is allowed to use the enterprise application.

![entraprivateaccess22.png](entraprivateaccess22.png)

View the successful joining of the device to Azure AD

![entraprivateaccess23.png](entraprivateaccess23.png)

On AzureAD, the device is shown as ***Azure AD Joined***

![entraprivateaccess24.png](entraprivateaccess24.png)

### Step 8 - Download the Global Secure Access client from Entra Admin portal and install on the remote AzureAD-joined client

![entraprivateaccess20.png](entraprivateaccess20.png)

The GSA client installation is straightforward, just launch the installer on the remote access client and follow the step by step instructions. 

![entraprivateaccess21.png](entraprivateaccess21.png)

### Step 9 - Test access to IIS web server from remote access client

At this stage, you will be able to launch the GSA client which will acquire traffic to the IIS web server 10.0.0.5 on TCP port 80. Browsing to http://10.0.0.5 on the remote access client should bring up the page for the IIS web server. 

![entraprivateaccess25.png](entraprivateaccess25.png)

![entraprivateaccess26.png](entraprivateaccess26.png)


#### Useful links
- [Microsoft Entra Expands into Security Service Edge with Two New Offerings](https://techcommunity.microsoft.com/t5/microsoft-entra-azure-ad-blog/microsoft-entra-expands-into-security-service-edge-with-two-new/ba-p/3847829)
- [Microsoft Entra Private Access: An Identity-Centric Zero Trust Network Access Solution](https://techcommunity.microsoft.com/t5/microsoft-entra-azure-ad-blog/microsoft-entra-private-access-an-identity-centric-zero-trust/ba-p/3905451)


