## Using policy-based VPN for vnet to vnet connectivity

Azure VPN Gateways support both policy-based and route-based VPN. The former is used mainly for backward compatibility with 3rd party VPN devices that only support policy-based VPN. Unlike route-based VPN, policy-based VPN only supports IKEv1 and the Basic VPN Gateway SKU, and does not support P2S connectivity. With policy-based VPN, only a single IPSEC connection is supported.

In this example, to test the function of policy-based VPN, we establish site-to-site VPN between two virtual networks. 

> What is important to note here is that when adding the connection to the VPN gateway, choose site-to-site instead of vnet-to-vnet. This will also require the remote VPN gateway to be identifed as a local network gateway. In this example, there is a VPN Gateway in East US and a VPN Gateway in West US each attached to virtual networks in the region respectively. For each VPN gateway, a local network gateway is configured to identify it to the remote IPSEC VPN peer. For example, when adding site-to-site VPN connection to East US VPN gateway the remote peer is a local gateway represending West US VPN gateway. 

**VPN Gateway for East US**
This is configured as Basic SKU with policy-based VPN, take note of the public IP which will be used to configure the local network gateway representing it

![s2spolicyvpn-2.png](s2spolicyvpn-2.png)

**VPN Gateway for West US**
This is configured as Basic SKU with policy-based VPN, take note of the public IP which will be used to configure the local network gateway representing it

![s2spolicyvpn-3.png](s2spolicyvpn-3.png)


**IPSEC site-to-site connection for East US**

Note that status as connected, as the remote gateway is a local network gateway representing West US VPN gateway

![s2spolicyvpn-4.png](s2spolicyvpn-4.png)


**IPSEC site-to-site connection for West US**

Note that status as connected, as the remote gateway is a local network gateway representing East US VPN gateway

![s2spolicyvpn-5.png](s2spolicyvpn-5.png)


**IPSEC site-to-site connection details for East US**

![s2spolicyvpn-7.png](s2spolicyvpn-7.png)

Uses pre-shared-keys for authentication

![s2spolicyvpn-8.png](s2spolicyvpn-8.png)

Only supports IKEv1

![s2spolicyvpn-9.png](s2spolicyvpn-9.png)



**Successful connectivity between 2 VMs in East and West US via the site-to-site policy-based VPN**

![s2spolicyvpn-1.png](s2spolicyvpn-1.png)



