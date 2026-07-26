## Enabling logging for NSX-T IDPS

In this post, I'm documenting how you can enable logging when NSX-T distributed IDPS is configured in NSX-T 3.1. Many people will think that just by toggling the "Logging" button to "enable" in an IDPS rule is sufficient, but as of NSX-T 3.1 there's still an additional step to enable the logging via a RESTful API call to NSX-T manager. 

**Step 1** - first enable logging for the specific distributed IDPS rule in NSX-T manager via NSX-T manager web interface

![idps-log-ui1](idps-log-ui1.png)

![idps-log-ui2](idps-log-ui2.png)


**Step 2** - Issue GET RESTful API call to NSX-T manager at the url <em>https://nsx-manager-fqdn/api/v1/global-configs/IdsGlobalConfig</em> . Note that **"global_idsevents_to_syslog_enabled"** will be initially set to false. Also take note of the revision number
  
![get-idps](get-idps.png)
  
**Step 3** - Issue PUT RESTful API call to the same URL above to set **"global_idsevents_to_syslog_enabled"** to true and make sure you use the **same revision number**. Use JSON for the body of the POST message.

![post-idps](post-idps.png)

**Step 4** - Verify that you see the logs on the ESXi hosts where IDPS events are triggered, see /var/log/nsx-idps/nsx-idps-events.log . The logs can be exported on EVE-JSON format to an external Syslog server. This configuration is done on ESXi host itself via vCenter
  
<pre><code>[root@esx:~] cat /var/log/nsx-idps/nsx-idps-events.log | more
2021-08-10T08:45:56Z IDPS_EVT: .NET CLR 4.0.3219)","http_method":"GET","protocol":"HTTP\/1.1","length":0},"app_proto":"http","flow":{"pkts_toserver":3,"pkts_toclient":1,"bytes_toserver":492,"bytes_toclient":60,"start":"2021-08-10T08:45:39.258790+0000"}}
2021-08-10T08:45:56Z IDPS_EVT: [529171]: {"timestamp":"2021-08-10T08:45:39.289287+0000","flow_id":945057032261928,"pcap_cnt":197686507,"event_type":"alert","src_ip":"192.168.206.41","src_port":49332,"dest_ip":"178.74.213.46","dest_port":80,"proto":"TCP","nsx_metadata":{"flow_src_ip":"192.168.206.41","flow_dest_ip":"178.74.213.46","flow_dir":2,"rule_id":2024,"profile_id":"c86bc24a-0f2b-4bdb-a57c-9c7302da0253","user_id":0,"vm_uuid":"502d20d9-bd73-a714-e68f-b66a22425352"},"tx_id":0,"alert":{"action":"allowed","gid":1,"signature_id":2018644,"rev":4,"signature":"ET TROJAN Win32\/Zemot Checkin","category":"A Network Trojan was Detected","severity":1,"metadata":{"updated_at":["2019_09_28"],"former_category":["MALWARE"],"created_at":["2014_07_07"],"cvssv3":["0.0"],"cvssv2":["0.0"]}},"http":{"hostname":"oak-tureght.ru","url":"\/mod_articles-auth9565.6595\/ajax\/","http_user_agent":"Mozilla\/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident\/4.0; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.4506.2152; .NET CLR 3.5.30729; .
</code></pre>


**NOTE** - if you do not perform steps 2 and 3, you will not get any logs even though you have enabled logging via webUI of NSX-T manager
