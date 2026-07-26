## Routing Internet-bound traffic via a NVA in a specific vnet through vnet-peering

In this post, we have 2 separate vnets - one in Singapore and another in Australia and vnet-peering has been set up between them. A VYOS router has been deployed in the Singapore vnet and the objective is to force all Internet-bound traffic from the vnet in Australia via the VYOS router in Singapore vnet. 

For simplicity, all NSGs are set to be permissive for testing purposes only.

> Note that by default, all Azure VMs are able to access Internet (outbound to Internet from Azure) even if there is no NAT Gateway and even if the VMs do not have public IP assigned. Each Azure VM created will have a default gateway automatically configured to point to the subnet default gateway which is the 1st address in the subnet. For example, if the subnet is 10.0.0.0/24 then the default gateway for the subnet is 10.0.0.1 and this provides Internet access for the Azure VMs in the subnet via SNAT to a public IP assigned by Azure.


**1. Overview of lab setup**

Notice that the VYOS router is deployed in 2-arm mode: 1 interface facing Internet and 1 interface facing internal and both interfaces are in Singapore vnet.

![vyosvnetpeering.png](vyosvnetpeering.png)

**2. Resource Groups and Virtual Machines**

Two separate resource groups created - one for Singapore and one for Australia. The VYOS VM is created in Singapore resource group and other other VM is in Australia resource group. Note that the VMs do not have public IP addresses assigned directly at all. Access to the VMs is via serial console enabled by creating a custom storage account when the VMs are created. There is also no bastion host in the environment.

![tworesourcegrp.png](tworesourcegrp.png)

![twovms](twovms.png)


**3. vnets for Singapore and Australia**

The Singapore vnet has two subnets - one for VYOS interface to Internet and another for communicating to Australia vnet via vnet-peering

![vnet1sg](vnet1sg.png)

The Australia vnet only has a single subnet

![vnet1au](vnet1au.png)


**4. vnet Peering**

![vnetsg-aupeer](vnetsg-aupeer.png)

![vnetau-sgpeer](vnetau-sgpeer.png)


**5. VYOS interfaces and Network Security Groups**

The VYOS router has 2 interfaces - one in a subnet meant to access Internet and one to reach the Australia vnet, for the latter a static route needs to be configured as seen later. This is effectively 2-arm mode when it comes to NAT setup. Network Security Groups have allow all configured for simplified testing. 

![vyosinterface1](vyosinterface1.png)

![vyosinterface2](vyosinterface2.png)

![vyosnsg](vyosnsg.png)


**6. IP Forwarding disabled for VYOS interfaces**  
Note that IP forwarding has to be disabled for VYOS interfaces in order for it to allow transit traffic

![vyosinterface1ipforward](vyosinterface1ipforward.png)

![vyosinterface2ipforward](vyosinterface2ipforward.png)

**7. Effective routes for VYOS interfaces**  

![vyosinterface1routes](vyosinterface1routes.png)

![vyosinterface2routes](vyosinterface2routes.png)

**8. Public IP used by VYOS for Internet access**  

Note that ICMP ping cannot be used by an Azure VM to check if it has Internet access, as ICMP from Azure vnet to external is not allowed. Curl will be a better way to verify Internet connectivity and here we perform a curl to https://ifconfig.me to find out the public IP used by VYOS for Internet access

![vyospublicip](vyospublicip.png)

**9. Custom Route Table associated with Australia subnet and configured with default route pointing to VYOS interface of 10.0.1.4** 

![auroutetable](auroutetable.png)

**10. Australia VM interface, effective routes and network security group**  

Here an allow-all rule has been added at the top of the Network Security Group to simplify access, but this is not a best practice. Notice the default route to VYOS from the custom route table overrides the original one. The subnet from Australia is associated with this custom route table.

![auvminterface1](auvminterface1.png)

![auvmroutes](auvmroutes.png)

![auvmnsg](auvmnsg.png)


**11. Routing table, NAT configuration on VYOS**  

Notice a route for the Australia subnet of 10.1.0.0/24 has been added to VYOS pointing to the next-hop of the interface subnet for VYOS

![vyosroutes](vyosroutes.png)


**12. Internet access from Australia VM**  

The VM in Australia is able to ping the VYOS interface which is its default gateway. Accessing ifconfig.me shows the same public IP which is seen when VYOS accesses ifconfig.me . This shows that the VM in Australia is using VYOS to access Internet.

![auvmpublicip](auvmpublicip.png)


