# SOC Investigation Report - MITM-Detection

## Demonstrated Skills

   - Wireshark Packet Capture Analysis
   - MITM Detection
   - ARP Spoofing Detection
   - DNS Traffic Analysis
   - SSL Stripping Identification
   - IOA/IOC Identification
   - Network Traffic Investigation
   - Threat Analysis
   - Incident Documentation

# Executive Summary

&nbsp;&nbsp;&nbsp; This investigation documents the analysis of a simulated MITM attack on Acme-Corps corporate LAN as part of TryHackMe's SOC 1 learning path. The investigation was conducted using Wireshark for packet analysis, which focused on identifying IOAs, validating malicious traffic patterns, and documenting the evidence found as it correlates to the attack chain.
Packet capture analysis revealed a chain attack which involved:
  1) ARP Spoofing
  2) DNS Spoofing
  3) SSL Stripping

The attacker utilized ARP spoofing to establish their position between the client systems and the gateway, then manipulated DNS responses to redirect the victim to a malicious host, and downgraded HTTPS traffic to HTTP traffic, leading to 
credential interception. 

# Environment Overview

   - Gateway IP: 192.168.10.1
   - Suspected Malicious Host: 192.168.10.55
   - Target Domain: corp-login.acme-corp.local
   - Analysis Tool Used: Wireshark
   - Data Source: network-traffic.pcap

 # Attack Chain Overview

ARP Spoofing -> Traffic Interception -> DNS Spoofing -> Victim Redirect -> SSL Stripping -> Credential Capture

# Findings

## ARP Spoofing 
 Analysis of the packet capture revealed multiple indicators indicative of ARP spoofing, including:

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

## SSL Stripping
Analysis of the packet capture revealed multiple indicators indicative of SSL stripping, including:

   - Expected HTTPS traffic downgraded to HTTP
   - Absence of TLS communication between endpoints
   - Presence of HTTP login requests
   - Plain text Credentials This evidence strongly indicates SSL stripping as part of a broader MITM attack chain. By downgrading HTTPS traffic to HTTP, the attacker was able to intercept login credentials in plaintext, enabling potential unauthorized access and lateral movement within the network.

#### Technical Analysis 
<a href = https://github.com/amphib24/MITM-detection/tree/main/ssl-stripping-analysis>Analysis</a>

# Indicators of Compromise (IOC)

<table>
  <tr>
    <th>Indicator</th>
    <th>Description</th>
  </tr>
  <tr>
   <td>192.168.10.55</td>
   <td> Suspected malicious host</td>
  </tr>
  <tr>
    <td>02:fe:fe:fe:55:55/td>
    <td>Spoofed MAC address</td>
  </tr>
  <tr>
    <td>corp-login.acme-corp.local</td>
    <td>Targeted domain</td>
  </tr>
  <tr>
    <td>Gratuitous ARP replies</td>
    <td>Evidence of ARP spoofing</td>
  </tr>
  <tr>
    <td>Low TTL DNS responses</td>
    <td>Possible DNS manipulation</td>
  </tr> 
  <tr>
    <td>Plaintext HTTP credentials</td>
    <td>Evidence of SSL stripping</td>
  </tr>
</table>

# Recommended Remediation
   - Isolate the malicious host associated with MAC address 02:fe:fe:fe:55:55
   - Clear ARP caches on affected hosts
   - Enable Dynamic ARP Inspection (DAI)
   - Implement DHCP snooping and IP-MAC binding
   - Strengthen endpoint monitoring and detection controls
   - Isolate the malicious host 192.168.10.55
   - Flush DNS cache on affected systems
   - Restrict DNS resolution to approved internal resolvers only
   - Implement network segmentation to limit lateral movement
   - Monitor for anomalous DNS response patterns
   - Enforce HTTPS with HSTS (HTTP Strict Transport Security
   - Disable legacy HTTP access where possible
   - Monitor for protocol downgrades from HTTPS to HTTP

# Conclusion
&nbsp;&nbsp;&nbsp; The investigation confirmed a coordinated MITM attack utilizing ARP spoofing, DNS spoofing, and SSL stripping techniques. The attacker was able to utilize ARP spoofing to position themselves between client devices and the network Gateway, redirected victims utilizing DNS spoofing techniques, and downgraded HTTPS traffic to HTTP in order to capture credentials in the clear. The investigations shows how layered network attacks can be chained together to compromise user communications and spotlights the importance of network monitoring, protocol enforcement, and network segmentation. 

                                                                              
 
