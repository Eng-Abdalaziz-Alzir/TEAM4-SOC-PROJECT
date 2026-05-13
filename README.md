Team4 SOC Lab Implementation
Project Overview
This repository contains the implementation of a Security Operations Center (SOC) environment for threat detection and incident response. The project simulates a full SOC workflow including log ingestion, threat detection, triaging, and reporting using Splunk, pfSense, and various security tools.

Objectives
Simulate a full SOC workflow including log ingestion, threat detection, triaging, and reporting
Build a functional SOC environment using Splunk for monitoring, pfSense for network security, and detection tools like Suricata and ModSecurity
Project Timeline
Week 1: SOC Setup & Log Ingestion
Set up centralized logging server using Splunk Enterprise on Ubuntu
Configure log sources: Windows, firewall, and IDS
Deploy pfSense Firewall and configure LAN/WAN interfaces
Set up Windows Domain Controller
Week 2: SIEM Configuration & Use Case Development
Install Universal Forwarders and Sysmon on endpoints
Integrate Suricata (IDS/IPS) and ModSecurity (WAF)
Verify log flow from all sources into Splunk
Deploy SIEM platform Splunk
Configure 3-5 real-world use cases (e.g., brute force, malware infection)
Week 3: Alert Triage and Incident Management
Configure SPL rules and alerts in Splunk
Simulate attacks (SQLi for WAF, testmyids.com for Suricata)
Perform Active Directory attack simulations
Triage and analyze triggered alerts
Apply MITRE ATT&CK mapping to events
Week 4: Reporting & Final Presentation
Draft the Incident Response Playbook
Finalize the Technical Report and Executive Summary
Record the project demonstration video
Prepare KPI metrics
Perform root cause analysis for a selected incident
Network Architecture
Core Infrastructure
Component	Details
Physical Host	Laptop (Win 11 Pro) i5-1235U (10 Cores), 16GB RAM, 512GB SSD
Virtualization	VMware Workstation
SIEM Platform	Ubuntu 22.04 LTS Splunk Enterprise (IP: 192.168.254.156)
Security Gateway	pfSense 2.7.x with Suricata IDS enabled on WAN/DMZ
Virtual Machine Inventory
Hostname	Operating System	IP Address	Primary Role	Domain Status
TEAM4-DC	Win Server 2019	192.168.254.140	Domain Controller (DNS/AD)	SOC.DEPI
WEBSRV	Win Server 2019	192.168.120.100	XAMPP, DVWA, SQL Server	SOC.DEPI
TEAM4-PC1	Win 10 Enterprise	10.1.1.100	HR Workstation	SOC.DEPI
TEAM4-PC2	Win 10 Enterprise	10.2.1.100	Finance Workstation	SOC.DEPI
KALI	Kali 2025.4	192.168.250.128	VAPT / Attacker Machine	Non-Domain
Network Segmentation
Interface	Subnet Range	Gateway	VLAN / Purpose	VMnet
WAN	192.168.250.0/24	192.168.250.129	Internet / External Exposure	VMnet0
LAN	192.168.254.0/24	192.168.254.2	DataCenter / Management	VMnet1
DMZ	192.168.120.0/24	192.168.120.1	Web Hosting (WEBSRV)	VMnet2
OPT1	10.1.1.0/24	10.1.1.1	Corporate (HR Dept)	VMnet3
OPT2	10.2.1.0/24	10.2.1.1	Corporate (Finance Dept)	VMnet4
Team Roles & Responsibilities
Role	Responsibility	Primary Tools
SOC Architect	Infrastructure setup, pfSense configuration, networking	VMware, pfSense
SIEM Administrator	Splunk installation, Index management, Syslog-ng setup	Splunk, Linux (Ubuntu)
Security Engineer	IDS/IPS (Suricata) and WAF (ModSecurity) implementation	Suricata, Apache/XAMPP
Endpoint Specialist	Sysmon deployment, GPO configuration, Windows logging	Windows Server, Sysmon
Threat Hunter	Writing SPL queries, creating alerts, attack simulation	Splunk (SPL), Kali Linux
Technical Writer	Documentation, Gantt chart, Final Presentation	GitHub, LaTeX/Office
Use Cases & Detection Rules
1. Brute Force Attack Detection
Description: Detect when an adversary attempts to gain access by guessing credentials through multiple login attempts.

Data Sources:

Windows Event Log (EventCode 4625)
Linux /var/log/secure
Web Logs (HTTP 401 status codes)
SPL Rule:

splunk
index="windows_dc" EventCode=4625
| stats count min(_time) as firstTime max(_time) as lastTime by Source_Network_Address, Account_Name
| where count >= 5
| eval severity="high"
2. SQL Injection (SQLi) Monitoring
Description: Detect malicious SQL code inserted into input fields or URLs to manipulate backend databases.

Data Sources:

Web server access logs (Apache, IIS, Nginx)
WAF logs (ModSecurity)
SPL Rule:

splunk
index=* "UNION" | stats values(sourcetype) as "Source Types", count by host
3. Network Scanning Reconnaissance
Description: Detect mapping of live hosts, open ports, and services as part of the "Reconnaissance" phase.

Data Sources:

Firewall logs
IDS/IPS logs (Suricata)
Netflow data
SPL Rule:

splunk
index="suricata" "192.168.250.128"
| rex "src_ip\":\"(?<src>[^\"]+)"
| rex "dest_port\":(?<port>\d+)"
| stats dc(port) as unique_ports_hit by src
| where unique_ports_hit > 10
Installation & Setup
Prerequisites
VMware Workstation or compatible virtualization platform
Host machine with at least 16GB RAM
ISO files for required operating systems
Setup Sequence
Install Splunk Enterprise on Ubuntu
Install Universal Forwarders on all endpoints
Configure Domain Controller Environment
Install Xampp Web app server
Install pfSense firewall
Install Suricata IDS/IPS
Configure DVWA vulnerable Web app on xampp
Configure ModSecurity WAF to detect web app attacks
Write rules in Splunk to generate alerts
Configure Active Directory attack detection
Power On Sequence
pfSense
Ubuntu (Splunk)
TEAM4-DC
Endpoints
Validation
Access Splunk Web at http://192.168.254.156:8000 and verify "Data Summary" shows logs from all hosts.

Testing Scenarios
Test ID	Scenario	Expected Outcome
TC-01	Brute Force from Kali (192.168.250.128)	Splunk triggers alert after 5 failed attempts
TC-02	SQL Injection on DVWA	ModSecurity logs the attempt; Splunk displays "UNION" query in dashboard
TC-03	Nmap Scan via WAN	pfSense/Suricata logs traffic; alert generated in Splunk
Risk Assessment & Mitigation
Potential Risk	Impact	Mitigation Strategy
Resource Exhaustion	High	Limit Windows 10 VMs to 2GB RAM each
IP Address Conflicts	Medium	Maintain strict IP standard
Log Injection Failure	High	Verify connectivity between Universal Forwarders and Splunk
WAF False Positives	Medium	Test ModSecurity in "Detection Only" mode first
Key Performance Indicators (KPIs)
System Uptime: 99% availability of Splunk web interface during testing phase
Log Coverage: 100% of critical endpoints successfully forwarding logs
Detection Accuracy: Zero "false negatives" during simulated attacks
Response Time: Alert generation in Splunk within 2 minutes of security event
Future Improvements
Transition from local log storage to dedicated Syslog-ng server
Integrate automated SOAR platform to automatically block IPs in pfSense
Expand use cases beyond current 10 scenarios
Upgrade to 32GB RAM for handling increased load
Repository Structure
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
could you give all details and data in : Repository Structure
├── docs/
│ ├── project-planning.md
│ ├── network-architecture.md
│ └── use-cases.md
├── splunk/
│ ├── dashboards/
│ ├── alerts/
│ └── saved-searches/
├── scripts/
│ ├── setup/
│ └── testing/
├── configs/
│ ├── pfsense/
│ ├── suricata/
│ └── modsecurity/
└── reports/
├── technical


Team4 SOC Lab Repository Structure
All files and data that is included in each directory of the repository:

