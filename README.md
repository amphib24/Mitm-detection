# SOC Investigation Report - MITM-Detection

## Demonstrated Skills

   - Wireshark Packet Capture Analysis
   - MITM Detection
   - ARP Spoofing Detection
   - DNS Traffic Analysis
   - SSL Stripping Identification
   - IOA/IOC Identification
   - Network Traffic Investigation
   - Threat Anlaysis
   - Incident Documentation

# Executive Summary

&nbsp;&nbsp;&nbsp; This investigation documents the analysis of a simulated MITM attack on Acme-Coprs corporate LAN as part of TryHackMe's SOC 1 learning path. The investigation was conducted using Wireshark for packet analysis , which focused on identifying IOAs, validating malicious traffic paterns, and documenting the evidence found as it correlates to the attack chain.
Packet capture analysis revealed a chain attack which involved:
  1) ARP Spoofing
  2) DNS Spoofin
  3) SSL Stripping

The attacker utilized ARP spoofing to establish their position between the client systems and the gateway, then manipulated DNS responses to redirect the victim to a malicious host, and downgraded HTTPS traffic to HTTP traffic, leading to 
credential interception. 

# Environment Overview

   - Gateway IP : 192.168.10.1
   - Suspected Malicious Host : 192.168.10.55
   - Target Domain : corp-login.acme-corp.local
   - Analysis Tool Used: Wireshark
   - Data source : network-traffic.pcap

 # Attack Chain Overview

ARP Spoofing -> Traffic Interception -> DNS Spoofing -> Victim Redirect -> SSL Stripping -> Credential Capture

# Findings

## ARP Spoofing 
 Anlaysis of the packet capture revealed multiple indicators indicative of ARP spoofing, including:

 - Gratuitous ARP replies
 - Duplicate IP-to-MAC mappings
 - Gateway impersonation attempts
 - Duplicate address detection alerts These findings strongly suggest that an attacker manipulated ARP traffic in order to impersonate the gateway and intercept network traffic as part of a MITM attack.
 - Suspicious MAC address 02:fe:fe:fe:55
#### Technical Analysis 
<a href = https://github.com/amphib24/MITM-detection/tree/main/arp-spoofing-analysis>Analysis</a>

## DNS Spoofing
Analysis of the DNS traffic revealed multiple IOA's consistent with DNS spoofing as part of a broader MITM attack, including:

   - DNS responses originating from 192.168.10.55 instead of 8.8.8.8
   - Suspiciously low TTL values
   - Consistent targeting of the internal login domain
#### Technical Analysis 
<a href = https://github.com/amphib24/MITM-detection/tree/main/dns-spoofing-analysis>Analysis</a>

                                                                              
 
