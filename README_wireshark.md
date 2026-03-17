# 🔍 Vendmo Tech — Network Traffic Analysis Report

![Type](https://img.shields.io/badge/Type-Network%20Forensics-blue?style=flat-square)
![Tool](https://img.shields.io/badge/Tool-Wireshark%204.2-1679A7?style=flat-square&logo=wireshark)
![Framework](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK%20v14-red?style=flat-square)
![Team](https://img.shields.io/badge/Team-Blue%20Team%20%2F%20SOC-teal?style=flat-square)
![Status](https://img.shields.io/badge/Incident-CRITICAL-critical?style=flat-square)
![Version](https://img.shields.io/badge/Version-v1.0-lightgrey?style=flat-square)

---

## 📋 Overview

This repository contains a **Wireshark-based Network Traffic Analysis and Threat Detection Report** for **Vendmo Tech** — a fictional South African fintech company. This is **Project 2** in the Vendmo Tech cybersecurity portfolio series, and serves as a companion to the [Security Audit & Risk Assessment Report](../VendmoTech_Security_Audit_Report/).

The report simulates a real-world **SOC Blue Team incident response** scenario, analysing a 2.3GB packet capture file from a confirmed multi-stage attack. It covers all three core threat scenarios a Blue Team / SOC analyst encounters in the field:

| Scenario | Detected | Severity |
|----------|----------|----------|
| 🦠 Malware C2 Beaconing | ✅ Yes | CRITICAL |
| 📤 Data Exfiltration | ✅ Yes | CRITICAL |
| 🔎 Port Scan / Network Recon | ✅ Yes | HIGH |

---

## 📁 Repository Contents

| File | Description |
|------|-------------|
| `VendmoTech_Network_Traffic_Analysis.docx` | Full Wireshark analysis report (Word format) |
| `README.md` | This file |

---

## 📚 What's Inside the Report

The report is structured across **10 sections** and **2 appendices**:

| Section | Content |
|---------|---------|
| 1. Executive Summary | Incident overview, key stats, POPIA breach notification status |
| 2. Scope & Methodology | Capture environment, analysis phases, Wireshark filters used |
| 3. Traffic Overview | Baseline protocol statistics, top suspicious external connections |
| 4. Detailed Findings | 8 findings with full technical analysis and filter commands |
| 5. Packet Evidence Table | 10 key packets with source, destination, flags, and severity |
| 6. Attack Timeline | Chronological reconstruction of the full kill chain |
| 7. IOCs | 10 Indicators of Compromise (IPs, domains, hashes, behaviours) |
| 8. MITRE ATT&CK Mapping | 8 techniques mapped across 5 ATT&CK tactics |
| 9. Recommendations | 10 prioritised containment and remediation actions |
| 10. Conclusion | Incident status, POPIA obligations, follow-up actions |
| Appendix A | Full Wireshark filter reference (copy-paste ready) |
| Appendix B | References and sources |

---

## 🛠️ Tools & Frameworks Used

### Primary Analysis Tool
| Tool | Version | Purpose |
|------|---------|---------|
| **Wireshark** | 4.2.0 | Packet capture analysis, display filtering, stream reconstruction |

### Threat Intelligence & Supporting Tools
| Tool | Purpose |
|------|---------|
| **AbuseIPDB** | IP reputation and threat intelligence |
| **VirusTotal** | Domain, IP, and file hash analysis |
| **Shodan** | Internet-facing host intelligence |
| **Burp Suite** | HTTP stream analysis (supporting) |

### Frameworks Applied
- **MITRE ATT&CK Enterprise v14** — Primary threat behaviour classification
- **NIST SP 800-61r2** — Computer Security Incident Handling Guide
- **SANS Network Forensics Methodology** — Analysis structure
- **POPIA (Act 4 of 2013)** — South African breach notification requirements

---

## 🚨 Findings Summary

| ID | Severity | Finding |
|----|----------|---------|
| F-001 | 🔴 CRITICAL | Malware C2 Beaconing — 300s heartbeat to Tor exit node |
| F-002 | 🔴 CRITICAL | Customer PII Exfiltration — 4.2MB via plaintext HTTP POST |
| F-003 | 🔴 CRITICAL | DNS Tunnelling — Base64-encoded data in TXT queries |
| F-004 | 🟠 HIGH | Internal Port Scan — 260,096 SYN packets in 172 seconds |
| F-005 | 🟠 HIGH | SMB Lateral Movement — workstation to file server |
| F-006 | 🟠 HIGH | RDP Brute Force — 847 attempts in 4 minutes |
| F-007 | 🟠 HIGH | Unencrypted Data Transmission — API tokens in HTTP |
| F-008 | 🟠 HIGH | Typosquatted Domain in DNS — spoofed vendmo-cdn.net |

---

## 🗺️ MITRE ATT&CK Coverage

| Technique | Tactic | Observed |
|-----------|--------|---------|
| T1046 — Network Service Discovery | Discovery | ✅ |
| T1110.001 — Brute Force: Password Guessing | Credential Access | ✅ |
| T1021.002 — SMB/Windows Admin Shares | Lateral Movement | ✅ |
| T1071.001 — C2: Web Protocols | Command & Control | ✅ |
| T1071.004 — C2: DNS | Command & Control | ✅ |
| T1041 — Exfiltration Over C2 Channel | Exfiltration | ✅ |
| T1048.003 — Exfil Over Unencrypted Protocol | Exfiltration | ✅ |
| T1583.001 — Acquire Infrastructure: Domains | Resource Development | ✅ |

---

## 🔎 Key Wireshark Filters (Quick Reference)

```wireshark
# Detect C2 beaconing
ip.addr == 185.220.101.12

# Detect port scan activity
tcp.flags.syn == 1 && tcp.flags.ack == 0

# Detect DNS tunnelling
dns.qry.type == 16

# Detect large data exfiltration via HTTP
http.request.method == "POST" && data.len > 100000

# Detect malware user-agent string
http.user_agent contains "MSIE 9.0"

# Isolate all external HTTP traffic (should be near zero)
http && !(ip.dst == 10.0.0.0/8)
```

---

## ⏱️ Attack Timeline Summary

```
08:12  → Port scan initiated from 10.0.0.99 (1,024 ports × 254 hosts)
08:31  → RDP brute force against 10.0.0.5:3389 (847 attempts)
09:02  → SMB lateral movement from 10.0.0.15 to file server
09:15  → Malware implant establishes C2 beaconing (300s interval)
11:03  → DNS tunnelling begins — data staging via TXT records
11:45  → 4.2MB HTTP POST — customer PII exfiltrated to 203.0.113.55
14:22  → C2 beaconing continues — attacker maintaining access
07:00  → SOC detects anomalous traffic (next morning — 23hr dwell time)
```

---

## 📌 Indicators of Compromise (IOCs)

| Type | Indicator | Threat |
|------|-----------|--------|
| IP | `185.220.101.12` | C2 server (Tor exit node) |
| IP | `203.0.113.55` | Exfiltration endpoint |
| IP | `10.0.0.99` | Internal attacker host |
| Domain | `vendmo-cdn.net` | Attacker-controlled C2 domain |
| Domain | `data-sync.vendmo-cdn.net` | DNS tunnel endpoint |
| User-Agent | `Mozilla/5.0 (compatible; MSIE 9.0)` | Malware beacon signature |
| Port | `TCP/4422` | Non-standard C2 port |
| Behaviour | 300s beacon interval | C2 heartbeat pattern |

---

## 🏢 About the Fictional Scenario

**Vendmo Tech** is a simulated fintech company used as the subject of this analysis:
- **Incident Date:** 14 March 2026
- **Capture File:** `vendmotech_incident_20260314.pcap` (2.3GB — simulated)
- **Affected Host:** Finance department workstation (192.168.1.45)
- **Data Breached:** ~4,200 customer PII records
- **Detection Time:** ~23 hours (significant gap — key learning point)

> ⚠️ **Disclaimer:** Vendmo Tech is a fictional company. All IP addresses, packet data, and incidents are entirely simulated for educational and portfolio purposes. No real network was captured or analysed.

---

## 🔗 Related Projects

| Project | Description |
|---------|-------------|
| [Project 1 — Security Audit Report](../VendmoTech_Security_Audit_Report/) | Full cybersecurity audit covering 14 vulnerabilities, NIST CSF & ISO 27001 |
| Project 3 — SIEM & Log Analysis *(coming soon)* | Splunk/ELK-based threat detection and alerting |

---

## 🎯 Skills Demonstrated

- Real-world Wireshark usage and display filter construction
- Multi-threat scenario analysis (C2, exfiltration, recon)
- IOC extraction and threat intelligence correlation
- MITRE ATT&CK framework mapping
- SOC incident response documentation
- POPIA breach notification awareness
- Blue Team / Defensive security mindset

---

## 📄 Licence

This project is intended for educational and portfolio use. Feel free to reference or adapt it for your own cybersecurity learning.

---

*Report Version: v1.0 — March 2026 | Vendmo Tech SOC / Blue Team*
