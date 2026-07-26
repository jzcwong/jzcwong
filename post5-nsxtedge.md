## How to view gateway firewall logs and session in NSX-T Edge

NSX-T Edge nodes are capable of performing stateful firewalling for uplink interfaces or centralized service ports, and this can be done on T0 or T1 routers realized on the edge nodes. The main use case is to control zone to zone traffic or to carry out additional functions like NAT. For example, you may have Tenant-A behind T1-A router and Tenant-B behind T1-B router, and using gateway firewall feature on T1-A and T1-B you can control the traffic between the 2 tenants.

In this post, I'll describe how you can view the gateway firewall logs on NSX-T Edge which comes in to be useful for troubleshooting.

**Step 1** - first enable logging for the specific firewall rule in NSX-T manager via NSX-T manager web interface. In this example, the default rule in my t0-core router is configured to drop all traffic with a white-list model and logging is enabled for the rule.

![gfw01](gfw01.png)

![gfw02](gfw02.png)

**Step 2** - Identify the active T0 instance on the edge node. For stateful firewalling, the T0/T1 routers need to be deployed in active/standby manner on the edge clusters. Initially there is no connection session.

<pre><code>edge01> <span style="color:blue">get firewall interfaces</span>
Thu Aug 19 2021 UTC 02:41:04.751
Interface           : 1c5a085f-6345-4a4c-8992-d22884a5d661
Type                : BACKPLANE
Sync enabled        : false
Name                : bp-sr0-port
VRF ID              : 3
Context entity      : 5e3f0756-8a54-4e0e-80f0-43e9f74aef22
Context name        : SR-t0-core
 
Interface           : eb841818-5337-431d-a136-10a48de639f7
Type                : LINKED
Sync enabled        : false
Name                : t0-core-t1-core-t0_lrp
VRF ID              : 2
Context entity      : aeed5466-05bb-4e11-89c8-a6ec35d1a824
Context name        : DR-t0-core
 
Interface           : <span style="color:blue">a91a35d4-acea-4843-8b0f-b4b8b5fe50a4</span>
Type                : UPLINK
Sync enabled        : false
Name                : rp-uplink
VRF ID              : 3
Context entity      : 5e3f0756-8a54-4e0e-80f0-43e9f74aef22
Context name        : SR-t0-core
 
edge01> <span style="color:blue">get firewall a91a35d4-acea-4843-8b0f-b4b8b5fe50a4 connection</span>
Thu Aug 19 2021 UTC 02:40:01.390
Connection count: 0</code></pre>


**Step 3** - You can see firewall logs using the command below

<pre><code>edge01>edge01> <span style="color:blue">get log-file syslog follow</span>
 
2021-08-19T02:37:29.031Z edge01.lab01.one NSX 5055 FIREWALL [nsx@6876 comp="nsx-edge" subcomp="datapathd" s2comp="firewallpkt" level="INFO"] <3 a91a35d4acea4843:8b0fb4b8b5fe50a4> INET reason-match DROP 1001 OUT 84 PROTO 1 172.16.20.10->172.16.5.1</code></pre>


