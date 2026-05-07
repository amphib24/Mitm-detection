## ARP Spoofing Analysis - MITM Investigation

## Introduction

&nbsp;&nbsp;&nbsp; Adress Resolution Protocol (ARP), maps IP addresses to MAC addresses within a local network. Due to ARP lacking authentication, attackers leverage it to impersonate devices on the network.
This technique is commonly used in Man-in-the-Middle (MITM) attacks to intercept, monitor, or redirect network traffic to the attackers spoofed address.

## Scenario 
 
&nbsp;&nbsp;&nbsp; This investigation is based off the MITM Detection labs from TryHackMe's SOC 1 learning path. The challenge involves locating activity associated with a MITM attack inside a corporate LAN environment.
The lab provided a gateway IP of 192.168.10.1 which is a router, a domain of corp-login.acme.local, as well as the packet capture (pcap) file.

The investigation is focused on identifying evidence of:
  - ARP Spoofing
  - DNS Spoofing
  - SSL Stripping
This write up will cover the ARP spoofing portion of the investigation using packet analysis in Wireshark.

## Potential Indicators of Attack (IOAs)
  - Duplicate MAC-to-IP mappings
  - Unsolicited ARP replies (gratuitous ARP), meaning there is a reply with no matching request.
  - Abnormal ARP traffic volume
  - Multiple MAC Addresses associated with the gateway IP
  - Suspicious traffic redirection patterns
  - ARP probe/reply loops
## Traffic Analysis

### ARP Traffic Review

I began by filtering ARP traffic within the packet capture to ID abnormal ARP behavior.

#### Wireshark Filter Used: 
   - arp

#### Analyst Observation:
   - A total of 391 packets were identified out of 3281 total packets in the capture.

<img width="1858" height="915" alt="initial_arp_filter" src="https://github.com/user-attachments/assets/6c12061b-9b4e-4a13-bada-4c37e26714ee" />

### Gratuitous ARP Analysis

#### Wireshark Filter Used
   - arp.opcode == 2, this allowed me to ID any unsolicited ARP replies.

#### Analyst Observation:
   - Multiple gratuitous ARP replies were identified during analysis. Gratuitous ARP traffic is significant because it is commonly used by attackers to overwrite ARP cache entries on victim systems
     during MITM attacks. This activity is indicative of possible ARP spoofing activity within the network.

<img width="1862" height="910" alt="arp_opcode_2_responses" src="https://github.com/user-attachments/assets/bbe54fec-d278-475e-8e0a-99b3b92923ca" />

### Gateway ARP Activity Analysis

#### Wireshark Filter Used:
   - arp.opcode == 2 && arp.src.proto_ipv4 == 192.168.10.1, this isolated the traffic to ARP responses associated with the gateway IP address.

#### Analyst Observation:
   - Most responses mapped the gateway IP address (192.168.10.1) to the MAC address 02:aa:bb:cc:00:01 which appeared normal.
   - However, the additional responses associated with the same IP address mapped to 02:fe:fe:fe:55:55. This is suspicious because the presence of
     multiple MAC addresses associated with the gateway IP strongly suggest ARP spoofing activity. This behavior suggests an attacker-controlled
     device was attempting to impersonate the gateway in order to intercept or redirect traffic. 

<img width="1853" height="404" alt="gateway_mac_2_spoofed" src="https://github.com/user-attachments/assets/e4d1cddd-f880-4e52-830c-16388469d03a" />

### Duplicate Verification

#### Wireshark Filter Used:
   - arp.duplicate-address-detected || arp.duplicate-address-frame, this allowed me to verify my findings.

#### Analyst Observation: 
   - Wireshark identified duplicate address activity involving the spoofed MAC address, which further confirmed evidence of ARP spoofing within the network. 

<img width="1861" height="630" alt="duplicate_macs_filter" src="https://github.com/user-attachments/assets/56fead09-07aa-46f7-8972-51fee482dcb5" />

### Conclusion

&nbsp;&nbsp;&nbsp; Anlaysis of the packet capture revealed multiple indicators indicative of ARP spoofing, including:
   - Gratuitous ARP replies
   - Duplicate IP-to-MAC mappings
   - Gateway impersonation attempts
   - Duplicate address detection alerts
These findings strongly suggest that an attacker manipulated ARP traffic in order to impersonate the gateway and intercept network traffic as part of a MITM attack.

### Remediation

    1) Isolate the malicious host associated with MAC address 02:fe:fe:fe:55:55
    2) Clear ARP caches on affected hosts
    3) Enable Dynamic ARP Inspection (DAI)
    4) Implement DHCP snooping and IP-MAC binding
    5) Strengthen endpoint monitoring and detection controls



     
