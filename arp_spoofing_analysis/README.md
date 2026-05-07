## Arp Spoofing Anlysis - MITM Investigation

## Intro

&nbsp;&nbsp;&nbsp; Adress Resolution Protocol (ARP), maps IP addresses to MAC addresses within a local network. Due to ARP lacking authenication, attackers leverage it to impersonate devices on the network.
This technique is commonly used in Man-in-the-Middle (MITM) attacks to intercept, monitor, or redirect network traffic to the attackers spoofed address.

## Sceanrio 
 
&nbsp;&nbsp;&nbsp; This investigation is based of the MITM Detection labs from TryHackMe's SOC 1 learning path. The challenge invovles locating activity associated with a MITM attack inside a corporate LAN environment. 

The investigation is focused on identifying evidence of:
  - ARP Spoofing
  - DNS Spoofing
  - SSL Stripping
This write up will cover the ARP spooing portion of the investigation using packet analysis in Wireshark.

## Potential Indicators of Attack (IOAs)
  - Duplicate MAC-to-IP mappings
  - Unsolicited ARP replies (gratuitous ARP), meaning theres a reply with no matching request.
  - Abnormal ARP traffic volume
  - Multiple MAC Addresses associated with the gateway IP
  - Supicious traffic redirection patterns
  - ARP probe/reply loops
## Traffic Analysis

### ARP Traffic Review

I began by filtering ARP traffic within the packet capture to ID abnormal ARP behvior.

<u>Wireshark filter used:<u/> arp

<u>Reuslts:U/> A total of 391 packets were identified out of 3281 total packets in the capture.

<img width="1858" height="915" alt="initial_arp_filter" src="https://github.com/user-attachments/assets/6c12061b-9b4e-4a13-bada-4c37e26714ee" />





     
