# Mini Security Operations Center (SOC) — Team 4

[![Splunk](https://img.shields.io/badge/SIEM-Splunk%20Enterprise-orange)]()
[![pfSense](https://img.shields.io/badge/Firewall-pfSense%202.7.x-blue)]()
[![Suricata](https://img.shields.io/badge/IDS%2FIPS-Suricata-red)]()
[![ModSecurity](https://img.shields.io/badge/WAF-ModSecurity-green)]()
[![Ubuntu](https://img.shields.io/badge/OS-Ubuntu%2022.04%20LTS-purple)]()
[![Status](https://img.shields.io/badge/Release-4-success)]()

> **Project Title:** Security Operations Center (SOC) Implementation and Threat Detection
> **Program:** رواد مصر الرقمية — وزارة الاتصالات وتكنولوجيا المعلومات (DEPI)
> **Team:** Team 4
> **Release:** 4

---

## 📖 Table of Contents

- [Overview](#-overview)
- [Objectives](#-objectives)
- [Lab Architecture](#-lab-architecture)
- [Lab Configuration Standard](#-lab-configuration-standard)
- [Network Topology & Segmentation](#-network-topology--segmentation-pfsense)
- [Identity & Access Management](#-identity--access-management-ad-credentials)
- [Detection & Monitoring Stack](#-detection--monitoring-stack)
- [Project Plan & Timeline](#-project-plan--timeline)
- [Milestones & Deliverables](#-milestones--deliverables)
- [Team Roles & Responsibilities](#-team-roles--responsibilities)
- [SOC Use Cases](#-soc-use-cases)
- [SIEM Detection Rules (SPL)](#-siem-detection-rules-spl)
- [Risk Assessment](#-risk-assessment--mitigation-plan)
- [KPIs](#-key-performance-indicators-kpis)
- [Testing & QA](#-testing--quality-assurance)
- [Execution Guide](#-execution-guide)
- [Findings & Recommendations](#-findings--recommendations)
- [License](#-license)

---

## 📌 Overview

This repository documents the design, implementation, and operation of a **Mini Security Operations Center (SOC)** built in a virtualized lab environment.
It simulates a corporate network with a **Domain Controller**, **Web Server (DVWA on XAMPP)**, **Workstations**, **pfSense Firewall**, and a centralized **Splunk SIEM**, all monitored via **Suricata IDS/IPS**, **ModSecurity WAF**, and **Sysmon** endpoint logging.

Attacks are launched from a **Kali Linux** attacker VM to validate detection use cases (Brute Force, SQL Injection, Network Scanning, Lateral Movement, Privilege Escalation).

---

## 🎯 Objectives

1. Simulate a full SOC workflow: **log ingestion → threat detection → triage → reporting**.
2. Build a functional SOC environment using:
   - **Splunk Enterprise** (monitoring/SIEM)
   - **pfSense** (network security/segmentation)
   - **Suricata** (IDS/IPS)
   - **ModSecurity** (WAF)
   - **Sysmon + Universal Forwarders** (endpoint visibility)

---

## 🏗 Lab Architecture

```
   PUBLIC INTERNET / ATTACKER         WEB NETWORK (DMZ)        pfSense FW         CORP NETWORK (LAN)
   ┌───────────────────────┐          ┌──────────────────┐      ┌───────┐         ┌──────────────────┐
   │   KALI LINUX (VAPT)   │ ───────► │ WEBSRV (XAMPP +  │ ──►  │ pfSe- │ ──────► │ DOMAIN CONTROLLER│
   │   192.168.250.128     │ ATTACKS  │ DVWA + ModSec)   │      │ nse + │         │ (TEAM4-DC AD)    │
   └───────────────────────┘          │ 192.168.120.100  │      │Suric- │         │ 192.168.254.140  │
                                      └──────────────────┘      │ ata   │         └──────────────────┘
                                                                └───┬───┘                  │
                                                  WAF / IDS / FW logs│                     ▼
                                                                     ▼          ┌────────────────────┐
                                                          ┌────────────────┐    │ TEAM4-PC1 (HR)      │
                                                          │  Ubuntu 22.04  │    │ 10.1.1.100 + UF/Sys │
                                                          │  SPLUNK SIEM   │◄───┤                    │
                                                          │ 192.168.254.156│    │ TEAM4-PC2 (Finance) │
                                                          └────────────────┘    │ 10.2.1.100 + UF/Sys │
                                                                                └────────────────────┘
```

---

## ⚙️ Lab Configuration Standard

### 1. Core Infrastructure & Host Specifications

| Category | Component | Details |
|----------|-----------|---------|
| **Physical Host** | Laptop (Win 11 Pro) | i5-1235U (10 Cores), 16GB RAM, 512GB SSD |
| **Virtualization** | Platform | VMware Workstation |
| **SIEM Platform** | Ubuntu 22.04 LTS | Splunk Enterprise (`192.168.254.156`) |
| **Security Gateway** | pfSense 2.7.x | Suricata IDS enabled on WAN/DMZ |

### 2. Virtual Machine Inventory

| Hostname | OS | IP Address | Primary Role | Domain |
|----------|----|-----------:|--------------|--------|
| **TEAM4-DC** | Win Server 2019 | `192.168.254.140` | Domain Controller (DNS/AD) | SOC.DEPI |
| **WEBSRV** | Win Server 2019 | `192.168.120.100` | XAMPP, DVWA, SQL Server | SOC.DEPI |
| **TEAM4-PC1** | Win 10 Enterprise | `10.1.1.100` | HR Workstation | SOC.DEPI |
| **TEAM4-PC2** | Win 10 Enterprise | `10.2.1.100` | Finance Workstation | SOC.DEPI |
| **KALI** | Kali 2025.4 | `192.168.250.128` | VAPT / Attacker | Non-Domain |

---

## 🌐 Network Topology & Segmentation (pfSense)

| Interface | Subnet | Gateway | Purpose | VMnet |
|-----------|--------|---------|---------|-------|
| **WAN** | `192.168.250.0/24` | `192.168.250.129` | Internet / External Exposure | VMnet0 |
| **LAN** | `192.168.254.0/24` | `192.168.254.2` | DataCenter / Management | VMnet1 |
| **DMZ** | `192.168.120.0/24` | `192.168.120.1` | Web Hosting (WEBSRV) | VMnet2 |
| **OPT1** | `10.1.1.0/24` | `10.1.1.1` | Corporate (HR) | VMnet3 |
| **OPT2** | `10.2.1.0/24` | `10.2.1.1` | Corporate (Finance) | VMnet4 |

---

## 🔐 Identity & Access Management (AD Credentials)

> ⚠️ **LAB ONLY** — These credentials are intentionally weak for attack-simulation purposes. **Never** use in production.

| User / Service | Username | Password | Notes |
|----------------|----------|----------|-------|
| Domain Admin | `administrator` | `P@$$w0rd!` | High Privilege |
| HR User | `AAli` | `Password1` | TEAM4-PC1 Login |
| Finance User | `AHamdy` | `Password2` | TEAM4-PC2 Login |
| Special User | `MMaged` | `Password2026!@#` | Target for privilege escalation |
| Service Account | `SQLService` | `MYpassword123#` | Targeted for Kerberoasting |

---

## 🛡 Detection & Monitoring Stack

- **WAF (ModSecurity):** Configured on `WEBSRV` → logs to `C:\xampp\apache\logs\modsec_audit.log`
- **IDS/IPS (Suricata):** Enabled on pfSense WAN/DMZ interfaces
- **Endpoint Logging:** Splunk Universal Forwarder + **Sysmon** on all Windows hosts
- **Centralization:** All logs forwarded to **Splunk Enterprise** on Ubuntu (`192.168.254.156:9997`)

### Splunk Indexes

| Index | Source |
|-------|--------|
| `pfsense_fw` | pfSense Firewall (filterlog) |
| `suricata` | Suricata IDS JSON events |
| `waf` | ModSecurity audit log |
| `webapp` | Apache access log |
| `windows_dc` | Domain Controller (Sysmon + WinEventLog) |
| `windows_pc1` | TEAM4-PC1 (Sysmon) |
| `windows_pc2` | TEAM4-PC2 (Sysmon) |
| `windows_websrv` | WEBSRV host events |

---

## 📅 Project Plan & Timeline

A 4-week sprint structure following the project booklet:

### Week 1 — SOC Setup & Log Ingestion *(March)*
- Install Splunk Enterprise on Ubuntu
- Deploy pfSense FW (LAN/WAN/DMZ/OPT interfaces)
- Stand up Windows Domain Controller
- ✅ **Deliverables:** Operational log pipeline, documented SOC architecture

### Week 2 — SIEM Configuration & Use Case Development *(April)*
- Install Universal Forwarders + Sysmon on endpoints
- Integrate Suricata (IDS/IPS) and ModSecurity (WAF)
- Develop 3–5 real-world use cases
- ✅ **Deliverables:** Use-case docs with correlation rules, alert screenshots

### Week 3 — Alert Triage & Incident Management *(Early May)*
- Configure SPL detection rules and alerts
- Simulate attacks (SQLi on DVWA, `testmyids.com`, AD attacks)
- Triage alerts and map to **MITRE ATT&CK**
- ✅ **Deliverables:** Triage sheets, IOCs, containment recommendations

### Week 4 — Reporting & Final Presentation *(Mid May)*
- Draft Incident Response Playbook
- Compile Technical Report + Executive Summary
- Record demo video, prepare KPI metrics, root cause analysis
- ✅ **Deliverables:** Final Report, Presentation

---

## 🏁 Milestones & Deliverables

| Milestone | Deliverable | Due Date |
|-----------|-------------|----------|
| **M1: Planning & Scope** | Project Proposal & Risk Assessment | Jan 27, 2026 |
| **M2: Research & Req.** | Literature Review & HW/SW Specs | Mar 1, 2026 |
| **M3: System Design** | Network Topology & SIEM Architecture | Apr 3, 2026 |
| **M4: Implementation** | Functional SOC (Splunk, pfSense, WAF, IDS) | May 17, 2026 |
| **M5: Final Submission** | Final Report, Test Cases, Presentation | May 24, 2026 |

### Final Deliverables
1. Fully configured **Splunk Enterprise** instance
2. **Active Directory** + Windows log forwarding
3. **WAF (ModSecurity)** integration
4. **IDS/IPS (Suricata)** setup

---

## 👥 Team Roles & Responsibilities

| Role | Responsibility | Primary Tools |
|------|----------------|---------------|
| **SOC Architect** | Infrastructure, pfSense, networking | VMware, pfSense |
| **SIEM Administrator** | Splunk install, Index mgmt, Syslog-ng | Splunk, Ubuntu |
| **Security Engineer** | Suricata IDS/IPS + ModSecurity WAF | Suricata, Apache/XAMPP |
| **Endpoint Specialist** | Sysmon, GPO, Windows logging | Win Server, Sysmon |
| **Threat Hunter** | SPL queries, alerts, attack simulations | Splunk, Kali Linux |
| **Technical Writer** | Documentation, Gantt chart, presentation | GitHub, LaTeX/Office |

### Task Assignment

| Task | Assignee |
|------|----------|
| AD Environment, Sysmon, GPO, Windows Logging | **Abdalaziz Mohammad Mohammad** |
| Splunk Installation | **Ahmed Hamdy** |
| pfSense FW | **Marko Maged** |
| Suricata IDS/IPS | **Ahmed Atef** |
| XAMPP Web Server + DVWA | **Bassam Alsayed** |
| WAF (ModSecurity) | **Ahmed Tarek** |

> Each member is responsible for: setup & configuration, log forwarding to Splunk, use-case creation & correlation, and documentation for their assigned component.

### Tasks Sequence
1. Install Splunk → 2. Install Forwarder → 3. Configure DC → 4. Install XAMPP → 5. Install pfSense → 6. Install Suricata → 7. Configure DVWA → 8. Configure ModSecurity → 9. Write SPL rules & alerts → 10. Detect AD attacks

---

## 🔍 SOC Use Cases

### 1️⃣ Brute Force Attack
- **Description:** Multiple failed authentication attempts against SSH/RDP/Web from a single source.
- **Why It Matters:** Leads to account compromise, data breaches, ransomware.
- **Data Sources:** Windows EventCode `4625`, `/var/log/secure`, HTTP `401`.

### 2️⃣ SQL Injection (SQLi)
- **Description:** Malicious SQL inserted into URLs/forms to manipulate the database (OWASP Top 10).
- **Why It Matters:** Data exfiltration, auth bypass, RCE.
- **Data Sources:** Apache/IIS/Nginx access logs, ModSecurity WAF logs.

### 3️⃣ Network Scanning (Reconnaissance)
- **Description:** Mapping live hosts, ports, and services — first phase of the Cyber Kill Chain.
- **Why It Matters:** High-fidelity precursor to attack; early hardening opportunity.
- **Data Sources:** pfSense firewall logs, Suricata IDS, NetFlow.

### 4️⃣ Lateral Movement
- **Description:** Pass-the-Hash, RDP, SMB, remote service creation between hosts.
- **Data Sources:** Win Event `4624`, `4688`, `5140`, Sysmon `1`/`3`.

### 5️⃣ Privilege Escalation
- **Description:** Group membership changes, Kerberoasting, token manipulation.
- **Data Sources:** Win Event `4728`, `4729`, `4732`, `4733`.

---

## 🧠 SIEM Detection Rules (SPL)

### Brute Force (Win EventCode 4625)
```spl
index="windows_dc" EventCode=4625
| stats count min(_time) as firstTime max(_time) as lastTime
    by Source_Network_Address, Account_Name
| where count >= 5
| eval severity="high"
```

### SQL Injection (UNION-based)
```spl
index=* "UNION"
| stats values(sourcetype) as "Source Types", count by host
```

### Network Scanning (Suricata, attacker = Kali `192.168.250.128`)
```spl
index="suricata" "192.168.250.128"
| rex "src_ip\":\"(?<src>[^\"]+)"
| rex "dest_port\":(?<port>\d+)"
| stats dc(port) as unique_ports_hit by src
| where unique_ports_hit > 10
```

### Network Scanning (pfSense filterlog — extracted fields)
```spl
index=pfsense_fw sourcetype="PFSENSE FW"
| rex field=_raw "(?<src_ip>\d+\.\d+\.\d+\.\d+),(?<dest_ip>\d+\.\d+\.\d+\.\d+),(?<src_port>\d+),(?<dest_port>\d+)"
| search src_ip="192.168.250.128"
| stats dc(dest_port) as unique_ports values(dest_port) as ports by dest_ip
| where unique_ports > 5
```

### Lateral Movement (multi-host logon by single account)
```spl
index=windows_* EventCode=4624
| stats dc(host) as distinct_hosts by Account_Name
| where distinct_hosts > 3
```

### Privilege Escalation (group membership changes)
```spl
index=windows_dc (EventCode=4728 OR EventCode=4729 OR EventCode=4732 OR EventCode=4733)
| stats count by Account_Name, Target_Account
```

---

## ⚠️ Risk Assessment & Mitigation Plan

| Risk | Impact | Mitigation |
|------|--------|------------|
| **Resource Exhaustion** | High | Limit Win10 VMs (HR/Finance) to 2GB RAM each |
| **IP Address Conflicts** | Medium | Strict IP standard; do not change subnets |
| **Log Injection Failure** | High | Verify forwarder ↔ Splunk via `ping` + `telnet 9997` |
| **WAF False Positives** | Medium | Run ModSecurity in *Detection-Only* before *Enforcement* |

---

## 📊 Key Performance Indicators (KPIs)

- **System Uptime:** 99% Splunk web availability over 4-week test phase
- **Log Coverage:** 100% of critical hosts (TEAM4-DC, WEBSRV, pfSense) forwarding
- **Detection Accuracy:** Zero false negatives for SQLi (WEBSRV) & NTLM relay
- **Response Time:** Alert generation in Splunk within **2 minutes** of trigger

---

## ✅ Testing & Quality Assurance

| Test ID | Scenario | Expected Outcome |
|---------|----------|------------------|
| **TC-01** | Brute Force from Kali (`192.168.250.128`) | Splunk triggers alert after ≥5 failed attempts |
| **TC-02** | SQL Injection on DVWA | ModSecurity logs the attempt; Splunk dashboard shows "UNION" |
| **TC-03** | Nmap Scan via WAN | pfSense/Suricata logs traffic; Splunk alert generated |

---

## 🚀 Execution Guide

### Power-On Sequence
1. **Start Hypervisor:** Open VMware Workstation
2. **Boot order:**
   1. **pfSense** (gateway must be up first)
   2. **Ubuntu / Splunk** (`192.168.254.156`)
   3. **TEAM4-DC** (Domain Controller)
   4. **WEBSRV** + Workstations
   5. **Kali** (only when running attack simulations)
3. **Validation:**
   - Splunk Web → `http://192.168.254.156:8000`
   - Verify **Settings → Data Summary** shows logs from all hosts.

### Quick Health Check (SPL)
```spl
| tstats count where index=* by index, sourcetype
```

---

## 🔭 Findings & Recommendations

- ✅ The current **i5-1235U / 16GB RAM** host handles the full lab load.
- ⚠️ Scaling beyond ~10 use cases will likely require **upgrading to 32GB RAM**.
- 🔧 **Future improvements:**
  - Move Linux logging from local files → dedicated **Syslog-ng** server (scalability).
  - Integrate a **SOAR** platform to auto-block IPs in pfSense when Splunk fires a *High* severity alert.

### Final Grading Criteria
`40%` Technical Implementation · `30%` Testing (Attack Simulations) · `20%` Documentation · `10%` Presentation

---

## 📂 Repository Structure (suggested)

```
soc-team4/
├── README.md
├── docs/
│   ├── architecture-diagram.png
│   ├── project-plan.pdf
│   └── ir-playbook.md
├── splunk/
│   ├── savedsearches.conf
│   ├── dashboards/
│   └── lookups/
├── pfsense/
│   └── config-backup.xml
├── suricata/
│   └── custom.rules
├── modsecurity/
│   └── crs-config/
├── sysmon/
│   └── sysmonconfig.xml
└── attack-simulations/
    ├── nmap-recon.md
    ├── dvwa-sqli.md
    └── ad-attacks.md
```

---

## 📜 License

This project is released for **educational purposes only** as part of the **DEPI – Ministry of Communications and Information Technology** training program.

> ⚠️ **Disclaimer:** All offensive techniques shown here are intended for use **inside the isolated lab environment only**. Do **not** apply them against systems you do not own or have explicit written permission to test.

---

**Maintained by:** SOC Team 4 — DEPI 2026
**Release:** 4 · **Last Updated:** May 2026