Repository Structure
├── docs/
│   ├── project-planning.md
│   ├── network-architecture.md
│   ├── use-cases.md
│   ├── installation-guide.md
│   ├── team-roles.md
│   └── risk-assessment.md
├── splunk/
│   ├── dashboards/
│   │   ├── soc-overview.xml
│   │   ├── threat-detection.xml
│   │   ├── network-monitoring.xml
│   │   └── endpoint-security.xml
│   ├── alerts/
│   │   ├── brute-force-alert.xml
│   │   ├── sqli-alert.xml
│   │   ├── network-scan-alert.xml
│   │   └── ad-attack-alert.xml
│   ├── saved-searches/
│   │   ├── failed-logins.txt
│   │   ├── network-scans.txt
│   │   ├── suspicious-activity.txt
│   │   └── privilege-escalation.txt
│   └── apps/
│       └── soc-monitoring/
│           ├── default/
│           │   ├── data/
│           │   │   ├── ui/
│           │   │   │   ├── views/
│           │   │   │   ├── nav/
│           │   │   │   └── panels/
│           │   │   └── macros/
│           │   └── app.conf
│           └── local/
│               ├── data/
│               │   ├── ui/
│               │   │   ├── views/
│               │   │   ├── nav/
│               │   │   └── panels/
│               │   └── macros/
│               └── app.conf
├── scripts/
│   ├── setup/
│   │   ├── install-splunk.sh
│   │   ├── configure-forwarders.sh
│   │   ├── setup-pfsense.sh
│   │   ├── install-suricata.sh
│   │   ├── configure-modsecurity.sh
│   │   ├── setup-domain-controller.ps1
│   │   ├── install-sysmon.ps1
│   │   └── configure-gpo.ps1
│   ├── testing/
│   │   ├── brute-force-test.py
│   │   ├── sqli-test.py
│   │   ├── nmap-scan-test.sh
│   │   ├── ad-attack-test.py
│   │   └── validate-soc.sh
│   └── automation/
│       ├── ip-blocker.py
│       ├── alert-notifier.py
│       └── log-parser.py
├── configs/
│   ├── pfsense/
│   │   ├── config.xml
│   │   ├── aliases.conf
│   │   ├── firewall-rules.conf
│   │   ├── nat-rules.conf
│   │   └── interfaces.conf
│   ├── suricata/
│   │   ├── suricata.yaml
│   │   ├── classification.config
│   │   ├── reference.config
│   │   ├── threshold.config
│   │   └── rules/
│   │       ├── local.rules
│   │       ├── emerging-scan.rules
│   │       └── emerging-malware.rules
│   ├── modsecurity/
│   │   ├── modsecurity.conf
│   │   ├── crs-setup.conf
│   │   └── rules/
│   │       ├── REQUEST-900-EXCLUSION-RULES-BEFORE-CRS.conf
│   │       ├── REQUEST-901-INITIALIZATION.conf
│   │       ├── REQUEST-910-IP-REPUTATION.conf
│   │       ├── REQUEST-911-METHOD-ENFORCEMENT.conf
│   │       ├── REQUEST-912-DOS-PROTECTION.conf
│   │       ├── REQUEST-913-SCANNER-DETECTION.conf
│   │       ├── REQUEST-920-PROTOCOL-ENFORCEMENT.conf
│   │       ├── REQUEST-921-PROTOCOL-ATTACK.conf
│   │       ├── REQUEST-930-APPLICATION-ATTACK-LFI.conf
│   │       ├── REQUEST-931-APPLICATION-ATTACK-RFI.conf
│   │       ├── REQUEST-932-APPLICATION-ATTACK-RCE.conf
│   │       ├── REQUEST-933-APPLICATION-ATTACK-PHP.conf
│   │       ├── REQUEST-941-APPLICATION-ATTACK-XSS.conf
│   │       ├── REQUEST-942-APPLICATION-ATTACK-SQLI.conf
│   │       ├── REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION.conf
│   │       ├── REQUEST-944-APPLICATION-ATTACK-JAVA.conf
│   │       ├── REQUEST-949-BLOCKING-EVALUATION.conf
│   │       ├── RESPONSE-950-DATA-LEAKAGES.conf
│   │       ├── RESPONSE-951-DATA-LEAKAGES-SQL.conf
│   │       ├── RESPONSE-952-DATA-LEAKAGES-JAVA.conf
│   │       ├── RESPONSE-953-DATA-LEAKAGES-PHP.conf
│   │       ├── RESPONSE-954-DATA-LEAKAGES-IIS.conf
│   │       ├── RESPONSE-959-BLOCKING-EVALUATION.conf
│   │       ├── RESPONSE-980-CORRELATION.conf
│   │       └── RESPONSE-999-EXCLUSION-RULES-AFTER-CRS.conf
│   ├── splunk/
│   │   ├── indexes.conf
│   │   ├── inputs.conf
│   │   ├── props.conf
│   │   ├── transforms.conf
│   │   └── deploymentclient.conf
│   └── windows/
│       ├── sysmon.xml
│       ├── audit-policy.csv
│       └── gpo-backup/
│           ├── security-settings.inf
│           └── registry-pol.reg
├── reports/
    ├── technical/
    │   ├── soc-architecture-design.md
    │   ├── implementation-guide.md
    │   ├── testing-results.md
    │   └── performance-analysis.md
    ├── executive/
    │   ├── project-summary.md
    │   ├── security-posture-assessment.md
    │   └── roi-analysis.md
    ├── incident-response/
    │   ├── incident-handbook.md
    │   ├── escalation-matrix.md
    │   ├── communication-plan.md
    │   └── containment-procedures.md
    └── compliance/
        ├── audit-findings.md
        ├── remediation-plan.md
        └── compliance-matrix.md
└── media/
    ├── diagrams/
    │   ├── network-topology.png
    │   ├── data-flow.png
    │   ├── attack-path.png
    │   └── mitre-attack-mapping.png
    ├── screenshots/
    │   ├── splunk-dashboard.png
    │   ├── pfsense-config.png
    │   ├── suricata-alerts.png
    │   └── modsecurity-logs.png
    └── videos/
        ├── demo-attack-detection.mp4
        ├── incident-response-workflow.mp4
        └── dashboard-tour.mp4
Detailed File Contents
docs/
project-planning.md
Project overview and objectives
Timeline with Gantt chart
Milestones and deliverables
Resource allocation
Risk assessment and mitigation strategies
network-architecture.md
Detailed network topology diagram
IP addressing scheme
Network segmentation details
Firewall rules and policies
Traffic flow documentation
use-cases.md
Detailed description of each use case
MITRE ATT&CK framework mapping
Detection logic and rules
Response procedures
False positive handling
installation-guide.md
Step-by-step installation instructions
Prerequisites and system requirements
Configuration details
Troubleshooting guide
Validation procedures
team-roles.md
Detailed role definitions
Responsibilities matrix
Contact information
Escalation procedures
Skill requirements
risk-assessment.md
Risk register with likelihood and impact
Risk treatment strategies
Risk acceptance criteria
Monitoring and review procedures
Business impact analysis
splunk/
dashboards/
XML configuration files for all dashboards
Panel configurations and visualizations
Drill-down configurations
Scheduled report settings
alerts/
Alert configuration files
Threshold settings
Notification configurations
Escalation procedures
Alert suppression rules
saved-searches/
Search query definitions
Search scheduling
Result formatting
Output configurations
Search optimization tips
scripts/
setup/
Automated installation scripts
Configuration scripts
Validation scripts
Environment setup scripts
Service startup scripts
testing/
Attack simulation scripts
Validation scripts
Performance testing scripts
Load testing scripts
Regression testing scripts
automation/
Response automation scripts
Alert enrichment scripts
Threat intelligence integration
Ticket creation scripts
Report generation scripts
configs/
pfsense/
Complete pfSense configuration
Firewall rules
NAT configuration
Interface settings
VPN configuration
suricata/
Suricata configuration files
Custom detection rules
Threshold settings
Classification configurations
Performance tuning settings
modsecurity/
ModSecurity configuration
OW

The content for all the files in the repository structure:


docs/project-planning.md
# Team4 SOC Lab Project Planning

## Project Overview

This project implements a Security Operations Center (SOC) environment for threat detection and incident response using Splunk, pfSense, and various security tools.

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

## Project Milestones

| Milestone | Deliverable | Due Date |
|-----------|-------------|----------|
| M1: Planning & Scope | Project Proposal & Risk Assessment | Jan 27, 2026 |
| M2: Research & Req. | Literature Review & Hardware/Software Specs | Mar 1, 2026 |
| M3: System Design | Network Topology & SIEM Architecture Design | Apr 3, 2026 |
| M4: Implementation | Functional SOC (Splunk, pfSense, WAF, IDS) | May 17, 2026 |
| M5: Final Submission | Final Report, Test Cases, & Presentation | May 24, 2026 |

## Resource Allocation

| Role | Responsibility | Primary Tools |
|------|----------------|---------------|
| SOC Architect | Infrastructure setup, pfSense configuration, networking | VMware, pfSense |
| SIEM Administrator | Splunk installation, Index management, Syslog-ng setup | Splunk, Linux (Ubuntu) |
| Security Engineer | IDS/IPS (Suricata) and WAF (ModSecurity) implementation | Suricata, Apache/XAMPP |
| Endpoint Specialist | Sysmon deployment, GPO configuration, Windows logging | Windows Server, Sysmon |
| Threat Hunter | Writing SPL queries, creating alerts, attack simulation | Splunk (SPL), Kali Linux |
| Technical Writer | Documentation, Gantt chart, Final Presentation | GitHub, LaTeX/Office |

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
docs/network-architecture.md
# Team4 SOC Lab Network Architecture

## Network Topology Overview

The SOC lab environment is designed to simulate a corporate network with multiple security zones to detect and analyze various attack vectors.

## Core Infrastructure

| Component | Details |
|-----------|---------|
| Physical Host | Laptop (Win 11 Pro) i5-1235U (10 Cores), 16GB RAM, 512GB SSD |
| Virtualization | VMware Workstation |
| SIEM Platform | Ubuntu 22.04 LTS Splunk Enterprise (IP: 192.168.254.156) |
| Security Gateway | pfSense 2.7.x with Suricata IDS enabled on WAN/DMZ |

## Virtual Machine Inventory

| Hostname | Operating System | IP Address | Primary Role | Domain Status |
|----------|------------------|------------|--------------|---------------|
| TEAM4-DC | Win Server 2019 | 192.168.254.140 | Domain Controller (DNS/AD) | SOC.DEPI |
| WEBSRV | Win Server 2019 | 192.168.120.100 | XAMPP, DVWA, SQL Server | SOC.DEPI |
| TEAM4-PC1 | Win 10 Enterprise | 10.1.1.100 | HR Workstation | SOC.DEPI |
| TEAM4-PC2 | Win 10 Enterprise | 10.2.1.100 | Finance Workstation | SOC.DEPI |
| KALI | Kali 2025.4 | 192.168.250.128 | VAPT / Attacker Machine | Non-Domain |

