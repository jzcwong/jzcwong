## Azure Private DNS zones and Private DNS Resolver

In this post, I'm sharing a test setup demonstrating the use of Private DNS zones and Private DNS resolvers in Azure. As the configuration steps are straightforward and available in the documentation, I will not go into configuration details but rather I'll explain what the test setup aims to demonstrate. 

Azure DNS Private Resolver is a new service that enables you to query Azure DNS private zones from an on-premises environment and vice versa without deploying VM based DNS servers. It requires 2 interfaces – inbound to receive DNS queries and outbound to forward DNS queries. Each of these interfaces need to be placed into a minimum /28 subnet. The inbound interface of the private DNS resolver will acquire an IP address in the inbound subnet, but its outbound interface will not have an IP address.

There are subnet [restrictions](https://learn.microsoft.com/en-us/azure/dns/dns-private-resolver-overview#subnet-restrictions) and this service is only available in [specific regions](https://learn.microsoft.com/en-us/azure/dns/dns-private-resolver-overview#regional-availability)


In the test setup, there are two virtual networks peered with each other:
- One virtual network is associated with private DNS zone contoso.com and the other virtual network is associated with Private DNS zone fabrikam.com
- A private DNS resolver is created for each virtual network
- For the private DNS resolver associated with the contoso.com virtual network, a DNS forwarding ruleset is associated with its outbound interface to send all *.fabrikam.com DNS queries to the inbound interface of the Private DNS resolver for fabrikam.com 
- Similarly, for the private DNS resolver associated with the fabrikam.com virtual network, a DNS forwarding ruleset is associated with its outbound interface to send all *.contoso.com DNS queries to the inbound interface of the Private DNS resolver for contoso.com 
- In this way, a VM in the contoso.com virtual network is able to resolve *.contoso.com and *.fabrikam.com by sending DNS queries to the inbound interface of the private DNS resolver in contoso.com virtual network
- Similarly, a VM in the fabrikam.com virtual network is able to resolve *.fabrikam.com and *.contoso.com by sending DNS queries to the inbound interface of the private DNS resolver in fabrikam.com virtual network

![azurednsdiagram.png](azurednsdiagram.png)
