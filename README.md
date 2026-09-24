# Wazuh + Active Directory Security Monitoring Lab

## Overview
This project is **Phase 2 of ADShield**, a hands-on security monitoring lab built to understand how a SIEM (Security Information and Event Management) solution integrates with a Windows Active Directory environment. **Wazuh** was deployed on a dedicated Ubuntu server VM to monitor a domain-joined Windows client, with the goal of collecting endpoint logs and Active Directory-related activity in real time.

## Architecture

<img width="2200" height="2486" alt="architecture diagram — wazuh-siem-architecture" src="https://github.com/user-attachments/assets/730ee69d-6c66-4f58-bf3c-226333fe120c" />


The lab runs inside an isolated **VirtualBox Host-Only Network** (`192.168.56.0/24`) with two VMs:

| Component | Role | IP |
|---|---|---|
| **Windows Client VM** (WIN-CLIENT) | Domain-joined endpoint running the Wazuh Agent (Agent ID: 001) | `192.168.56.102` |
| **Wazuh Server VM** (Ubuntu Linux) | Runs Wazuh Manager, Indexer, and Dashboard | `192.168.56.103` |

**Data flow:**
`Windows Endpoint → Wazuh Agent 001 → Wazuh Manager → Wazuh Indexer → Wazuh Dashboard`

1. The **Wazuh Agent** on the Windows Client collects local logs and events — Windows Security Event Logs, System/Application logs, and authentication/process events — continuously in real time, and forwards them to the Wazuh Manager over **TCP 1514**.
2. The **Wazuh Manager** receives agent data, applies rules and decoders, generates security alerts, and manages agent registration.
3. The **Wazuh Indexer** (OpenSearch-based data store) stores processed events and alerts, enabling fast search and correlation over **TCP 9200**.
4. The **Wazuh Dashboard** visualizes security events for monitoring, alert triage, and threat hunting, accessible over **HTTPS** at `https://192.168.56.103`.
5. The **Admin/Host laptop** (running the VirtualBox host) accesses the Wazuh Dashboard via browser over HTTPS.

This is a single-node Wazuh deployment (Manager, Indexer, and Dashboard all on one server VM), suitable for a lab/learning environment.

## Tools Used
- Wazuh (SIEM — Manager, Indexer, Dashboard, and Agent)
- Ubuntu Linux (Wazuh Server VM)
- Windows 10/11 (domain-joined client, Wazuh Agent)
- Active Directory Domain Services
- Oracle VirtualBox (Host-Only Network, isolated lab environment)

## What I Did
1. Set up two virtual machines on an isolated VirtualBox Host-Only Network — a **Windows Client VM** (domain-joined) and an **Ubuntu Server VM**
2. Verified network connectivity between the client and server (`ping`, `ipconfig`)
3. Installed the **Wazuh Manager, Indexer, and Dashboard** on the Ubuntu server VM
4. Deployed and registered the **Wazuh Agent** (Agent ID: 001) on the Windows Client VM
5. Configured the agent to forward Windows Security Event Logs, System/Application logs, and authentication/process events to the manager over TCP 1514
6. Verified agent connectivity and confirmed the agent status as **Active** on the Wazuh dashboard
7. Reviewed the Wazuh dashboard overview — agent summary, alert severity breakdown, and endpoint security modules (Configuration Assessment, Malware Detection, File Integrity Monitoring, Threat Hunting, Vulnerability Detection, MITRE ATT&CK mapping)

## Goal
The objective of this lab was to **monitor a domain-joined Windows endpoint's activity and login attempts** through Wazuh, laying the groundwork for detection engineering — understanding how endpoint logs flow into a SIEM before writing custom detection rules.

## Screenshots

**Server Manager Dashboard (Windows Client VM)**

<img width="1309" height="939" alt="screenshots 01-server-manager-dashboard" src="https://github.com/user-attachments/assets/ebd58e93-5ed3-4cd4-a509-6b0eb5d3e05a" />


**VM Setup — Client & Server IP Configuration**
<img width="1917" height="995" alt="screenshots 02-vm-setup-ip-config" src="https://github.com/user-attachments/assets/502e4607-1939-4c85-8687-fbb0d476b95b" />


**Connectivity Test — Client to Wazuh Server**

<img width="1917" height="1028" alt="screenshots 03-cl-to-dc01-connectivity-test" src="https://github.com/user-attachments/assets/255129b3-3965-42f5-b5e8-a1ba2fe3bb34" />


**Wazuh Agent Registration**

<img width="623" height="370" alt="screenshots 04-wazuh-agent-registration" src="https://github.com/user-attachments/assets/c8bce3ea-b13d-4d8e-95d0-ffb922fdbf5a" />


**Wazuh Agent Config (ossec.conf)**

<img width="1203" height="837" alt="screenshots 05-ossec-config" src="https://github.com/user-attachments/assets/f792a22d-e3b2-41b2-a3db-79526760cd60" />


**Wazuh Login Page**

<img width="1917" height="1022" alt="screenshots 06-wazuh-login-page" src="https://github.com/user-attachments/assets/da29352d-3be3-4920-bfe9-5b7a5fa05a11" />


**Wazuh Dashboard — Overview**

<img width="1188" height="473" alt="screenshots 07-wazuh-indexer-status" src="https://github.com/user-attachments/assets/15daf32c-1c68-4328-958f-04f83300a734" />


## Current Status
- Infrastructure setup and agent-to-manager communication are fully working
- Agent is actively reporting to the Wazuh manager (visible on dashboard: Active Agents = 1)
- Custom detection rules/alerts for specific AD login events are the planned next step

## What I Learned
This project gave me hands-on experience deploying a SIEM in a Windows/Active Directory environment — from network setup and agent deployment to verifying log ingestion on the Wazuh dashboard. It also gave me a clearer picture of how endpoint monitoring pipelines (Agent → Manager → Indexer → Dashboard) are built before diving into custom detection engineering.

## Next Steps
- Configure custom Wazuh rules to detect failed/successful AD logon events (Event ID 4624/4625)
- Set up alerting thresholds for suspicious login activity (e.g. multiple failed attempts)
- Explore the MITRE ATT&CK mapping module for detected events
