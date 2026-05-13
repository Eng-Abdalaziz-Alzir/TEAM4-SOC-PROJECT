# TEAM4-SOC-PROJECT
DEPI TEAM4-SOC-PROJECT
 

1. Project Planning & Management 
•	Project Proposal – Overview of the project, objectives, and scope.
 
Project 1: Building and Operating a Mini Security Operations Center (SOC)
•	Project Title: Security Operations Center (SOC) Implementation and Threat Detection.

Objectives: 
1 - Simulate a full SOC workflow including log ingestion, threat detection, triaging, and reporting. 
2 - To build a functional SOC environment using Splunk for monitoring, a pfSense firewall for network security, and various detection tools like Suricata and ModSecurity.


•	Project Plan – Timeline (Gantt chart), milestones, deliverables, and resource allocation.

Project Plan:

 
Timeline (Gantt Chart View):
This timeline follows the 4-week sprint structure suggested in project booklet for building the SOC and performing incident response.
Week 1: SOC Setup & Log Ingestion:
Environment Setup (March)
Tasks:
o Set up a centralized logging server using Splunk.
	Install Splunk Enterprise on Ubuntu.
o Configure log sources: Windows, firewall, and IDS.
	Deploy pfSense Firewall and configure LAN/WAN interfaces.
	Set up Windows Domain Controller.
Deliverables:
	Operational log ingestion pipeline.
	Documented SOC architecture.
Week 2: SIEM Configuration & Use Case Development:
Data Ingestion & Security Layers (April)
	Install Universal Forwarders and Sysmon on endpoints.
	Integrate Suricata (IDS/IPS) and ModSecurity (WAF).
	Verify log flow from all sources into Splunk.
Tasks:
	Deploy SIEM platform Splunk.
	Configure 3–5 real-world use cases (e.g., brute force, malware infection).
Deliverables:
	Use case documentation with correlation rules.
	Screenshots of triggered alerts.

Week 3: Alert Triage and Incident Management: 
Detection & Simulation (Early May)
	Configure SPL rules and alerts in Splunk.
	Simulate attacks (SQLi for WAF, testmyids.com for Suricata).
	Perform Active Directory attack simulations.
Tasks:
	Triage and analyze triggered alerts.
	Apply MITRE ATT&CK mapping to events.
Deliverables:
	Triage sheets and IOC identification.
	Evidence of containment or analysis recommendations.
Week 4: Reporting & Final Presentation: 
Documentation & Hardening (Mid May)
	Draft the Incident Response Playbook.
	Finalize the Technical Report and Executive Summary.
	Record the project demonstration video.
Tasks:
	Prepare KPI metrics.
	Perform root cause analysis for a selected incident.
Deliverables:
	Final Report: SOC architecture, SIEM rules, KPIs, analysis.
	Presentation: SOC design, use cases, response strategy.

Project Milestones & Deliverables:
This table outlines the major phases of SOC project.
Milestone	Deliverable	Due Date
M1: Planning & Scope	Project Proposal & Risk Assessment	Jan 27, 2026
M2: Research & Req.	Literature Review & Hardware/Software Specs	Mar 1, 2026
M3: System Design	Network Topology & SIEM Architecture Design	Apr 3, 2026
M4: Implementation	Functional SOC (Splunk, pfSense, WAF, IDS)	May 17, 2026
M5: Final Submission	Final Report, Test Cases, & Presentation	May 24, 2026
Deliverables:
1.	A fully configured Splunk Enterprise instance.
2.	Active Directory and Windows log forwarding.
3.	Web Application Firewall (WAF) integration.
4.	Intrusion Detection/Prevention System (IDS/IPS) setup.
Resource Allocation:
distribution of responsibilities:
Role	Responsibility	Primary Tools
SOC Architect	Infrastructure setup, pfSense configuration, and networking.	VMware, pfSense
SIEM Administrator	Splunk installation, Index management, and Syslog-ng setup.	Splunk, Linux (Ubuntu)
Security Engineer	IDS/IPS (Suricata) and WAF (ModSecurity) implementation.	Suricata, Apache/XAMPP
Endpoint Specialist	Sysmon deployment, GPO configuration, and Windows logging.	Windows Server, Sysmon
Threat Hunter	Writing SPL queries, creating alerts, and attack simulation.	Splunk (SPL), Kali Linux
Technical Writer	Documentation, Gantt chart, and Final Presentation.	GitHub, LaTeX/Office

