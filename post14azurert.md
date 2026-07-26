## Using a custom route table to force traffic in a vnet to a network virtual appliance

This post describes a simple example of how you can use a custom route table to force traffic going out of the vnet to Internet to first traverse a network virtual appliance. By default every subnet within a virtual network has a default route to the Internet and workloads on the subnet can access the Internet directly. This is not a desired behaviour in many cases since most organisations want to control Internet access for their workloads in the cloud. This example shows how this can be done using a custom route table. The network virtual appliance deployed here is VYOS router that performs basic NAT for the egress traffic to Internet. In actual deployments organizations will want to use a true Firewall.

For simplicity, all NSGs are set to be permissive for testing purposes only.


**1. Overview of lab setup**

Notice that the VYOS router is deployed in 2-arm mode: 1 interface facing Internet and 1 interface facing internal

![azure1rt](azure1rt.png)  

![azure10rt](azure10rt.png)  


**2. Custom Route table**

The custom route table is associated with vnet1-subnet1 and has a default route pointing to VYOS interface IP in the same subnet.

![azure2rt](azure2rt.png)


**3. Effective routes for Ubuntu VM NIC**

Notice the default route is to 10.0.0.5 which is the VYOS interface IP in vnet1-subnet1, and the original default route is inactive

![azure3rt](azure3rt.png)


This seems to override the Ubuntu VM's routing table, which still points to 10.0.0.1. A test using "curl ifconfig.me" shows the public IP which is VYOS public IP, confirming that traffic to Internet is forced through VYOS

![azure9rt](azure9rt.png)

**4. VYOS interface with public IP**

![azure4rt](azure4rt.png)

**5. VYOS interface with private IP facing internal subnet**

![azure5rt](azure5rt.png)


**6. Effective routes for VYOS internal interface**  
Although the default route now points to itself 10.0.0.5, it does not seem to affect the routing or cause any recursive routing. 

![azure6rt](azure6rt.png)

**6. Effective routes for VYOS Internet-facing interface**  

![azure7rt](azure7rt.png)


**7. Interfaces, routing table and NAT configuration on VYOS**  


![azure8rt](azure8rt.png)




