## SSL Stripping Analysis - MITM Investigation

## Introduction

&nbsp;&nbsp;&nbsp; SSL stripping is a Man-in-the-Middle (MITM) techniques where an attacker downgrades a secure HTTPS connection to unencrypted HTTP. This allows sensitive data to be transmitted in clear text, enabling 
credential interception and session hijacking. While modern browsers and major sites being hardcoded to always use HTTPS, it is still a concern in the wild in organizations with misconfigured systems and legacy apps. 

## Scenario 
 
&nbsp;&nbsp;&nbsp; This investigation is based off the MITM Detection labs from TryHackMe's SOC 1 learning path. The challenge involves locating activity associated with a MITM attack inside a corporate LAN environment.
The lab provided a gateway IP of 192.168.10.1 which is a router, a domain of corp-login.acme-corp.local, as well as the packet capture (pcap) file.

The investigation is focused on identifying evidence of:
  - ARP Spoofing
  - DNS Spoofing
  - SSL Stripping
This write up will cover the SSL stripping portion of the investigation using packet analysis in Wireshark.

## Potential Indicators of Attack (IOAs)
  - HTTPS requests downgraded to HTTP
  - Persistent redirects from HTTPS to HTTP
  - Absence of TLS negotiation where excpected  
  - Certificate errors
  - Plaintext credentials observed in HTTP traffic
## Traffic Analysis
I began by analyzing TLS handshake activity associated with the target domain corp-login.acme-corp.local.

#### Wireshark Filter Used:
   - tls.handshake.type == 1 && tls.handshake.extensions_server_name == "corp-login.acme-corp.local"

#### Analyst Observation:
   - Client hello packets confirmed inital attempts to establish TLS connections to the target domain using Server Name Indication (SNI), indicating expected HTTPS communication at the start of the session.

<img width="1857" height="856" alt="verify_domain_and_its_use_of_tls" src="https://github.com/user-attachments/assets/ba227748-e382-4918-b45b-8b8af9fed516" />

## DNS Spoofing Correlation Check
I believe the attacker is using the same host as was identified durning the DNS anlaysis (192.168.10.55). To correlate the SSL stripping activity with the earlie DNS activity I analyzed responses from the suspected host.

#### Wireshark Filter Used:
   - dns.flags.response == 1 && ip.src == 192.168.10.55 && dns.qry.name == "corp-login.acme-corp.local"

#### Analyst Observation:
   - The evidence suggests that the IP 192.168.10.55 is invovled in the traffic interception with and endpoint located at 192.168.10.10.

<img width="1858" height="852" alt="verify_dns_ip" src="https://github.com/user-attachments/assets/3621172a-cf26-4fd6-b3eb-afd68653bce0" />

## TLS Traffic Verification
 I then checked for TLS traffic between the affected hosts.

#### Wireshark Filter Used:
   - tls && ip.src == 192.168.10.10 && ip.dst == 192.1681.10.55

#### Analyst Observation:
   - No TLS traffic was found between the endpoints, indicating that encrypted communication was likely prevented or downgraded.

<img width="1861" height="856" alt="verify_no_tls_traffic" src="https://github.com/user-attachments/assets/0349f4da-352a-428f-b65d-ea9b94cf85d8" />