Task Assignment & Roles – Defined responsibilities for team members. 

Task	Assignment	responsibilities
AD environment and, Sysmon deployment, GPO configuration, Windows logging 	Abdalaziz Mohammad Mohammad	All responsible for his:
1- Infrastructure setup, configuration, and networking. 
2- Index management, and Syslog-ng setup.
3- Writing SPL queries, creating alerts, and attack simulation.
4 - collect his logs and send it to Splunk.
5 - create use cases and correlate it.
6 - make  Documentation, Gantt chart, and Final Presentation  (for his specific task according to the previous schedule).
Splunk installation	Ahmed Hamdy	
PFSENSE FW	Marko Maged	
SURICATA IDS/IPS 	Ahmed Atef	
XAMP WEB SERVER AND DVWA WEB  APPLICATION	Bassam Alsayed	
WAF (ModSecurity) 	Ahmed Tarek	



 





Tasks Sequence:
1- install splunk.
2- install forwarder.
3- configure Domain Controller Environment.
4- install Xampp Web app server.
5- install Pfsense firewall. 
6- install Suricata IPS/IDS. 
6- configure DVWA vulnerable Web app on xampp.
7- configure securityMod WAF To detect web app attacks.
8- write rule in splunk to generate alert. 
9- detect Active Directory Attacks.

SOC Team4: Lab Configuration Standard
________________________________________
1. Core Infrastructure & Host Specifications
Category	Component	Details
Physical Host	Laptop (Win 11 Pro)	i5-1235U (10 Cores), 16GB RAM, 512GB SSD
Virtualization	Platforms Used	VMware Workstation / PNetLab
SIEM Platform	Ubuntu 22.04 LTS	Splunk Enterprise (IP: 192.168.254.156)
Security Gateway	pfSense 2.7.x	Suricata IDS enabled on WAN/DMZ
________________________________________
2. Virtual Machine Inventory (Endpoints & Servers)
Hostname	Operating System	IP Address	Primary Role	Domain Status
TEAM4-DC	Win Server 2019	192.168.254.140	Domain Controller (DNS/AD)	SOC.DEPI
WEBSRV	Win Server 2019	192.168.120.100	XAMPP, DVWA, SQL Server	SOC.DEPI
TEAM4-PC1	Win 10 Enterprise	10.1.1.100	HR Workstation	SOC.DEPI
TEAM4-PC2	Win 10 Enterprise	10.2.1.100	Finance Workstation	SOC.DEPI
KALI	Kali 2025.4	192.168.250.128	VAPT / Attacker Machine	Non-Domain
________________________________________
3. Network Topology & Segmentation (pfSense)
Interface	Subnet Range	Gateway	VLAN / Purpose	VMnet
WAN	192.168.250.0/24	192.168.250.129	Internet / External Exposure	VMnet0
LAN	192.168.254.0/24	192.168.254.2	DataCenter / Management	VMnet1
DMZ	192.168.120.0/24	192.168.120.1	Web Hosting (WEBSRV)	VMnet2
OPT1	10.1.1.0/24	10.1.1.1	Corporate (HR Dept)	VMnet3
OPT2	10.2.1.0/24	10.2.1.1	Corporate (Finance Dept)	VMnet4
________________________________________
4. Identity & Access Management (AD Credentials)
User / Service	Username	Password	Notes
Domain Admin	administrator	P@$$w0rd!	High Privilege
HR User	AAli	Password1	TEAM4-PC1 Login
Finance User	AHamdy	Password2	TEAM4-PC2 Login
Special User	MMaged	Password2026!@#	Target for privilege escalation
Service Acc	SQLService	MYpassword123#	Targeted for Kerberoasting
________________________________________
Detection & Monitoring Stack
•	WAF (ModSecurity): Configured on WEBSRV to log to C:\xampp\apache\logs\modsec_audit.log.
•	Logging: Splunk Universal Forwarder installed on all Windows endpoints.
•	SIEM Integration: Logs are centralized on the Ubuntu Splunk instance for correlation.

# Team4 SOC Lab Implementation

## Project Overview

