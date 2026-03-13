# Cloud SIEM Lab: Microsoft Sentinel SSH Brute Force Detection

## 📌 Project Overview
As part of my cybersecurity portfolio, I built a cloud-based Security Information and Event Management (SIEM) laboratory in Microsoft Azure. The objective was to deploy a purposely vulnerable Linux virtual machine (a honeypot), expose it to the public internet, and monitor the live, automated attacks targeting it. 

By configuring Microsoft Sentinel to ingest Syslog data, I authored custom Kusto Query Language (KQL) detections to identify and alert on SSH brute-force campaigns in near real-time, mapping the malicious behavior to the MITRE ATT&CK framework.

**Inspired by:** [Mad Hat's SOC/SIEM Tutorial](https://www.youtube.com/watch?v=IJUkGuirTGQ) (Adapted from Windows/RDP to Linux/SSH).

## 🛠️ Technologies & Tools Used
* **Cloud Provider:** Microsoft Azure
* **Infrastructure:** Linux (Ubuntu 24.04), Network Security Groups (NSGs)
* **SIEM:** Microsoft Sentinel, Log Analytics Workspace
* **Log Ingestion:** Azure Monitor Agent (AMA), Syslog
* **Query Language:** Kusto Query Language (KQL)
* **Frameworks:** MITRE ATT&CK

---

## 🚀 Step-by-Step Implementation

### 1. Infrastructure Setup & Honeypot Deployment
I provisioned an **Ubuntu 24.04** virtual machine (`SIEMProject1`) in the Azure Southeast Asia region. This machine was assigned a public IP address to ensure it was reachable from the outside world.

![Virtual Machine Deployed](Screenshot 2026-03-11 090606.png)

To guarantee the honeypot attracted live internet traffic, I intentionally misconfigured the Network Security Group (NSG) by opening **Port 22 (SSH)** and **Port 3389 (RDP)** to the public internet (`Source: Any`). This essentially placed a target on the machine's back for automated scanners.

![Vulnerable NSG Configuration](Screenshot 2026-03-11 084254.png)

### 2. Log Management & Telemetry Pipeline
With the target infrastructure running, I deployed a **Log Analytics Workspace** (`Project-LogAnalytics`) and attached **Microsoft Sentinel** to act as the centralized database for all security events.

![Sentinel Workspace](Screenshot 2026-03-11 084426.png)

To transport the authentication logs (`auth.log`) from the Linux machine into Sentinel, I configured the **Azure Monitor Agent (AMA)** via Sentinel's Data Connectors to ingest **Syslog** events. 

![Syslog via AMA Connected](Screenshot 2026-03-11 085232.png)

### 3. Custom Detection Engineering (KQL)
To simulate the role of a SOC Analyst, I utilized Kusto Query Language (KQL) to author a custom Analytics Rule named **"Failed Sign In"**.

![Analytics Rule Wizard - General](Screenshot 2026-03-11 085404.png)

I designed the rule logic to specifically hunt for SSH authentication failures. The KQL query filters the Syslog for the `auth` facility, looks for messages containing `"Failed password"`, and counts the attempts by `Computer` over 5-minute intervals. If the attempts exceed 5, an alert is triggered.

![Analytics Rule Logic - KQL Query](Screenshot 2026-03-11 085420.png)

Before deploying the rule, I mapped the detection to the MITRE ATT&CK framework under the **Credential Access** tactic. The rule was scheduled to run every 5 minutes to ensure near real-time detection.

![Analytics Rule - Review and Create](Screenshot 2026-03-11 085526.png)

### 4. Incident Generation & MITRE ATT&CK Mapping
Because the VM was exposed to the open internet, threat actors found it almost immediately. The custom detection rule began firing aggressively, generating nearly **6,000 active incidents** as automated botnets attempted to brute-force the SSH service.

![Active Incidents - Failed Sign Ins](Screenshot 2026-03-11 090130.png)

Within Sentinel, I mapped these real-world attacks against the MITRE ATT&CK framework. The primary behaviors observed aligned with:
* **Initial Access: Exploit Public-Facing Application (T1190)**
* **Execution: Command and Scripting Interpreter (T1059)**

![MITRE - Exploit Public-Facing Application](Screenshot 2026-03-11 084923.png)

## 💡 Conclusion & Key Takeaways
This lab provided hands-on, practical experience in cloud security engineering. Through this project, I successfully:
1. Provisioned and deliberately exposed cloud infrastructure to attract live threat actors.
2. Established a continuous log ingestion pipeline using AMA and Syslog.
3. Authored custom detection logic using KQL to identify brute-force patterns.
4. Analyzed live attack data and mapped threat actor behavior to industry-standard frameworks (MITRE ATT&CK).
