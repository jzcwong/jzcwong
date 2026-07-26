## Hub and Spoke virtual network peering with Route Server at Hub for spoke to spoke traffic

In this post, a hub and spoke vnet peering arrangement is created with a single hub and 2 spoke vnets. A Route Server is created in the hub site, and spoke to spoke traffic is configured to route via the hub Route Server.

All the 3 vnets are in different resource groups, subscriptions and regions. 

> Note that vnet peering is not transitive by default. This means if vnet A peers to vnet B and vnet B peers to vnet C, it does not mean that vnet A can communicate with vnet C via vnet B.

Normally the purpose of a Route Server is to peer via BGP with an upstream NVA like firewall, and then have the NVA route traffic between spokes and perform security inspection at the same time. In this example, in the absence of NVA the Route Server is configured to perform the spoke to spoke traffic routing. See https://docs.microsoft.com/en-gb/azure/route-server/overview for more information on Azure Route Server. 

> It is observed that the Route Server is actually implemented as a VM Scale Set, and it must reside in a pre-defined subnet call RouteServerSubnet that is at least /27 in size.


**1. Overview of setup - Resource Groups**

3 separate resource groups are created, one for the hub vnet and two for the spoke vnets. This is to illustrate that vnet peering can happen across regions, subscriptions and resource groups

![01hubspokers](01hubspokers.png)

**2. Overview of the virtual networks**

One hub vnet, and two spoke vnets are created in different regions. Peering takes place between hub and spokes only, as shown later.

![02hubspokers](02hubspokers.png)


**3. Hub vnet**

Within the Hub vnet, a RouteServerSubnet is created within which the Route Server VM scale set is deployed. The example shows it has a /24, at minimum it should be /27

![03hubspokers](03hubspokers.png)

Route Server VM Scale Set deployed onto the RouteServerSubnet

![04hubspokers](04hubspokers.png)


**4. Spoke vnets and connected VMs**

Spoke 1 vnet shown below. The subnet in it is part of a custom route table used to define a route for spoke 2 via route server later on.

![05hubspokers](05hubspokers.png)

Spoke 1 VM connected to the subnet

![06hubspokers](06hubspokers.png)

In a similar way, spoke 2 vnet has a subnet that is placed into a custom route table to define a static route for spoke 1 via hub's Route Server. Below shows the connected VM in spoke 2 subnet.

![08hubspokers](08hubspokers.png)

**5. Route Server settings**

Route Server must be configured to allow branch to branch traffic

![09hubspokers](09hubspokers.png)


**6. vnet peering settings**

vNet peerings at the hub

![10hubspokers](10hubspokers.png)


Hub to spoke 1 vnet peering. Note the setting of *use this virtual network's gateway or Route Server*

![11hubspokers](11hubspokers.png)

Hub to spoke 2 vnet peering. Note the setting of *use this virtual network's gateway or Route Server*

![12hubspokers](12hubspokers.png)

Although not shown, the spoke peering's to hub will have a *use the remote virtual network's gateway or Route Server* setting enabled.

**7. Route table configuration**

Two route tables configured - one for spoke 1 and the other for spoke 2

![13hubspokers](13hubspokers.png)

Static route at spoke 1 route table, pointing spoke 2 destination via hub Route Server.

![14hubspokers](14hubspokers.png)

Static route at spoke 2 route table, pointing spoke 1 destination via hub Route Server.

![15hubspokers](15hubspokers.png)



**4. Effective routes at the vnic of VMs at spoke 1 and spoke 2**

Spoke 1 VM nic effective routes

![16hubspokers](16hubspokers.png)

Spoke 2 VM nic effective routes

![17hubspokers](17hubspokers.png)


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