This repository contains the implementation of a Security Operations Center (SOC) environment for threat detection and incident response. The project simulates a full SOC workflow including log ingestion, threat detection, triaging, and reporting using Splunk, pfSense, and various security tools.

### Objectives

1. Simulate a full SOC workflow including log ingestion, threat detection, triaging, and reporting
2. Build a functional SOC environment using Splunk for monitoring, pfSense for network security, and detection tools like Suricata and ModSecurity

## Project Timeline

### Week 1: SOC Setup & Log Ingestion
- Set up centralized logging server using Splunk Enterprise on Ubuntu
- Configure log sources: Windows, firewall, and IDS
- Deploy pfSense Firewall and configure LAN/WAN interfaces
- Set up Windows Domain Controller

### Week 2: SIEM Configuration & Use Case Development
- Install Universal Forwarders and Sysmon on endpoints
- Integrate Suricata (IDS/IPS) and ModSecurity (WAF)
- Verify log flow from all sources into Splunk
- Deploy SIEM platform Splunk
- Configure 3-5 real-world use cases (e.g., brute force, malware infection)

### Week 3: Alert Triage and Incident Management
- Configure SPL rules and alerts in Splunk
- Simulate attacks (SQLi for WAF, testmyids.com for Suricata)
- Perform Active Directory attack simulations
- Triage and analyze triggered alerts
- Apply MITRE ATT&CK mapping to events

### Week 4: Reporting & Final Presentation
- Draft the Incident Response Playbook
- Finalize the Technical Report and Executive Summary
- Record the project demonstration video
- Prepare KPI metrics
- Perform root cause analysis for a selected incident

## Network Architecture

### Core Infrastructure

| Component | Details |
|-----------|---------|
| Physical Host | Laptop (Win 11 Pro) i5-1235U (10 Cores), 16GB RAM, 512GB SSD |
| Virtualization | VMware Workstation |
| SIEM Platform | Ubuntu 22.04 LTS Splunk Enterprise (IP: 192.168.254.156) |
| Security Gateway | pfSense 2.7.x with Suricata IDS enabled on WAN/DMZ |

### Virtual Machine Inventory

| Hostname | Operating System | IP Address | Primary Role | Domain Status |
|----------|------------------|------------|--------------|---------------|
| TEAM4-DC | Win Server 2019 | 192.168.254.140 | Domain Controller (DNS/AD) | SOC.DEPI |
| WEBSRV | Win Server 2019 | 192.168.120.100 | XAMPP, DVWA, SQL Server | SOC.DEPI |
| TEAM4-PC1 | Win 10 Enterprise | 10.1.1.100 | HR Workstation | SOC.DEPI |
| TEAM4-PC2 | Win 10 Enterprise | 10.2.1.100 | Finance Workstation | SOC.DEPI |
| KALI | Kali 2025.4 | 192.168.250.128 | VAPT / Attacker Machine | Non-Domain |

### Network Segmentation

| Interface | Subnet Range | Gateway | VLAN / Purpose | VMnet |
|-----------|--------------|---------|----------------|-------|
| WAN | 192.168.250.0/24 | 192.168.250.129 | Internet / External Exposure | VMnet0 |
| LAN | 192.168.254.0/24 | 192.168.254.2 | DataCenter / Management | VMnet1 |
| DMZ | 192.168.120.0/24 | 192.168.120.1 | Web Hosting (WEBSRV) | VMnet2 |
| OPT1 | 10.1.1.0/24 | 10.1.1.1 | Corporate (HR Dept) | VMnet3 |
| OPT2 | 10.2.1.0/24 | 10.2.1.1 | Corporate (Finance Dept) | VMnet4 |

## Team Roles & Responsibilities

| Role | Responsibility | Primary Tools |
|------|----------------|---------------|
| SOC Architect | Infrastructure setup, pfSense configuration, networking | VMware, pfSense |
| SIEM Administrator | Splunk installation, Index management, Syslog-ng setup | Splunk, Linux (Ubuntu) |
| Security Engineer | IDS/IPS (Suricata) and WAF (ModSecurity) implementation | Suricata, Apache/XAMPP |
| Endpoint Specialist | Sysmon deployment, GPO configuration, Windows logging | Windows Server, Sysmon |
| Threat Hunter | Writing SPL queries, creating alerts, attack simulation | Splunk (SPL), Kali Linux |
| Technical Writer | Documentation, Gantt chart, Final Presentation | GitHub, LaTeX/Office |

