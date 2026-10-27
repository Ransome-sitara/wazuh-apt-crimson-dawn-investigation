# Wazuh APT Investigation: Operation Crimson Dawn

![Wazuh Version](https://img.shields.io/badge/Wazuh-v4.14.8-blue)
![Framework](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red)
![Platform](https://img.shields.io/badge/Platform-Docker%20%7C%20Linux%20%7C%20Windows-lightgrey)
![Status](https://img.shields.io/badge/Status-Completed-success)

## Project Overview
This repository contains the technical investigation and executive reporting for a simulated Advanced Persistent Threat (APT) campaign known as "Operation Crimson Dawn." 

Acting as a Senior SOC Analyst, the objective was to detect, investigate, and contain a multi-stage cyberattack utilizing Wazuh SIEM. The investigation covers the Cyber Kill Chain, from initial reconnaissance and brute-force attacks to lateral movement and Command-and-Control (C2) communication.

## Technical Architecture

| Component | Environment | Role |
| :--- | :--- | :--- |
| **Wazuh Manager** | Kali Linux (Docker) | Central SIEM processing, rule matching, and API management. |
| **Wazuh Indexer** | Kali Linux (Docker) | OpenSearch backend for log retention and alert indexing. |
| **Wazuh Dashboard** | Kali Linux (Docker) | Web interface for threat hunting and visualization. |
| **Ubuntu Web Server** | Docker Container | Simulated vulnerable target for initial breach and persistence. |
| **Windows Endpoint** | Windows 11 Host | Simulated target for lateral movement and SMB enumeration. |

## Investigation Phases Covered

1.  **Reconnaissance & Initial Access:** Detected active Nmap scanning (T1595) and SSH brute-force attempts (T1110).
2.  **Persistence & Masquerading:** Identified File Integrity Monitoring (FIM) alerts for malicious script creation (`/tmp/backup.sh`) and cron job modification (T1053.003, T1036).
3.  **Lateral Movement:** Analyzed Windows Event ID 4625 (Failed Logon) and SMB admin share access originating from the compromised Ubuntu server (T1021.002).
4.  **Threat Intelligence Integration:** Developed and validated a custom Wazuh rule (Rule ID: 100100) to detect traffic from a known malicious C2 IP address (`10.10.10.10`).

## Key Evidence & Findings

The full investigation is documented in the `APT_Crimson_Dawn_Report.pdf` found in this repository. All technical evidence was captured directly from the Wazuh backend and CLI tools to ensure data integrity.

### 1. Reconnaissance
Detected Nmap service scanning against the Wazuh Manager ports.
*(See `/image/01_Recon_Scan.png`)*

### 2. File Integrity Monitoring (Persistence)
Wazuh FIM successfully decoded the creation of the malicious `backup.sh` file.
*(See `/image/02_Persistence_FIM_Alert.png`)*

### 3. Lateral Movement
Correlated Windows logon failures from the Ubuntu Web Server targeting the Administrator account.
*(See `/image/03_Lateral_Movement_Logs.png`)*

### 4. Custom Threat Intelligence Rule
Validated custom Rule 100100 triggering on the APT's known C2 IP.
*(See `/image/05_Custom_Rule.png`)*

## Repository Structure
```text
├── README.md                        # Project documentation (this file)
├── APT_Report.md                    # Full SOC Report (Markdown format)
├── APT_Crimson_Dawn_Report.pdf      # Executive PDF Report
└── image/                           # Screenshots proving lab execution
    ├── 01_Recon_Scan.png
    ├── 01_Recon_and_SSH_Failures.png
    ├── 02_Persistence_FIM_Alert.png
    ├── 03_Lateral_Movement_Logs.png
    └── 05_Custom_Rule.png
```


##  Technical Challenges Overcome

During this lab, several real-world deployment challenges were addressed:

    Docker API Disconnects: Bypassed a failed Wazuh Dashboard API connection by querying the OpenSearch Indexer directly via curl to extract and verify alert data.

    Docker Container Limits: Utilized isolated Wazuh logtest containers to simulate and validate Wazuh decoders and custom rules without breaking the production Manager configuration.

    XML Configuration Stability: Managed complex Wazuh Manager XML configurations, ensuring that injected custom rules and cluster settings maintained valid syntax.

## Tools Used

    Wazuh (v4.14.8): SIEM and XDR platform.

    Docker & Docker Compose: Container orchestration.

    Nmap: Network reconnaissance and port scanning.

    MITRE ATT&CK Framework: Threat mapping and analysis.

Disclaimer: This lab was conducted in an isolated, authorized environment using synthetic data for educational purposes.
