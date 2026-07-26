## Using Front Door with an APEX domain registered via 3rd party registrar

In this example, I have registered a public domain azcloudhub.com with godaddy.com for S$8 for the 1st year, but I have delegated the management of this domain to Azure DNS by creating an Azure DNS zone with the same domain azcloudhub.com and then replacing the original DNS servers in godaddy.com with those from Azure. By using Azure DNS to provide name resolution for this domain, it is more closely integrated with the configuration that we are going to use on Front Door where this domain will be configured as the APEX domain for an App Service endpoint in the Front Door configuration.

> The initial steps of creating an App Service and then using it in an origin group behind a Front Door endpoint are not documented here. It is assumed that this already has been done.

**Create Azure DNS zone and obtain the DNS servers**

In this step an Azure DNS zone of azcloudhub.com is created and the 4 Azure DNS servers that are in this zone are used to replace the original ones in godaddy.com . 

It is important to add an APEX record into the Azure DNS zone of azcloudhub.com if it does not exist already. This is so that the APEX domain is available for selection from the drop down box when creating the custom domain from Azure Front Door. See below screenshot.

![APEXrecord.png](APEXrecord.png)

Replace GoDaddy's DNS servers with all four of Azure DNS servers.

![afdapexdomain2.png](afdapexdomain2.png)

As a result, godaddy's DNS configuration portal states that the management of DNS for this domain is no longer under godaddy which is expected since we are using Azure DNS instead

![afdapexdomain3.png](afdapexdomain3.png)

**Create the domain azcloudhub.com in Azure Front Door**

Here you choose Non-Azure validated domain since it has not been validated yet, select Azure-managed DNS and select the correct DNS zone and its APEX record. To have Azure generate a certificate for this APEX domain, select AFD-managed for HTTPS

![afdapexdomain4.png](afdapexdomain4.png)

**Validate the domain and associate with Front Door endpoint**

After the domain is created, click on the pending validation link and it will bring up a wizard GUI to auto-add a verification TXT record to the Azure DNS zone of azcloudhub.com . After which you associate the domain to the Azure Front Door endpoint and on the link under DNS state column click on it to have it auto-add a CNAME record for the Azure FrontDoor endpoint in the Azure DNS zone.

![afdapexdomain5.png](afdapexdomain5.png)



**Verify TXT and CNAME record on the Azure DNS zone**

If the earlier step was done correctly, you should see a validation TXT record and CNAME record added in the DNS zone. Note that the original APEX record in the domain was over-written automatically to point to Azure Front Door.

![afdapexdomain1.png](afdapexdomain1.png)



**Browse to https://azcloudhub.com and verify the certificate**

Here you should see the App Service landing page and you should not get any SSL certificate errors as Azure provided the certificate for this APEX domain.

![afdapexdomain6.png](afdapexdomain6.png)

![afdapexdomain7.png](afdapexdomain7.png)
