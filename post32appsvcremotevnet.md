## Connecting App Service to virtual network in a different region using Gateway-required virtual network integration

Integrating an App Service to a virtual network in outbound direction in the same region is straight-forward, but what happens if the virtual network is in a different region from the App Service itself? In such a case, you will need to use Point-to-Site VPN to connect the App Service to the remote virtual network. This means a Virtual Network Gateway needs to be created in the remote virtual network to terminate the P2S VPN. The VPN authentication in this case is using the App Service certificate itself.

**App Service created in East US**

![appsvc-remotevnet7.png](appsvc-remotevnet7.png)

**Remote virtual network in West US with a VM running NGINX**

![appsvc-remotevnet9.png](appsvc-remotevnet9.png)

![appsvc-remotevnet8.png](appsvc-remotevnet8.png)



**After creating the virtual network gateway in remote virtual network, add the P2S VPN configuration**
Here the important part is the certificate, here you extract the public certificate of the App Service excluding the "Begin Certificate" and "End Certificate" lines

![appsvc-remotevnet2.png](appsvc-remotevnet2.png)

![appsvc-remotevnet3.png](appsvc-remotevnet3.png)


**Create the outbound vnet integration on the App Service**

![appsvc-remotevnet1.png](appsvc-remotevnet1.png)


**Verify the connection and check that an IP address has been allocated to the App Service from the assigned pool**

![appsvc-remotevnet4.png](appsvc-remotevnet4.png)

![appsvc-remotevnet5.png](appsvc-remotevnet5.png)


**Test the connection from the App Service to the NGINX web vm running in the remote virtual network**
Here we check that we can access the NGINX web VM in the remote virtual network using its private IP address

![appsvc-remotevnet6.png](appsvc-remotevnet6.png)
