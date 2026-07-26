## Using Azure Front Door with custom App Service Domain for App Services

In this example, Azure Front Door is used to provide front-end caching capabilities for an App Service application. By default, Azure Front Door is accessible via a url like  https://xxxx.azurefd.net but in this example we configure a custom App Service Domain https://www.azfasttrack.com that provides access to Front Door and its backend App Service application. With a custom App Service Domain, the domain and the Name Servers providing DNS resolution are all managed within Azure. Later I will be sharing one example where the domain is registered in GoDaddy but Azure DNS Zone is used to provide Name Server resolution for the domain.

The normal steps of creating App Services and Traffic Manager are not documented here, but rather the more important points are stated in this article for the setup to work. In addition to creating Front Door and its backend origin of App Service, this example also uses an App Service Domain used to manage the DNS zone for azfasttrack.com

**DNS zone for azfasttrack.com**

Here we need to add a CNAME for www.azfasttrack.com pointing to the Azure Front Door FQDN. Note that the 2nd TXT record entry is automatically added to this DNS zone for validation after this custom domain has been added in Front Door

![afdappsvc-customdomain6.png](afdappsvc-customdomain6.png)

**Adding www.azfasttrack.com as custom domain for Azure Front Door**

Note that after adding the custom domain to the Front Door, you need to validate the association which will automatically add TXT record into the DNS zone as per earlier screenshot, and you need to associate this custom domain with the Front Door origin.

![afdappsvc-customdomain4.png](afdappsvc-customdomain4.png)

After associating with the Front Door route, you should see that the Front Door route works for both the original xxxx.azurefd.net as well as the custom domain www.azfasttrack.com 

![afdappsvc-customdomain2.png](afdappsvc-customdomain2.png)


**Accessing https://www.azfasttrack.com and viewing the certificate**

![afdappsvc-customdomain.png](afdappsvc-customdomain.png)


**Accessing https://afdappsvc-awcphufbbkffered.z01.azurefd.net and viewing the certificate**

![afdappsvc-customdomain7.png](afdappsvc-customdomain7.png)
