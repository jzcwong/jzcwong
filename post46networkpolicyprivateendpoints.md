# This post aims to explain how NSG and route tables apply to private endpoints in a virtual network subnet

By default, a NSG applied to a subnet that contains private endpoints will not have effect on traffic for the private endpoint. 

To have NSG control **inbound** traffic to the private endpoints in the subnet, you have to enable NSG in the Network Policy for the subnet as show below. Note that you cannot use NSG to control outbound traffic from private endpoint, only inbound traffic since PAAS service does not originate traffic - see this [article](https://learn.microsoft.com/en-us/azure/private-link/private-endpoint-overview#nsg-more-considerations)

## Enable Network Policy for private endpoints at the subnet level
![peppolicy1.png](peppolicy1.png)

Also note that you have to reference the private IP address of the private endpoint and not service tags as the latter is a list of **public IPs** of the service maintained by Microsoft. Here you are trying to control traffic based on the private endpoint's private IP. 

## Creating NSG rule to block inbound traffic to the private endpoint of a storage account

In the below example, **10.0.0.4** is the private endpoint IP of a storage account.

![peppolicy4.png](peppolicy4.png)

Here you use the NSG rule with the IP address of the private endpoint in the rule. With this rule, you block all inbound access to the private endpoint.

![peppolicy3.png](peppolicy3.png)

## Influence inbound routing to private endpoint
If you want to influence inbound traffic to the private endpoint, for e.g. ensuring that traffic is routed to a NVA first before reaching the private endpoint, then you have to override the /32 propagated by the private endpoint with a custom route in a custom route table. By enabling Network Policy for Route Table at the subnet level, you can use a custom address prefix equal to or larger than the VNet address space to invalidate the /32 default route propagated by the private endpoint. 

In the below example, a custom route for 10.0.0.0/24 was added to point to null. Even though 10.0.0.4/24 is less specific than the private endpoint address of 10.0.0.4/32 we are able to override it because we have enabled Network Policy for Route table at the subnet level.

See this [article](https://learn.microsoft.com/en-us/azure/private-link/disable-private-endpoint-network-policy?tabs=network-policy-portal) for more information.

![peppolicy2.png](peppolicy2.png)
