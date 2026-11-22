# dns-icmp-traffic-analysis

# Network Traffic Analysis: DNS & ICMP Incident Investigation

A hands-on cybersecurity incident investigation analyzing packet captures from `tcpdump` to diagnose a service outage affecting `www.yummyrecipesforme.com`. Completed as part of the Google Cybersecurity Professional Certificate.

## Scenario Overview
Multiple customers reported being unable to reach `www.yummyrecipesforme.com`, receiving a **"destination port unreachable"** error after extended load times. Packet-level inspection was performed to identify the impacted network protocols, isolate the point of failure, and document findings in a formal incident report.

## Tools & Protocols Analyzed
* **Network Analyzer:** `tcpdump`
* **Protocols:** DNS (Application), UDP (Transport), ICMP (Internet)
* **Key Indicators:** Source `192.51.100.15:52444` → Destination `203.0.113.2:53` (`A?` record query ID `35084+`), returning `ICMP 203.0.113.2 udp port 53 unreachable`

## Key Findings
* **Root Cause:** The DNS server (`203.0.113.2`) is reachable at the IP layer, but no service is actively listening on **UDP port 53** (or a firewall rule is actively rejecting traffic on port 53).
* **Impact:** Because DNS resolution fails across three consecutive retry attempts (`13:24:32`, `13:26:32`, `13:28:32`), client browsers cannot resolve the domain's IP address to initiate an HTTPS session.
* **Escalation & Remediation:** Documented findings and escalated to security engineering to verify DNS daemon status, inspect firewall rules on port 53, and audit system logs for potential DoS activity.

## Repository Contents
* [`incident-report.md`](./incident-report.md) — Formal two-part incident analysis and root-cause report.
* [`evidence/tcpdump_capture.log`](./evidence/tcpdump_capture.log) — Raw `tcpdump` log output analyzed during the investigation.
