## Applying NSGs to App Services in outbound virtual network integration

Network Security Groups can be applied to App Services or Function Apps when these are integrated with a virtual network in the outbound direction. However, it appears that from the NSG itself you are not able to associate it with the subnet to which to App Service is running and there is an error message that the subnet is already delegated. The workaround is to go to the subnet where the App Function is running and associate it to the NSG instead.

Error message when attaching the NSG to the subnet where App Service is running
![nsg-appsvc1.png](nsg-appsvc1.png)

Workaround - You are able to associate the subnet where App Function is running to the NSG from the virtual network UI itself
![nsg-appsvc2.png](nsg-appsvc2.png)

