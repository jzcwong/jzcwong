# Using App Gateway with multi-site SSL

In this example, a single Application Gateway instance is configured for multi-site, HTTPs load-balancing. A single front-end IP is associated with the Application Gateway and two domains - www.contoso.com and www.fabrikam.com are associated with the front-end IP. 

To simulate DNS resolution from the test local computer, host file entries are added to the Windows host file for www.contoso.com and www.fabrikam.com pointing to the front-end public IP of the Application Gateway.

The important points to note:

 - a certificate is associated with the frontend IP that is not the same as the certificates on the backend pool members
 - for App GW v2, the root CA that signed the backend pool web servers must be uploaded into the App GW
 - there are 2 listeners, 2 rules, 2 backend settings and 2 custom health probes. One set for www.contoso.com and the other for www.fabrikam.com 

A diagram for this is shown below
![Overview](appgwmultisitessldiagram.png)


> The steps of creating the Application Gateway and the backend pools are not explained in detail in this post. Rather the more important points/caveats are highlighted


## Have a certificate associated with frontend IP listener

The certificate associated with frontend IP is not the same as the certificates used by the backend pool web servers. The upload of this certificate is done in the listener configuration. Also make sure that you set the listener to multi-site since you have two backends sharing the same frontend IP, and that you specify the right FQDN for each listener.

Below is the example used for the contoso listener. This is to be repeated for the fabrikam listener

![Listener](appgwmultissl10.png)



## Backend settings - upload root CA certificate and override hostname

For App GW v2, the root CA that is used to sign and issue the certificate for the backend web server must be uploaded into App GW in order for end-to-end SSL encryption to be achieved. This needs to be done for the root CA for contoso.com and fabrikam.com and is done on the backend settings

![UploadRootCA](appgwmultissl4.png)

The backend setting must also be configured to override the hostname with the appropriate FQDN for each domain

![OverrideHostname](appgwmultissl5.png)


## Health probe - specify FQDN and Path

For each of contoso.com and fabrikam.com you need to specify the hostname and path. The example shown below is for contoso.com 

![HealthProbe](appgwmultissl7.png)


## Test client settings

To simulate DNS resolving www.contoso.com and www.fabrikam.com to the App GW's frontend IP, the host file in the Windows client is edited and the following entries are added as shown. 

![WindowsClientDNS](appgwmultissl1.png)


## Test and confirm 

Browse to https://contoso.com and https://fabrikam.com from the Windows test client. Verify that pages load and analyse the certificate presented. 

![WindowsClientDNS](appgwmultissl14.png)


![WindowsClientDNS](appgwmultissl15.png)

