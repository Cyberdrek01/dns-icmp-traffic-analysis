# Cybersecurity Incident Report: Network Traffic Analysis

## Part 1: Summary of the Problem Found in the DNS and ICMP Traffic Log

The `tcpdump` network protocol analyzer logs indicate that **UDP port 53** is unreachable on the DNS server (`203.0.113.2`) when attempting to resolve the domain name `www.yummyrecipesforme.com`. 

The captured network traffic involves three protocols:
* **DNS:** Application-layer query requesting the website's `A` record IP address.
* **UDP:** Transport-layer protocol carrying the DNS query.
* **ICMP:** Internet-layer protocol returning destination/port unreachable error diagnostics.

Specifically, the log shows the client machine (`192.51.100.15`, ephemeral port `52444`) sending three identical UDP requests (Query ID `35084+`) at two-minute intervals (`13:24:32`, `13:26:32`, and `13:28:32`), with each request followed by an ICMP response stating `"udp port 53 unreachable"`. Port 53 is reserved for DNS services. Because no service is actively listening on UDP port 53, the client cannot translate the domain name into an IP address to establish an HTTPS connection with the web server—pointing to a crashed DNS service, a misconfigured firewall, or a potential Denial-of-Service (DoS) attack on the DNS server.

---

## Part 2: Incident Analysis & Suspected Root Cause

* **Time Incident Occurred:** 1:24 p.m. (first logged at `13:24:32.192571`).
* **Detection & Symptoms:** Several customers reported that they could not reach `www.yummyrecipesforme.com` and received a `"destination port unreachable"` error after waiting for the page to load.
* **Actions Taken:** The security team reproduced the error internally and ran live packet captures using `tcpdump` while attempting to reload the webpage. Findings were documented and escalated to the direct supervisor and security engineering team.
* **Key Investigation Findings:** The DNS server (`203.0.113.2`) is reachable over the network (evidenced by its ICMP replies), but UDP port 53 is closed or unresponsive, generating three consecutive ICMP Type 3, Code 3 (`Port Unreachable`) responses with packet lengths of `254`, `320`, and `150`.
* **Next Steps:**
  1. Verify that the DNS service daemon is running on `203.0.113.2` and restart it if stopped.
  2. Audit host and network firewall configurations to ensure inbound UDP port 53 is permitted.
  3. Review system and network logs for signs of anomalous traffic or DoS activity.
* **Suspected Root Cause:** An accidental crash or misconfiguration of the DNS service on `203.0.113.2`, or a targeted DoS attack disrupting DNS resolution and taking the client's website offline.
