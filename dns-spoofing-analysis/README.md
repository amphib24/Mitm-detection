## DNS Spoofing Analysis - MITM Investigation

## Introduction

&nbsp;&nbsp;&nbsp; Domain Name System (DNS) translates human-readable domain names into computer-friendly IP addresses. DNS lacks strong authentication in many environments, which 
means it can be manipulated by attackers to redirect users to malicious domains hosted by the attacker. DNS spoofing is commonly used in MITM attacks to intercept or redirect user 
traffic.

## Scenario

&nbsp;&nbsp;&nbsp; This investigation is based off the MITM Detection labs from TryHackMe's SOC 1 learning path. The challenge involves locating activity associated with a MITM attack inside a corporate LAN environment.
The lab provided a gateway IP of 192.168.10.1 which is a router, a domain of corp-login.acme-corp.local, as well as the packet capture (pcap) file.

The investigation is focused on identifying evidence of:
  - ARP Spoofing
  - DNS Spoofing
  - SSL Stripping
This write up will cover the DNS spoofing portion of the investigation using packet analysis in Wireshark.

## Potential Indicators of Attack (IOAs)
   - Multiple DNS responses for the same query
   - DNS responses from unexpected sources
   - Suspiciously low TTL values
   - Unsolicited DNS responses (no matching request)
## Traffic Analysis

### DNS Traffic Review

I began by filtering DNS traffic within the packet capture to ID traffic specific to DNS.

#### Wireshark Filter Used: 
   - dns

#### Analyst Observation:
   - Over 1,500 packets were identified, requiring more targeted filtering for analysis.

 <img width="1860" height="855" alt="initial_dns_filter" src="https://github.com/user-attachments/assets/81eda2c7-bcd6-47a5-ac83-b419bd378a38" />

### Baseline DNS Activity

#### Wireshark Filter Used
   - dns.flags.response == 1 && ip.src == 8.8.8.8, this allowed me to filter out traffic based on legitimate DNS responses from a known resolver.

#### Analyst Observation:
   - Responses from 8.8.8.8 showed consistent TTL values and expected resolution behavior, which provided a baseline for normal DNS activity.

<img width="1852" height="857" alt="dns_respsonse_by_ip_filter" src="https://github.com/user-attachments/assets/735a5107-f4c6-48c0-a877-7e3f4a1d6e07" />

### Target Domain Analysis

#### Wireshark Filter Used: 
   - dns.flags.response == 1 && dns.qry.name == "corp-login.acme-corp.login", filters DNS responses for the target domain.
#### Analyst Observation:
   - Most responses showed TTL values ranging between 300-600 seconds, which is consistent with normal DNS behavior.

<img width="1858" height="797" alt="dns_filter_by_domain_and_response(step 3)" src="https://github.com/user-attachments/assets/83ac3d6d-683d-445c-be3e-cb927c2758e2" />

### Suspicious DNS Response

#### Wireshark Filter Used:
   - dns.flags.response == 1 && dns.qry.name == "corp-login.acme-corp.local" && ip.src != 8.8.8.8, filters responses by IP address other than the legitimate resolver.

#### Analyst Observation
   - Two suspicious DNS responses were identified:
       - Packet 1124: has a Source IP of 192.168.10.55 with a TTL of 30 seconds
       - Packet 2239: has a Source IP of 192.168.10.55 with a TTL of 25 seconds
   - These responses originated from an unexpected internal host rather than the legitimate DNS resolver of 8.8.8.8 and had significantly lower TTL values. The combination
     of DNS responses from an unauthorized source, abnormally low TTL values, and targeting of the corp-login.acme-corp.local domain, strongly suggests DNS spoofing activity within
     the network, where DNS responses are being manipulated to redirect traffic to an attacker-controlled system.

<img width="1858" height="852" alt="_malicious_ip_results" src="https://github.com/user-attachments/assets/f059002e-6d06-4705-958b-c321c7cf7470" />

<img width="922" height="482" alt="ttl_30" src="https://github.com/user-attachments/assets/b873ca4e-8c7c-42a6-a8de-289473327a65" />

<img width="920" height="481" alt="ttl_25" src="https://github.com/user-attachments/assets/d40722f2-6180-465f-a024-111f2719c1c9" />

## Conclusion

Analysis of the DNS traffic revealed multiple IOA's consistent with DNS spoofing as part of a broader MITM attack.

#### Key Indicators
   - DNS responses originating from 192.168.10.55 instead of 8.8.8.8
   - Suspiciously low TTL values
   - Consistent targeting of the internal login domain

## Remediation

    1) Isolate the malicious host 192.168.10.55
    2) Flush DNS cache on affected systems
    3) Restrict DNS resolution to approved internal resolvers only
    4) Implement network segmentation to limit lateral movement
    5) Monitor for anomalous DNS response patterns