## Use Cases & Detection Rules

### 1. Brute Force Attack Detection

**Description**: Detect when an adversary attempts to gain access by guessing credentials through multiple login attempts.

**Data Sources**: 
- Windows Event Log (EventCode 4625)
- Linux /var/log/secure
- Web Logs (HTTP 401 status codes)

**SPL Rule**:
```splunk
index="windows_dc" EventCode=4625
| stats count min(_time) as firstTime max(_time) as lastTime by Source_Network_Address, Account_Name
| where count >= 5
| eval severity="high"
```

### 2. SQL Injection (SQLi) Monitoring

**Description**: Detect malicious SQL code inserted into input fields or URLs to manipulate backend databases.

**Data Sources**:
- Web server access logs (Apache, IIS, Nginx)
- WAF logs (ModSecurity)

**SPL Rule**:
```splunk
index=* "UNION" | stats values(sourcetype) as "Source Types", count by host
```

### 3. Network Scanning Reconnaissance

**Description**: Detect mapping of live hosts, open ports, and services as part of the "Reconnaissance" phase.

**Data Sources**:
- Firewall logs
- IDS/IPS logs (Suricata)
- Netflow data

**SPL Rule**:
```splunk
index="suricata" "192.168.250.128"
| rex "src_ip\":\"(?<src>[^\"]+)"
| rex "dest_port\":(?<port>\d+)"
| stats dc(port) as unique_ports_hit by src
| where unique_ports_hit > 10
```

## Installation & Setup

### Prerequisites

- VMware Workstation or compatible virtualization platform
- Host machine with at least 16GB RAM
- ISO files for required operating systems

### Setup Sequence

1. Install Splunk Enterprise on Ubuntu
2. Install Universal Forwarders on all endpoints
3. Configure Domain Controller Environment
4. Install Xampp Web app server
5. Install pfSense firewall
6. Install Suricata IDS/IPS
7. Configure DVWA vulnerable Web app on xampp
8. Configure ModSecurity WAF to detect web app attacks
9. Write rules in Splunk to generate alerts
10. Configure Active Directory attack detection

### Power On Sequence

1. pfSense
2. Ubuntu (Splunk)
3. TEAM4-DC
4. Endpoints

### Validation

Access Splunk Web at http://192.168.254.156:8000 and verify "Data Summary" shows logs from all hosts.

## Testing Scenarios

| Test ID | Scenario | Expected Outcome |
|---------|----------|------------------|
| TC-01 | Brute Force from Kali (192.168.250.128) | Splunk triggers alert after 5 failed attempts |
| TC-02 | SQL Injection on DVWA | ModSecurity logs the attempt; Splunk displays "UNION" query in dashboard |
| TC-03 | Nmap Scan via WAN | pfSense/Suricata logs traffic; alert generated in Splunk |

## Risk Assessment & Mitigation

| Potential Risk | Impact | Mitigation Strategy |
|----------------|--------|---------------------|
| Resource Exhaustion | High | Limit Windows 10 VMs to 2GB RAM each |
| IP Address Conflicts | Medium | Maintain strict IP standard |
| Log Injection Failure | High | Verify connectivity between Universal Forwarders and Splunk |
| WAF False Positives | Medium | Test ModSecurity in "Detection Only" mode first |

## Key Performance Indicators (KPIs)

- System Uptime: 99% availability of Splunk web interface during testing phase
- Log Coverage: 100% of critical endpoints successfully forwarding logs
- Detection Accuracy: Zero "false negatives" during simulated attacks
- Response Time: Alert generation in Splunk within 2 minutes of security event

## Future Improvements

- Transition from local log storage to dedicated Syslog-ng server
- Integrate automated SOAR platform to automatically block IPs in pfSense
- Expand use cases beyond current 10 scenarios
- Upgrade to 32GB RAM for handling increased load

## Repository Structure

```
├── docs/
│   ├── project-planning.md
│   ├── network-architecture.md
│   └── use-cases.md
├── splunk/
│   ├── dashboards/
│   ├── alerts/
│   └── saved-searches/
├── scripts/
│   ├── setup/
│   └── testing/
├── configs/
│   ├── pfsense/
│   ├── suricata/
│   └── modsecurity/
└── reports/
    ├── technical
