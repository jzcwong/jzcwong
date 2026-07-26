## Hub and Spoke virtual network peering with Virtual Network Gateway at Hub for spoke to spoke traffic

In this post, a hub and spoke vnet peering arrangement is created with a single hub and 2 spoke vnets. A virtual network gateway is created in the hub site, and spoke to spoke traffic is configured to route via the hub virtual network gateway. 

All the 3 vnets are in different resource groups, subscriptions and regions. 

> Note that vnet peering is not transitive by default. This means if vnet A peers to vnet B and vnet B peers to vnet C, it does not mean that vnet A can communicate with vnet C via vnet B.


**1. Overview of setup - Resource Groups**

3 separate resource groups are created, one for the hub vnet and two for the spoke vnets. This is to illustrate that vnet peering can happen across regions, subscriptions and resource groups

![01hubspokevgwpng](01hubspokevgw.png)

**2. Hub Resource Group items**

The hub resource groups contains the hub vnet, and the virtual network gateway.

![02hubspokevgw.png](02hubspokevgw.png)

The hub virtual network has a subnet dedicated to the virtual network gateway. This must be named GatewaySubnet and a minimum subnet size is /27

![03hubspokevgw.png](03hubspokevgw.png)

The hub virtual network gateway appears as connected to the GatewaySubnet

![04hubspokevgw.png](04hubspokevgw.png)


**3. Spoke 1 resource group items**

Spoke 1 has a vnet in Australia East and a custom route table to put in a static route for Spoke 2 via hub virtual network gateway. It also has a VM from where we use to test connectivity to Spoke 2.

![05hubspokevgw.png](05hubspokevgw.png)

Spoke 1 VM connected to the subnet in Spoke 1

![06hubspokevgw.png](06hubspokevgw.png)


**4. Spoke 2 resource group items**

Spoke 2 has a vnet in East Asia and a custom route table to put in a static route for Spoke 1 via hub virtual network gateway. It also has a VM from where we use to test connectivity to Spoke 1.

![07hubspokevgw.png](07hubspokevgw.png)

Spoke 2 VM connected to the subnet in Spoke 2

![08hubspokevgw.png](08hubspokevgw.png)


**5. Virtual Network Peering settings**

Hub vnet is peered to Spoke 1 and Spoke 2 vnets. These peerings are across subscriptions, resource-groups and regions.

![09hubspokevgw.png](09hubspokevgw.png)

Hub to spoke 1 vnet peering. Note the setting of *use this virtual network's gateway or Route Server*

![10hubspokevgw.png](10hubspokevgw.png)

Hub to spoke 2 vnet peering. Note the setting of *use this virtual network's gateway or Route Server*

![11hubspokevgw.png](11hubspokevgw.png)

Spoke 1 to Hub vnet peering. Note the setting of *use the remote virtual network's gateway or Route Server*

![12hubspokevgw.png](12hubspokevgw.png)

Spoke 2 to Hub vnet peering. Note the setting of *use the remote virtual network's gateway or Route Server*

![13hubspokevgw.png](13hubspokevgw.png)


**4. Custom Route Tables for Spoke 1 and Spoke 2**

A custom route table is created for Spoke 1 and associated with the subnet in Spoke 1, and a static route for Spoke 2 via virtual network gateway is added in this route table

![14hubspokevgw.png](14hubspokevgw.png)

A custom route table is created for Spoke 2 and associated with the subnet in Spoke 2, and a static route for Spoke 1 via virtual network gateway is added in this route table

![15hubspokevgw.png](15hubspokevgw.png)


**5. Effective routes for Spoke VMs**

Effective routes for spoke 1 VM nic shows the route for Spoke 2 via virtual network gateway

![16hubspokevgw.png](16hubspokevgw.png)

Effective routes for spoke 2 VM nic shows the route for Spoke 1 via virtual network gateway

![17hubspokevgw.png](17hubspokevgw.png)



**6. Final test to show connectivity between Spoke 1 and Spoke 2 VM**  

Able to SSH from spoke 1 to spoke 2

![18hubspokevgw.png](18hubspokevgw.png)

Able to SSH from spoke 2 to spoke 1

![19hubspokevgw.png](19hubspokevgw.png)