## Network Segmentation

| Interface | Subnet Range | Gateway | VLAN / Purpose | VMnet |
|-----------|--------------|---------|----------------|-------|
| WAN | 192.168.250.0/24 | 192.168.250.129 | Internet / External Exposure | VMnet0 |
| LAN | 192.168.254.0/24 | 192.168.254.2 | DataCenter / Management | VMnet1 |
| DMZ | 192.168.120.0/24 | 192.168.120.1 | Web Hosting (WEBSRV) | VMnet2 |
| OPT1 | 10.1.1.0/24 | 10.1.1.1 | Corporate (HR Dept) | VMnet3 |
| OPT2 | 10.2.1.0/24 | 10.2.1.1 | Corporate (Finance Dept) | VMnet4 |

## Security Controls

### Firewall Rules
- Default deny all inbound traffic
- Allow established/related connections
- Specific allow rules for required services
- Block known malicious IPs

### IDS/IPS Placement
- Suricata deployed on pfSense WAN interface
- Monitoring traffic entering and leaving the network
- Custom rules for detecting attack patterns

### Network Monitoring
- All network traffic logged to Splunk
- NetFlow data collected for traffic analysis
- Critical assets monitored for anomalous behavior

## Data Flow

1. External traffic enters through WAN interface
2. pfSense firewall filters traffic based on rules
3. Suricata inspects traffic for threats
4. Allowed traffic routed to appropriate network segment
5. Logs from all devices forwarded to Splunk
6. Security events correlated and analyzed in Splunk

docs/use-cases.md
# Team4 SOC Lab Use Cases

## Use Case 1: Brute Force Attack Detection

### Description
A brute force attack occurs when an adversary attempts to gain access to a system by guessing credentials through multiple login attempts. This can target various services such as SSH, RDP, or web application logins.

### Why It Matters
Successful brute force attacks lead to account compromise, data breaches, and ransomware deployment. Early detection allows the SOC to block the offending IP and reset compromised credentials before the attacker gains a foothold.

### MITRE ATT&CK Mapping
- Tactic: Credential Access (TA0006)
- Technique: Brute Force (T1110)
- Sub-techniques: Password Guessing (T1110.001), Password Spraying (T1110.003)

### Data Sources
- Windows Event Log (EventCode 4625) - An account failed to log on
- Linux /var/log/secure, application logs
- Web Logs: HTTP 401 status codes

### Detection Logic
Identify a high number of failed authentication attempts from a single source IP within a short time window.

### Splunk Detection Rule
```splunk
index="windows_dc" EventCode=4625
| stats count min(_time) as firstTime max(_time) as lastTime by Source_Network_Address, Account_Name
| where count >= 5
| eval severity="high"
Response Procedures
Verify if the source IP is internal or external
Block the offending IP at the firewall
Reset passwords for targeted accounts
Monitor for successful logins from blocked IP
Document the incident in the ticketing system
Use Case 2: SQL Injection (SQLi) Monitoring
Description
SQL Injection is a web vulnerability where an attacker inserts malicious SQL code into input fields or URLs to manipulate backend databases. This can result in data exfiltration, authentication bypass, or remote code execution.

Why It Matters
SQLi remains one of the most critical web application risks (OWASP Top 10). A successful attack can lead to the theft of sensitive data (PII, financial data) and full database compromise.

### MITRE ATT&CK Mapping
- Tactic: Initial Access (TA0001)
- Technique: Exploit Public-Facing Application (T1190)
- Sub-technique: Web Shell (T1505.003)

### Data Sources
- Web/Proxy Logs: Web server access logs (Apache, IIS, Nginx), proxy logs
- WAF Logs: Web Application Firewall logs (ModSecurity)
- Database Logs: SQL Server logs showing suspicious queries

### Detection Logic
Identify SQL injection patterns in web requests, such as UNION statements, SQL comments, and conditional logic.

### Splunk Detection Rule
```splunk
index=* "UNION" | stats values(sourcetype) as "Source Types", count by host
Response Procedures
Block the source IP at the WAF and firewall
Analyze the database for potential compromise
Review web application code for vulnerabilities
Implement additional input validation
Document the incident and remediation steps
Use Case 3: Network Scanning Reconnaissance
Description
Network scanning is a reconnaissance technique used by attackers to map live hosts, open ports, and running services. This is typically the first step in the "Reconnaissance" phase of the Cyber Kill Chain.

Why It Matters
While scanning itself is not inherently malicious, it is a high-fidelity precursor to an attack. Detecting scanning activity allows defenders to identify potential threats early and harden systems before an exploit is attempted.

MITRE ATT&CK Mapping
Tactic: Reconnaissance (TA0043)
Technique: Network Service Scanning (T1046)
Sub-technique: Port Scanning (T1046.001)
Data Sources
Firewall Logs: Traffic logs (allow/deny)
IDS/IPS Logs: Suricata, Snort
Netflow Data: Network flow records
Detection Logic
Identify patterns of port scanning from a single source IP to multiple destinations or ports within a short time window.

Splunk Detection Rule
splunk
index="suricata" "192.168.250.128"
| rex "src_ip\":\"(?<src>[^\"]+)"
| rex "dest_port\":(?<port>\d+)"
| stats dc(port) as unique_ports_hit by src
| where unique_ports_hit > 10
Response Procedures
Identify the source of the scan
Block the scanning IP at the firewall
Harden targeted systems against known vulnerabilities
Monitor for follow-up attacks
Document the reconnaissance activity
Use Case 4: Active Directory Attack Detection
Description
Attackers often target Active Directory to escalate privileges, move laterally, and exfiltrate data. Common attacks include Kerberoasting, Pass-the-Hash, and Golden Ticket attacks.

Why It Matters
Compromise of Active Directory can provide attackers with extensive access to the network, allowing them to move freely and access sensitive data.

MITRE ATT&CK Mapping
Tactic: Credential Access (TA0006)
Technique: Steal or Forge Kerberos Tickets (T1558)
Sub-techniques: Kerberoasting (T1558.003), Golden Ticket (T1558.001)
Data Sources
Windows Event Logs: Security logs (4768, 4769, 4624, 4625)
Domain Controller Logs: Authentication and authorization events
PowerShell Logs: Command-line auditing
Detection Logic
Identify suspicious authentication patterns, unusual service ticket requests, and anomalous account behavior.

Splunk Detection Rule
splunk
index="windows_dc" (EventCode=4769 OR EventCode=4768) Service_Name="krbtgt"
| stats count by _time, user, src_ip, Service_Name
| eval severity="high"
Response Procedures
Isolate affected accounts and systems
Reset passwords for compromised accounts
Review domain controller logs for additional indicators
Implement additional monitoring for privileged accounts
Document the incident and recovery steps


# docs/installation-guide.md

# Team4 SOC Lab Installation Guide

## Prerequisites

### Hardware Requirements
- Host machine with at least 16GB RAM
- 500GB of available disk space
- VMware Workstation or compatible virtualization platform

### Software Requirements
- Ubuntu 22.04 LTS ISO
- Windows Server 2019 ISO
- Windows 10 Enterprise ISO
- Kali Linux 2025.4 ISO
- pfSense 2.7.x ISO

## Installation Steps

### 1. Install Splunk Enterprise on Ubuntu

