## DNS Spoofing Analysis - MITM Investigation

## Introduction

&nbsp;&nbsp;&nbsp; Domain Name System (DNS) translates human-readable domain names into computer-friendly IP addresses. DNS lacks strong authentication in many environments, which 
means it can be manipulated by attackers to redirect users to malicious domains hosted by the attacker. DNS spoofing is commonly used in MITM attacks to intercept or redirect user 
traffic.

## Scenario

&nbsp;&nbsp;&nbsp; This investigation is based off the MITM Detection labs from TryHackMe's SOC 1 learning path. The challenge involves locating activity associated with a MITM attack inside a corporate LAN environment.
The lab provided a gateway IP of 192.168.10.1 which is a router, a domain of corp-login.acme.local, as well as the packet capture (pcap) file.

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
## Traffic Analysi