1. Create a new Ubuntu 22.04 LTS VM with 4GB RAM and 80GB disk
2. Install Ubuntu with default settings
3. Download Splunk Enterprise:
   ```bash
   wget -O splunk-9.1.2-linux-2.6-x86_64.deb 'https://www.splunk.com/bin/splunk/DownloadActivityServlet?architecture=x86_64&platform=linux&version=9.1.2&product=enterprise&mode=stable&filename=splunk-9.1.2-linux-2.6-x86_64.deb'
Install Splunk:
bash
sudo dpkg -i splunk-9.1.2-linux-2.6-x86_64.deb
Start Splunk:
bash
sudo /opt/splunk/bin/splunk start --accept-license
Enable boot start:
bash
sudo /opt/splunk/bin/splunk enable boot-start
Configure network interface with static IP 192.168.254.156
2. Configure pfSense Firewall
Create a new VM with 2GB RAM and 20GB disk
Install pfSense 2.7.x with default settings
Configure network interfaces:
WAN: 192.168.250.129/24
LAN: 192.168.254.2/24
DMZ: 192.168.120.1/24
OPT1: 10.1.1.1/24
OPT2: 10.2.1.1/24
Enable Suricata on WAN interface
Configure firewall rules:
Default deny all inbound traffic
Allow established/related connections
Allow outbound traffic
Specific allow rules for required services
3. Setup Windows Domain Controller
Create a new VM with 4GB RAM and 80GB disk
Install Windows Server 2019
Configure static IP 192.168.254.140
Install Active Directory Domain Services
Create domain SOC.DEPI
Configure DNS server
Create user accounts:
administrator: P@$$w0rd!
AAli: Password1
AHamdy: Password2
MMaged: Password2026!@#
SQLService: MYpassword123#
4. Setup XAMPP Web Server
Create a new VM with 4GB RAM and 60GB disk
Install Windows Server 2019
Configure static IP 192.168.120.100
Join to SOC.DEPI domain
Install XAMPP with Apache, MySQL, PHP
Install DVWA (Damn Vulnerable Web Application)
Configure ModSecurity WAF
Configure logging to C:\xampp\apache\logs\modsec_audit.log
5. Setup Windows Workstations
Create two VMs with 2GB RAM and 40GB disk each
Install Windows 10 Enterprise
Configure static IPs:
TEAM4-PC1: 10.1.1.100
TEAM4-PC2: 10.2.1.100
Join to SOC.DEPI domain
Install Splunk Universal Forwarder
Install Sysmon with configuration
6. Setup Kali Attack Machine
Create a new VM with 4GB RAM and 60GB disk
Install Kali Linux 2025.4
Configure static IP 192.168.250.128
Update system:
bash
sudo apt update && sudo apt upgrade -y
Install additional tools:
bash
sudo apt install -y nmap metasploit-framework sqlmap hydra
Validation Steps
Verify network connectivity between all VMs
Confirm log forwarding to Splunk
Test pfSense firewall rules
Validate Suricata IDS functionality
Test ModSecurity WAF rules
Verify Active Directory authentication
Confirm DVWA accessibility
Troubleshooting
Common Issues
Splunk Not Receiving Logs
Check network connectivity
Verify firewall rules allow port 9997
Confirm Universal Forwarder configuration
Check Splunk indexes configuration
pfSense Network Issues
Verify interface configuration
Check firewall rules
Confirm NAT settings
Validate routing tables
Windows Domain Issues
Check DNS configuration
Verify network connectivity
Confirm time synchronization
Check domain controller services


docs/team-roles.md
# Team4 SOC Lab Team Roles

## Role Definitions

### SOC Architect
**Primary Responsibilities:**
- Infrastructure setup and configuration
- pfSense firewall configuration
- Network design and implementation
- Security architecture development

**Skills Required:**
- Network engineering
- Firewall administration
- Virtualization technologies
- Security architecture principles

### SIEM Administrator
**Primary Responsibilities:**
- Splunk installation and configuration
- Index management
- Syslog-ng setup
- Log collection optimization

**Skills Required:**
- Splunk administration
- Linux administration
- Log management
- Query optimization

### Security Engineer
**Primary Responsibilities:**
- IDS/IPS (Suricata) implementation
- WAF (ModSecurity) configuration
- Security tool deployment
- Rule tuning and optimization

**Skills Required:**
- Intrusion detection systems
- Web application security
- Security rule development
- Threat analysis

### Endpoint Specialist
**Primary Responsibilities:**
- Sysmon deployment
- GPO configuration
- Windows logging configuration
- Endpoint security monitoring

**Skills Required:**
- Windows administration
- Endpoint security
- PowerShell scripting
- Active Directory management

### Threat Hunter
**Primary Responsibilities:**
- Writing SPL queries
- Creating alerts
- Attack simulation
- Threat analysis

**Skills Required:**
- Splunk query language (SPL)
- Penetration testing
- Threat intelligence
- Attack methodologies

### Technical Writer
**Primary Responsibilities:**
- Documentation creation
- Gantt chart development
- Final presentation preparation
- Report generation

**Skills Required:**
- Technical writing
- Documentation tools
- Presentation skills
- Project management

## Contact Information

| Role | Name | Email | Phone |
|------|------|-------|-------|
| SOC Architect | Mohammad | mohammad@team4.local | +1-555-0101 |
| SIEM Administrator | Ahmed Hamdy | ahmed.h@team4.local | +1-555-0102 |
| Security Engineer | Ahmed Atef | ahmed.a@team4.local | +1-555-0103 |
| Endpoint Specialist | Abdalaziz | abdalaziz@team4.local | +1-555-0104 |
| Threat Hunter | Mohammad | mohammad.h@team4.local | +1-555-0105 |
| Technical Writer | Mohammad | mohammad.w@team4.local | +1-555-0106 |

## Escalation Procedures

### Level 1 Escalation
- Initial incident detection
- Basic triage and analysis
- Documentation of findings
- Escalation to Level 2 if needed

### Level 2 Escalation
- In-depth analysis
- Incident containment
- Forensic investigation
- Escalation to Level 3 if needed

### Level 3 Escalation
- Critical incident management
- Executive notification
- External communication
- Incident resolution

## Skill Requirements Matrix

| Skill | SOC Architect | SIEM Admin | Security Engineer | Endpoint Specialist | Threat Hunter | Technical Writer |
|-------|---------------|------------|-------------------|--------------------|---------------|------------------|
| Network Engineering | Expert | Intermediate | Intermediate | Basic | Basic | Basic |
| Splunk Administration | Basic | Expert | Intermediate | Basic | Advanced | Basic |
| Security Tools | Intermediate | Intermediate | Expert | Intermediate | Advanced | Basic |
| Windows Administration | Basic | Intermediate | Intermediate | Expert | Intermediate | Basic |
| Linux Administration | Intermediate | Expert | Intermediate | Basic | Advanced | Basic |
| Penetration Testing | Basic | Basic | Intermediate | Basic | Expert | Basic |
| Technical Writing | Intermediate | Basic | Basic | Basic | Intermediate | Expert |
| Presentation Skills | Intermediate | Basic | Basic | Basic | Intermediate | Expert |

docs/risk-assessment.md
# Team4 SOC Lab Risk Assessment

## Risk Register

| Risk ID | Risk Description | Likelihood | Impact | Risk Level | Mitigation Strategy | Owner | Status |
|---------|------------------|------------|--------|-----------|-------------------|-------|--------|
| R001 | Resource exhaustion due to limited RAM | Medium | High | High | Limit VM resources, monitor usage | Mohammad | In Progress |
| R002 | IP address conflicts in the lab | Medium | Medium | Medium | Maintain strict IP standard | Ahmed Hamdy | Mitigated |
| R003 | Log injection failure to Splunk | Medium | High | High | Verify connectivity, test forwarding | Abdalaziz | In Progress |
| R004 | WAF false positives blocking legitimate traffic | High | Medium | Medium | Test in detection mode first | Ahmed Atef | In Progress |
| R005 | Virtualization host failure | Low | High | Medium | Regular backups, alternative host | Mohammad | Planned |
| R006 | Security tool configuration errors | Medium | Medium | Medium | Document configurations, peer review | Ahmed Atef | In Progress |
| R007 | Insider threat from team members | Low | High | Medium | Access controls, monitoring | Abdalaziz | Planned |
| R008 | Data corruption in Splunk indexes | Low | High | Medium | Regular backups, integrity checks | Ahmed Hamdy | Planned |

## Risk Treatment Strategies

### Risk Avoidance
- Avoid using production systems for testing
- Avoid connecting lab environment to production networks
- Avoid using real sensitive data in the lab

### Risk Mitigation
- Implement proper access controls
- Regular backups of configurations and data
- Documentation of all configurations and procedures
- Regular testing of backup and recovery procedures

### Risk Transfer
- Use cloud-based backup services for critical data
- Consider insurance for high-value equipment

### Risk Acceptance
- Accept low-impact risks that are cost-prohibitive to mitigate
- Document acceptance criteria and review periodically

## Risk Monitoring and Review

### Monthly Risk Review
- Review risk register for changes
- Assess effectiveness of mitigation strategies
- Identify new risks
- Update risk treatment plans

### Quarterly Risk Assessment
- Comprehensive review of all risks
- Re-evaluate likelihood and impact ratings
- Update risk assessment methodology
- Report to management

### Annual Risk Management Planning
- Review risk management framework
- Update risk appetite and tolerance
- Plan for upcoming changes
- Allocate resources for risk mitigation

## Business Impact Analysis

### Critical Systems
1. Splunk Enterprise (SIEM)
   - Impact: High
   - Recovery Time Objective (RTO): 4 hours
   - Recovery Point Objective (RPO): 1 hour

2. pfSense Firewall
   - Impact: High
   - RTO: 2 hours
   - RPO: 30 minutes

3. Active Directory Domain Controller
   - Impact: High
   - RTO: 4 hours
   - RPO: 2 hours

### Important Systems
1. Web Application Server
   - Impact: Medium
   - RTO: 8 hours
   - RPO: 4 hours

2. Windows Workstations
   - Impact: Low
   - RTO: 24 hours
   - RPO: 12 hours

## Incident Response Plan

### Incident Classification
1. Critical
   - Complete system outage
   - Data breach
   - Security compromise

2. High
   - Major system degradation
   - Partial data loss
   - Security incident

3. Medium
   - Minor system degradation
   - Isolated system failure
   - Potential security issue

4. Low
   - Minor issues
   - Documentation errors
   - Configuration discrepancies

### Response Procedures
1. Incident Detection
2. Initial Assessment
3. Incident Classification
4. Response Team Notification
5. Containment Actions
6. Investigation and Analysis
7. Resolution and Recovery
8. Post-Incident Review
9. Documentation Updates

splunk/dashboards/soc-overview.xml
xml
<dashboard version="1.1">
  <label>SOC Overview</label>
  <description>Overview of security events and system status</description>
  <row>
    <panel>
      <title>Security Events by Time</title>
      <chart>
        <search>
          <query>index=* (index=pfsense_fw OR index=suricata OR index=waf OR index=windows_dc OR index=windows_pc1 OR index=windows_pc2 OR index=windows_websrv)
| timechart count</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="charting.chart">line</option>
        <option name="charting.axisY.scale">log</option>
      </chart>
    </panel>
    <panel>
      <title>Events by Index</title>
      <chart>
        <search>
          <query>index=* (index=pfsense_fw OR index=suricata OR index=waf OR index=windows_dc OR index=windows_pc1 OR index=windows_pc2 OR index=windows_websrv)
| stats count by index</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="charting.chart">pie</option>
      </chart>
    </panel>
  </row>
  <row>
    <panel>
      <title>Top Source IPs</title>
      <table>
        <search>
          <query>index=* (index=pfsense_fw OR index=suricata) 
| stats count by src_ip
| sort -count
| head 10</query>
          <earliest>-24h@

splunk/dashboards/soc-overview.xml (continued)
xml
<latest>now</latest>
        </search>
        <option name="count">10</option>
        <option name="drilldown">none</option>
      </table>
    </panel>
    <panel>
      <title>Recent Critical Alerts</title>
      <table>
        <search>
          <query>index=* (index=pfsense_fw OR index=suricata OR index=waf OR index=windows_dc OR index=windows_pc1 OR index=windows_pc2 OR index=windows_websrv) severity="high"
| eval _time=strftime(_time, "%Y-%m-%d %H:%M:%S")
| table _time, host, source, severity
| sort -_time
| head 10</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="count">10</option>
        <option name="drilldown">none</option>
      </table>
    </panel>
  </row>
  <row>
    <panel>
      <title>Failed Logins</title>
      <chart>
        <search>
          <query>index=windows_dc EventCode=4625
| timechart count</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="charting.chart">column</option>
      </chart>
    </panel>
    <panel>
      <title>Firewall Events</title>
      <chart>
        <search>
          <query>index=pfsense_fw
| stats count by action</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="charting.chart">pie</option>
      </chart>
    </panel>
  </row>
</dashboard>
splunk/dashboards/threat-detection.xml
xml
<dashboard version="1.1">
  <label>Threat Detection</label>
  <description>Threat detection and analysis dashboard</description>
  <row>
    <panel>
      <title>Brute Force Attempts</title>
      <table>
        <search>
          <query>index=windows_dc EventCode=4625
| stats count min(_time) as firstTime max(_time) as lastTime by Source_Network_Address, Account_Name
| where count >= 5
| eval firstTime=strftime(firstTime, "%Y-%m-%d %H:%M:%S")
| eval lastTime=strftime(lastTime, "%Y-%m-%d %H:%M:%S")
| sort -count</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="count">10</option>
      </table>
    </panel>
    <panel>
      <title>SQL Injection Attempts</title>
      <table>
        <search>
          <query>index=* "UNION" OR "SELECT" OR "INSERT" OR "DELETE" OR "UPDATE"
| stats count by host, source
| sort -count</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="count">10</option>
      </table>
    </panel>
  </row>
  <row>
    <panel>
      <title>Network Scans</title>
      <table>
        <search>
          <query>index=suricata src_ip=192.168.250.128
| rex "src_ip\":\"(?<src>[^\"]+)"
| rex "dest_port\":(?<port>\d+)"
| stats dc(port) as unique_ports_hit by src
| where unique_ports_hit > 10</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="count">10</option>
      </table>
    </panel>
    <panel>
      <title>Suspicious Processes</title>
      <table>
        <search>
          <query>index=windows_dc OR index=windows_pc1 OR index=windows_pc2 OR index=windows_websrv sourcetype=XmlWinEventLog:Microsoft-Windows-Sysmon/Operational EventID=1
| xmlkv
| stats count by Image, CommandLine, host
| sort -count
| head 10</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="count">10</option>
      </table>
    </panel>
  </row>
</dashboard>
splunk/dashboards/network-monitoring.xml
xml
<dashboard version="1.1">
  <label>Network Monitoring</label>
  <description>Network traffic and security monitoring</description>
  <row>
    <panel>
      <title>Firewall Traffic by Action</title>
      <chart>
        <search>
          <query>index=pfsense_fw
| timechart count by action</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="charting.chart">area</option>
        <option name="charting.stackMode">stacked</option>
      </chart>
    </panel>
    <panel>
      <title>Top Destination Ports</title>
      <chart>
        <search>
          <query>index=pfsense_fw
| stats count by dest_port
| sort -count
| head 10</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="charting.chart">bar</option>
      </chart>
    </panel>
  </row>
  <row>
    <panel>
      <title>IDS Alerts</title>
      <table>
        <search>
          <query>index=suricata
| stats count by alert.signature, src_ip, dest_ip
| sort -count
| head 10</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="count">10</option>
      </table>
    </panel>
    <panel>
      <title>Network Protocols</title>
      <chart>
        <search>
          <query>index=pfsense_fw
| stats count by proto
| sort -count</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="charting.chart">pie</option>
      </chart>
    </panel>
  </row>
</dashboard>
splunk/dashboards/endpoint-security.xml
xml
<dashboard version="1.1">
  <label>Endpoint Security</label>
  <description>Endpoint security monitoring</description>
  <row>
    <panel>
      <title>Windows Security Events</title>
      <chart>
        <search>
          <query>index=windows_dc OR index=windows_pc1 OR index=windows_pc2 OR index=windows_websrv
| timechart count by host</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="charting.chart">line</option>
      </chart>
    </panel>
    <panel>
      <title>Process Creation Events</title>
      <table>
        <search>
          <query>index=windows_dc OR index=windows_pc1 OR index=windows_pc2 OR index=windows_websrv sourcetype=XmlWinEventLog:Microsoft-Windows-Sysmon/Operational EventID=1
| xmlkv
| stats count by host, Image
| sort -count
| head 10</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="count">10</option>
      </table>
    </panel>
  </row>
  <row>
    <panel>
      <title>Network Connections</title>
      <table>
        <search>
          <query>index=windows_dc OR index=windows_pc1 OR index=windows_pc2 OR index=windows_websrv sourcetype=XmlWinEventLog:Microsoft-Windows-Sysmon/Operational EventID=3
| xmlkv
| stats count by host, DestinationIp, DestinationPort
| sort -count
| head 10</query>
          <earliest>-24h@h</earliest>
          <latest>now</latest>
        </search>
        <option name="count">10</option>
      </table>
    </panel>
    <panel>
      <Title>Registry Changes</Title>
      <table>
        <search>
          <query>index=windows_dc OR index=windows_pc1 OR index=windows_pc2 OR index=windows_websrv sourcetype=XmlWinEventLog:Microsoft-Windows-Sysmon/Operational EventID=13
| xmlkv
| stats count by host, TargetObject
| sort -count

splunk/dashboards/endpoint-security.xml (continued)
xml
<latest>now</latest>
        </search>
        <option name="count">10</option>
      </table>
    </panel>
  </row>
</dashboard>
splunk/alerts/brute-force-alert.xml
xml
<savedsearch name="Brute Force Attack Alert">
  <search>index="windows_dc" EventCode=4625
| stats count min(_time) as firstTime max(_time) as lastTime by Source_Network_Address, Account_Name
| where count >= 5
| eval severity="high"</search>
  <earliest>-15m@m</earliest>
  <latest>now</latest>
  <alert.severity>3</alert.severity>
  <alert.suppress>0</alert.suppress>
  <alert.track>1</alert.track>
  <alert.expires>24h</alert.expires>
  <count>1</count>
  <cron_schedule>*/5 * * * *</cron_schedule>
  <dispatch.earliest_time>-15m@m</dispatch.earliest_time>
  <dispatch.latest_time>now</dispatch.latest_time>
  <display.visualizations.charting.chart>line</display.visualizations.charting.chart>
  <request.ui_dispatch_app>search</request.ui_dispatch_app>
  <request.ui_dispatch_view>search</request.ui_dispatch_view>
  <actions>
    <action email>
      <server>localhost</server>
      <subject>SOC Alert: Brute Force Attack Detected</subject>
      <priority>3</priority>
      <message>
        A brute force attack has been detected:
        
        Source IP: $row.Source_Network_Address$
        Target Account: $row.Account_Name$
        Failed Attempts: $row.count$
        First Attempt: $row.firstTime$
        Last Attempt: $row.lastTime$
        
        Please investigate immediately.
      </message>
    </action>
  </actions>
</savedsearch>
splunk/alerts/sqli-alert.xml
xml
<savedsearch name="SQL Injection Attack Alert">
  <search>index=* "UNION" OR "SELECT" OR "INSERT" OR "DELETE" OR "UPDATE"
| stats count by host, source
| where count > 0</search>
  <earliest>-15m@m</earliest>
  <latest>now</latest>
  <alert.severity>3</alert.severity>
  <alert.suppress>0</alert.suppress>
  <alert.track>1</alert.track>
  <alert.expires>24h</alert.expires>
  <count>1</count>
  <cron_schedule>*/5 * * * *</cron_schedule>
  <dispatch.earliest_time>-15m@m</dispatch.earliest_time>
  <dispatch.latest_time>now</dispatch.latest_time>
  <display.visualizations.charting.chart>line</display.visualizations.charting.chart>
  <request.ui_dispatch_app>search</request.ui_dispatch_app>
  <request.ui_dispatch_view>search</request.ui_dispatch_view>
  <actions>
    <action email>
      <server>localhost</server>
      <subject>SOC Alert: SQL Injection Attempt Detected</subject>
      <priority>3</priority>
      <message>
        A SQL injection attempt has been detected:
        
        Host: $row.host$
        Source: $row.source$
        Count: $row.count$
        
        Please investigate immediately.
      </message>
    </action>
  </actions>
</savedsearch>
splunk/alerts/network-scan-alert.xml
xml
<savedsearch name="Network Scan Alert">
  <search>index="suricata" src_ip=192.168.250.128
| rex "src_ip\":\"(?<src>[^\"]+)"
| rex "dest_port\":(?<port>\d+)"
| stats dc(port) as unique_ports_hit by src
| where unique_ports_hit > 10</search>
  <earliest>-15m@m</earliest>
  <latest>now</latest>
  <alert.severity>2</alert.severity>
  <alert.suppress>0</alert.suppress>
  <alert.track>1</alert.track>
  <alert.expires>24h</alert.expires>
  <count>1</count>
  <cron_schedule>*/5 * * * *</cron_schedule>
  <dispatch.earliest_time>-15m@m</dispatch.earliest_time>
  <dispatch.latest_time>now</dispatch.latest_time>
  <display.visualizations.charting.chart>line</display.visualizations.charting.chart>
  <request.ui_dispatch_app>search</request.ui_dispatch_app>
  <request.ui_dispatch_view>search</request.ui_dispatch_view>
  <actions>
    <action email>
      <server>localhost</server>
      <subject>SOC Alert: Network Scan Detected</subject>
      <priority>2</priority>
      <message>
        A network scan has been detected:
        
        Source IP: $row.src$
        Unique Ports Scanned: $row.unique_ports_hit$
        
        Please investigate immediately.
      </message>
    </action>
  </actions>
</savedsearch>
splunk/alerts/ad-attack-alert.xml
xml
<savedsearch name="Active Directory Attack Alert">
  <search>index="windows_dc" (EventCode=4768 OR EventCode=4769) Service_Name="krbtgt"
| stats count by _time, user, src_ip, Service_Name
| eval severity="high"</search>
  <earliest>-15m@m</earliest>
  <latest>now</latest>
  <alert.severity>3</alert.severity>
  <alert.suppress>0</alert.suppress>
  <alert.track>1</alert.track>
  <alert.expires>24h</alert.expires>
  <count>1</count>
  <cron_schedule>*/5 * * * *</cron_schedule>
  <dispatch.earliest_time>-15m@m</dispatch.earliest_time>
  <dispatch.latest_time>now</dispatch.latest_time>
  <display.visualizations.charting.chart>line</display.visualizations.charting.chart>
  <request.ui_dispatch_app>search</request.ui_dispatch_app>
  <request.ui_dispatch_view>search</request.ui_dispatch_view>
  <actions>
    <action email>
      <server>localhost</server>
      <subject>SOC Alert: Active Directory Attack Detected</subject>
      <priority>3</priority>
      <message>
        A potential Active Directory attack has been detected:
        
        User: $row.user$
        Source IP: $row.src_ip$
        Service: $row.Service_Name$
        Count: $row.count$
        
        Please investigate immediately.
      </message>
    </action>
  </actions>
</savedsearch>
splunk/saved-searches/failed-logins.txt
index=windows_dc EventCode=4625
| stats count min(_time) as firstTime max(_time) as lastTime by Source_Network_Address, Account_Name
| where count >= 5
| eval firstTime=strftime(firstTime, "%Y-%m-%d %H:%M:%S")
| eval lastTime=strftime(lastTime, "%Y-%m-%d %H:%M:%S")
| sort -count
| head 20
splunk/saved-searches/network-scans.txt
index=suricata
| rex "src_ip\":\"(?<src>[^\"]+)"
| rex "dest_port\":(?<port>\d+)"
| stats dc(port) as unique_ports_hit by src
| where unique_ports_hit > 10
| sort -unique_ports_hit
| head 20
splunk/saved-searches/suspicious-activity.txt
index=windows_dc OR index=windows_pc1 OR index=windows_pc2 OR index=windows_websrv sourcetype=XmlWinEventLog:Microsoft-Windows-Sysmon/Operational (EventID=1 OR EventID=3 OR EventID=7 OR EventID=8 OR EventID=13)
| xmlkv
| stats count by host, Image, CommandLine, DestinationIp, DestinationPort, TargetObject
| sort -count
| head 20
splunk/saved-searches/privilege-escalation.txt
index=windows_dc OR index=windows_pc1 OR index=windows_pc2 OR index=windows_websrv (EventCode=4672 OR EventCode=4673 OR EventCode=4674 OR EventCode=4688 OR EventCode=4964)
| stats count by host, Account_Name, Process_Name, Privilege_List
| sort -count
| head 20
splunk/apps/soc-monitoring/default/app.conf
ini
[ui]
is_visible = true
label = SOC Monitoring
description = Security Operations Center monitoring application

[launcher]
author = Team4
description = Security Operations Center monitoring application
version = 1.0

[package]
id = soc_monitoring
splunk/apps/soc-monitoring/default/data/ui/views/soc-overview.xml
xml
<view template="dashboard.html">
  <label>SOC Overview</label>
  <module name="AccountBar" layoutPanel="appHeader"/>
  <module name="AppBar" layoutPanel="appHeader"/>
  <module name="Message" layoutPanel="messaging">
    <param name="filter">*</param>
    <param name

splunk/apps/soc-monitoring/default/data/ui/views/soc-overview.xml (continued)
xml
<param name="maxSize">1</param>
  </module>
  <module name="TitleBar" layoutPanel="viewHeader">
    <param name="label">SOC Overview</param>
  </module>
  <module name="HiddenSearch" layoutPanel="panel_row1_col1" autoRun="true">
    <param name="search">index=* (index=pfsense_fw OR index=suricata OR index=waf OR index=windows_dc OR index=windows_pc1 OR index=windows_pc2 OR index=windows_websrv) | timechart count</param>
    <param name="earliest">-24h@h</param>
    <param name="latest">now</param>
    <module name="HiddenChartFormatter">
      <param name="chart">line</param>
      <param name="charting.axisY.scale">log</param>
      <module name="JSChart">
        <param name="width">100%</param>
        <param name="height">250px</param>
      </module>
    </module>
  </module>
  <module name="HiddenSearch" layoutPanel="panel_row1_col2" autoRun="true">
    <param name="search">index=* (index=pfsense_fw OR index=suricata OR index=waf OR index=windows_dc OR index=windows_pc1 OR index=windows_pc2 OR index=windows_websrv) | stats count by index</param>
    <param name="earliest">-24h@h</param>
    <param name="latest">now</param>
    <module name="HiddenChartFormatter">
      <param name="chart">pie</param>
      <module name="JSChart">
        <param name="width">100%</param>
        <param name="height">250px</param>
      </module>
    </module>
  </module>
</view>
scripts/setup/install-splunk.sh
bash
#!/bin/bash

# Team4 SOC Lab - Splunk Installation Script
# This script installs and configures Splunk Enterprise on Ubuntu 22.04

# Update system
echo "Updating system packages..."
sudo apt-get update -y

# Install wget if not present
echo "Installing wget..."
sudo apt-get install wget -y

# Download Splunk Enterprise
echo "Downloading Splunk Enterprise..."
cd /tmp
wget -O splunk-9.1.2-linux-2.6-x86_64.deb 'https://www.splunk.com/bin/splunk/DownloadActivityServlet?architecture=x86_64&platform=linux&version=9.1.2&product=enterprise&mode=stable&filename=splunk-9.1.2-linux-2.6-x86_64.deb'

# Install Splunk
echo "Installing Splunk Enterprise..."
sudo dpkg -i splunk-9.1.2-linux-2.6-x86_64.deb

# Create Splunk user
echo "Creating splunk user..."
sudo useradd -m splunk
sudo chown -R splunk:splunk /opt/splunk

# Start Splunk
echo "Starting Splunk..."
sudo /opt/splunk/bin/splunk start --accept-license --answer-yes --no-prompt

# Enable boot start
echo "Enabling Splunk boot start..."
sudo /opt/splunk/bin/splunk enable boot-start -user splunk

# Configure network interface
echo "Configuring network interface..."
sudo tee /etc/netplan/01-netcfg.yaml > /dev/null <<EOF
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: no
      addresses: [192.168.254.156/24]
      gateway4: 192.168.254.2
      nameservers:
        addresses: [192.168.254.140, 8.8.8.8]
EOF

# Apply network configuration
sudo netplan apply

# Configure firewall
echo "Configuring firewall..."
sudo ufw allow 22/tcp
sudo ufw allow 8000/tcp
sudo ufw allow 9997/tcp
sudo ufw --force enable

# Create indexes
echo "Creating Splunk indexes..."
sudo /opt/splunk/bin/splunk add index windows_dc -auth admin:changeme
sudo /opt/splunk/bin/splunk add index windows_pc1 -auth admin:changeme
sudo /opt/splunk/bin/splunk add index windows_pc2 -auth admin:changeme
sudo /opt/splunk/bin/splunk add index windows_websrv -auth admin:changeme
sudo /opt/splunk/bin/splunk add index pfsense_fw -auth admin:changeme
sudo /opt/splunk/bin/splunk add index suricata -auth admin:changeme
sudo /opt/splunk/bin/splunk add index waf -auth admin:changeme

# Create inputs.conf
echo "Creating inputs.conf..."
sudo mkdir -p /opt/splunk/etc/apps/SplunkForwarder/default
sudo tee /opt/splunk/etc/apps/SplunkForwarder/default/inputs.conf > /dev/null <<EOF
[ splunktcp://9997 ]
connection_host = ip
index = default
source = tcp:9997
sourcetype = tcp:9997
EOF

# Restart Splunk
echo "Restarting Splunk..."
sudo /opt/splunk/bin/splunk restart

echo "Splunk installation completed!"
echo "Access Splunk Web at: http://192.168.254.156:8000"
echo "Default username: admin"
echo "Default password: changeme"
scripts/setup/configure-forwarders.sh
bash
#!/bin/bash

# Team4 SOC Lab - Universal Forwarder Configuration Script
# This script configures Splunk Universal Forwarders on all endpoints

# Configure Windows endpoints
echo "Configuring Windows endpoints..."

# Create deployment server configuration
sudo tee /opt/splunk/etc/apps/SplunkForwarder/default/deploymentclient.conf > /dev/null <<EOF
[deployment-client]
[target-broker:deploymentServer]
targetUri = 192.168.254.156:8089
EOF

# Create server class configuration
sudo mkdir -p /opt/splunk/etc/deployment-apps/windows_dc/default
sudo tee /opt/splunk/etc/deployment-apps/windows_dc/default/inputs.conf > /dev/null <<EOF
[WinEventLog://Security]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
index = windows_dc
renderXml = true

[WinEventLog://Application]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
index = windows_dc
renderXml = true

[WinEventLog://System]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
index = windows_dc
renderXml = true
EOF

sudo mkdir -p /opt/splunk/etc/deployment-apps/windows_pc1/default
sudo tee /opt/splunk/etc/deployment-apps/windows_pc1/default/inputs.conf > /dev/null <<EOF
[WinEventLog://Security]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
index = windows_pc1
renderXml = true

[WinEventLog://Application]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
index = windows_pc1
renderXml = true

[WinEventLog://System]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
index = windows_pc1
renderXml = true
EOF

sudo mkdir -p /opt/splunk/etc/deployment-apps/windows_pc2/default
sudo tee /opt/splunk/etc/deployment-apps/windows_pc2/default/inputs.conf > /dev/null <<EOF
[WinEventLog://Security]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
index = windows_pc2
renderXml = true

[WinEventLog://Application]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
index = windows_pc2
renderXml = true

[WinEventLog://System]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
index = windows_pc2
renderXml = true
EOF

sudo mkdir -p /opt/splunk/etc/deployment-apps/windows_websrv/default
sudo tee /opt/splunk/etc/deployment-apps/windows_websrv/default/inputs.conf > /dev/null <<EOF
[WinEventLog://Security]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
index = windows_websrv
renderXml = true

[WinEventLog://Application]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
index = windows_websrv
renderXml = true

[WinEventLog://System]
disabled = 0
start_from = oldest
current_only = 0
checkpointInterval = 5
index = windows_websrv
renderXml = true

[monitor://C:\xampp\apache\logs\modsec_audit.log]
disabled = 0

scripts/setup/configure-forwarders.sh (continued)
bash
index = waf
sourcetype = modsecurity
crcSalt = <SOURCE>
EOF

# Create serverclass.conf
sudo tee /opt/splunk/etc/system/local/serverclass.conf > /dev/null <<EOF
[serverClass:windows_dc]
whitelist.0 = 192.168.254.140
restartSplunkWeb = 0
restartSplunkd = 1
stateOnClient = enabled

[serverClass:windows_dc:app:windows_dc]
repositoryLocation = /opt/splunk/etc/deployment-apps/windows_dc
stateOnClient = enabled

[serverClass:windows_pc1]
whitelist.0 = 10.1.1.100
restartSplunkWeb = 0
restartSplunkd = 1
stateOnClient = enabled

[serverClass:windows_pc1:app:windows_pc1]
repositoryLocation = /opt/splunk/etc/deployment-apps/windows_pc1
stateOnClient = enabled

[serverClass:windows_pc2]
whitelist.0 = 10.2.1.100
restartSplunkWeb = 0
restartSplunkd = 1
stateOnClient = enabled

[serverClass:windows_pc2:app:windows_pc2]
repositoryLocation = /opt/splunk/etc/deployment-apps/windows_pc2
stateOnClient = enabled

[serverClass:windows_websrv]
whitelist.0 = 192.168.120.100
restartSplunkWeb = 0
restartSplunkd = 1
stateOnClient = enabled

[serverClass:windows_websrv:app:windows_websrv]
repositoryLocation = /opt/splunk/etc/deployment-apps/windows_websrv
stateOnClient = enabled
EOF

# Restart Splunk
echo "Restarting Splunk..."
sudo /opt/splunk/bin/splunk restart

echo "Universal Forwarder configuration completed!"
echo "Windows endpoints will now forward logs to Splunk."
scripts/setup/setup-pfsense.sh
bash
#!/bin/bash

# Team4 SOC Lab - pfSense Configuration Script
# This script configures pfSense firewall and Suricata IDS

# Note: This script should be run on the pfSense system

# Configure network interfaces
echo "Configuring network interfaces..."

# WAN Interface
cat > /tmp/wan_config.xml <<EOF
<interface>
  <wan>
    <enable>1</enable>
    <if>em0</if>
    <ipaddr>dhcp</ipaddr>
    <subnet>24</subnet>
    <gateway></gateway>
    <descr>WAN</descr>
  </wan>
</interface>
EOF

# LAN Interface
cat > /tmp/lan_config.xml <<EOF
<interface>
  <lan>
    <enable>1</enable>
    <if>em1</if>
    <ipaddr>192.168.254.2</ipaddr>
    <subnet>24</subnet>
    <gateway></gateway>
    <descr>LAN</descr>
  </lan>
</interface>
EOF

# DMZ Interface
cat > /tmp/dmz_config.xml <<EOF
<interface>
  <opt1>
    <enable>1</enable>
    <if>em2</if>
    <ipaddr>192.168.120.1</ipaddr>
    <subnet>24</subnet>
    <gateway></gateway>
    <descr>DMZ</descr>
  </opt1>
</interface>
EOF

# OPT1 Interface (HR)
cat > /tmp/opt1_config.xml <<EOF
<interface>
  <opt2>
    <enable>1</enable>
    <if>em3</if>
    <ipaddr>10.1.1.1</ipaddr>
    <subnet>24</subnet>
    <gateway></gateway>
    <descr>HR</descr>
  </opt2>
</interface>
EOF

# OPT2 Interface (Finance)
cat > /tmp/opt2_config.xml <<EOF
<interface>
  <opt3>
    <enable>1</enable>
    <if>em4</if>
    <ipaddr>10.2.1.1</ipaddr>
    <subnet>24</subnet>
    <gateway></gateway>
    <descr>Finance</descr>
  </opt3>
</interface>
EOF

# Configure firewall rules
echo "Configuring firewall rules..."

cat > /tmp/firewall_rules.xml <<EOF
<filter>
  <rule>
    <id>1</id>
    <type>pass</type>
    <interface>wan</interface>
    <protocol>tcp</protocol>
    <source>
      <any>1</any>
    </source>
    <destination>
      <address>192.168.254.156</address>
      <port>8000</port>
    </destination>
    <descr>Allow Splunk Web Access</descr>
  </rule>
  <rule>
    <id>2</id>
    <type>pass</type>
    <interface>wan</interface>
    <protocol>tcp</protocol>
    <source>
      <any>1</any>
    </source>
    <destination>
      <address>192.168.120.100</address>
      <port>80</port>
    </destination>
    <descr>Allow Web Server Access</descr>
  </rule>
  <rule>
    <id>3</id>
    <type>pass</type>
    <interface>lan</interface>
    <protocol>any</protocol>
    <source>
      <network>lan</network>
    </source>
    <destination>
      <any>1</any>
    </destination>
    <descr>Allow LAN to Any</descr>
  </rule>
  <rule>
    <id>4</id>
    <type>pass</type>
    <interface>opt1</interface>
    <protocol>any</protocol>
    <source>
      <network>opt1</network>
    </source>
    <destination>
      <any>1</any>
    </destination>
    <descr>Allow HR to Any</descr>
  </rule>
  <rule>
    <id>5</id>
    <type>pass</type>
    <interface>opt2</interface>
    <protocol>any</protocol>
    <source>
      <network>opt2</network>
    </source>
    <destination>
      <any>1</any>
    </destination>
    <descr>Allow Finance to Any</descr>
  </rule>
  <rule>
    <id>6</id>
    <type>pass</type>
    <interface>lan</interface>
    <protocol>any</protocol>
    <source>
      <network>lan</network>
    </source>
    <destination>
      <network>opt1</network>
    </destination>
    <descr>Allow LAN to HR</descr>
  </rule>
  <rule>
    <id>7</id>
    <type>pass</type>
    <interface>lan</interface>
    <protocol>any</protocol>
    <source>
      <network>lan</network>
    </source>
    <destination>
      <network>opt2</network>
    </destination>
    <descr>Allow LAN to Finance</descr>
  </rule>
  <rule>
    <id>8</id>
    <type>pass</type>
    <interface>lan</interface>
    <protocol>any</protocol>
    <source>
      <network>lan</network>
    </source>
    <destination>
      <network>dmz</network>
    </destination>
    <descr>Allow LAN to DMZ</descr>
  </rule>
</filter>
EOF

# Configure logging to Splunk
echo "Configuring logging to Splunk..."

cat > /tmp/syslog_config.xml <<EOF
<syslog>
  <remoteserver>
    <ipaddr>192.168.254.156</ipaddr>
    <port>514</port>
    <protocol>udp</protocol>
    <logfilter>
      <facility>all</facility>
      <severity>info</severity>
      <program>*</program>
    </logfilter>
  </remoteserver>
</syslog>
EOF

echo "pfSense configuration completed!"
echo "Please apply these configurations through the pfSense web interface."
scripts/setup/install-suricata.sh
bash
#!/bin/bash

# Team4 SOC Lab - Suricata Installation Script
# This script installs and configures Suricata IDS on pfSense

# Update system
echo "Updating system packages..."
pkg update -f

# Install Suricata
echo "Installing Suricata..."
pkg install -y suricata

# Enable Suricata on boot
echo "Enabling Suricata on boot..."
sysrc suricata_enable="YES"

# Create Suricata configuration directory
mkdir -p /usr/local/etc/suricata

# Download Suricata configuration
echo "Downloading Suricata configuration..."
fetch -o /usr/local/etc/suricata/suricata.yaml https://raw.githubusercontent.com/OISF/suricata/master/suricata.yaml

# Configure Suricata
echo "Configuring Suricata..."

cat > /usr/local/etc/suricata/suricata.yaml <<EOF
# Suricata configuration file for Team4 SOC Lab

%YAML 1.1
---

# General settings
af

scripts/setup/install-suricata.sh (continued)
bash
# Suricata configuration file for Team4 SOC Lab

%YAML 1.1
---

# General settings
af-packet:
  - interface: em0  # WAN interface
    cluster-id: 99
    cluster-type: cluster_flow
    defrag: yes
    mmap: yes
    ring-size: 200000
    buffer-size: 64512
    promisc: no
    checksum-checks: auto
    threads: auto
    pfring: no

# Logging settings
outputs:
  - fast:
      enabled: yes
      filename: fast.log
      append: yes
  - eve-log:
      enabled: yes
      type: file
      filename: eve.json
      types:
        - alert:
            payload: yes
            payload-printable: yes
            http: yes
            tls: yes
            ssh: yes
        - http:
            extended: yes
            custom: [Accept-Encoding, Accept-Language, User-Agent]
        - dns:
            version: 2
            requests: yes
            responses: yes
        - tls:
            extended: yes
        - files:
            force-magic: no
            force-md5: no
        - flow
  - stats:
      enabled: yes
      filename: stats.log
      totals: yes
      threads: no

# Detection engine settings
detect:
  profile: high
  custom-values:
    toclient-chunk-size: 2560
    toserver-chunk-size: 2560
  sgh-mpm-context: auto
  inspection-recursion-limit: 3000
  custom-non-detectable: yes
  prefilter:
    default: mpm
  grouping:
    tcp-whitelist: 53, 80, 139, 443, 445, 1433, 993
    udp-whitelist: 53, 135, 137, 138, 139, 5060
  profiling:
    inspected-packets:
      max: 10000
      sort: avg_ticks
    rules:
      max: 10000
      sort: max_ticks

# Threshold settings
thresholds:
  memcap: 1048576
  detect:
    rate-limit: 5
    burst-limit: 10
  suppress:
    rate-limit: 5
    burst-limit: 10

# Logging settings
logging:
  default-log-level: notice
  outputs:
    - console:
        enabled: yes
    - file:
        enabled: yes
        filename: /var/log/suricata/suricata.log

# PfSense integration
pfsense:
  enable: yes
  interface: em0
  home_net: "[192.168.0.0/16,10.0.0.0/8,172.16.0.0/12]"
  external_net: "!$HOME_NET"
EOF

# Create Suricata rules directory
mkdir -p /usr/local/etc/suricata/rules

# Download emerging threats rules
echo "Downloading emerging threats rules..."
fetch -o /usr/local/etc/suricata/rules/emerging-all.rules https://rules.emergingthreats.net/open/suricata/emerging-all.rules

# Create custom rules
echo "Creating custom Suricata rules..."

cat > /usr/local/etc/suricata/rules/local.rules <<EOF
# Team4 SOC Lab - Custom Suricata Rules

# Detect Nmap scans
alert tcp any any -> \$HOME_NET any (msg:"Potential Nmap Scan"; flags:0; ack:0; threshold:type both, track by_src, count 20, seconds 10; sid:1000001; rev:1;)

# Detect SQL injection attempts
alert tcp any any -> \$HOME_NET 80 (msg:"SQL Injection Attempt"; content:"UNION"; nocase; http_client_body; sid:1000002; rev:1;)

# Detect brute force SSH attempts
alert tcp any any -> \$HOME_NET 22 (msg:"SSH Brute Force Attempt"; threshold:type both, track by_src, count 5, seconds 60; sid:1000003; rev:1;)

# Detect suspicious user agents
alert tcp any any -> \$HOME_NET 80 (msg:"Suspicious User Agent"; content:"sqlmap"; nocase; http_user_agent; sid:1000004; rev:1;)

# Detect directory traversal attempts
alert tcp any any -> \$HOME_NET 80 (msg:"Directory Traversal Attempt"; content:"../"; http_uri; nocase; sid:1000005; rev:1;)
EOF

# Configure Suricata to log to Splunk
echo "Configuring Suricata to log to Splunk..."

cat > /usr/local/etc/suricata/suricata.yaml <<'EOF'
# Logging to Splunk
logging:
  outputs:
    - syslog:
        enabled: yes
        facility: local0
        level: info
        server: 192.168.254.156
        port: 514
EOF

# Start Suricata
echo "Starting Suricata..."
service suricata start

# Configure pfSense to forward logs to Splunk
echo "Configuring pfSense to forward logs to Splunk..."

cat > /tmp/syslog_config.php <<'EOF'
<?php
require_once("config.inc");
require_once("functions.inc");
require_once("filter.inc");

// Configure syslog to forward to Splunk
global $config;
$config['syslog']['remoteserver'] = "192.168.254.156";
$config['syslog']['remoteserver2'] = "";
$config['syslog']['remoteserver3'] = "";
$config['syslog']['sourceip'] = "";
$config['syslog']['ipproto'] = "udp";
$config['syslog']['filter'] = "";
$config['syslog']['descriptions'] = "1";
$config['syslog']['enable'] = "1";
$config['syslog']['logall'] = "1";

write_config();
system_syslog_start();
?>
EOF

echo "Suricata installation and configuration completed!"
echo "Suricata is now monitoring network traffic and forwarding alerts to Splunk."
scripts/setup/configure-modsecurity.sh
bash
#!/bin/bash

# Team4 SOC Lab - ModSecurity Configuration Script
# This script configures ModSecurity WAF on the web server

# Note: This script should be run on the WEBSRV Windows server with XAMPP

# Create ModSecurity configuration directory
mkdir -p C:/xampp/apache/conf/modsecurity.d

# Download ModSecurity configuration
echo "Downloading ModSecurity configuration..."
curl -o C:/xampp/apache/conf/modsecurity.conf https://raw.githubusercontent.com/SpiderLabs/ModSecurity/master/modsecurity.conf-recommended

# Configure ModSecurity
echo "Configuring ModSecurity..."

cat > C:/xampp/apache/conf/modsecurity.conf <<'EOF'
# Team4 SOC Lab - ModSecurity Configuration

# -- Rule engine initialization --------------------------------------------------

# Enable ModSecurity, attaching it to every transaction. Use detection
# only to start with, because that minimises the chances of breaking
# existing applications.
SecRuleEngine DetectionOnly

# -- Request body handling ------------------------------------------------------

# Allow ModSecurity to access request bodies. If you don't, ModSecurity
# won't be able to see any POST parameters, which opens a large security
# hole for attackers, because most SQL Injection is done via POST.
SecRequestBodyAccess On

# Max request body size we will accept for buffering. If you support
# file uploads then the value given on the first line has to be as large
# as the largest file you are willing to accept. The second value applies
# to buffered request bodies, as those might become very large.
SecRequestBodyLimit 13107200
SecRequestBodyNoFilesLimit 131072

# Store up to 128 KB of request body data in memory. When the
# body gets larger, response will be rejected (or logged with an error).
SecRequestBodyInMemoryLimit 131072

# What do do if the request body size is above our configured limit.
# In production, you might want to reject the request, but in testing
# you want to log the problem and continue.
SecRequestBodyLimitAction Reject

# Verify that we've correctly processed the request body.
# As a rule of thumb, when failing to process a request body
# you should reject the request (when deployed in blocking mode)
# or log the event (when deployed in detection-only mode).
SecRequestBodyProcessor URLENCODED|MULTIPART|XML

# -- Response body handling -----------------------------------------------------

# Allow ModSecurity to access response bodies. You should have this
# enabled if you need to examine response content, for example to
# detect data leakage.
SecResponseBodyAccess On

# Which response MIME types do you want to inspect? You should add the
# types that will be used for a specific application, and for general
# purpose crawling you might want to inspect commonly used types.
SecResponseBodyMimeType text/plain text/html text/xml

# Buffer response bodies of up to 512 KB in length.
SecResponseBodyLimit 524288

# What happens when we encounter a response body larger than the
# configured limit? By default, we process what we have and let the
# rest go. In production, you might want to reject the request, but
# in testing you want to log the problem and continue.
SecResponseBodyLimitAction ProcessPartial

