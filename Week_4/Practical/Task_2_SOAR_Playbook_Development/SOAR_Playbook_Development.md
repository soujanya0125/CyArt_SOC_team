# SOAR Playbook Development: Automated Incident Response

**Disclaimer:** This document is designed to guide you through your SOAR Playbook simulation task.

## 1. Playbook Creation (Splunk Phantom)
**Objective:** Create a playbook to auto-block IP addresses triggered by phishing alerts.

**Playbook Logic Flow (Phishing Investigation & Containment):**
1. **Trigger (Ingest Alert):** Wazuh detects a malicious URL click or suspicious email source and sends the alert to Splunk Phantom.
2. **Action 1 (Extract IOCs):** Phantom parses the alert to extract the source IP address (e.g., `10.98.90.102`).
3. **Action 2 (IP Reputation Check):** Phantom queries an integrated Threat Intelligence platform (e.g., VirusTotal or AbuseIPDB) to check if the IP is known for malicious activity.
4. **Decision (Condition Check):** 
   - *If IP Reputation == Malicious (Score > 0):* Proceed to Containment.
   - *If IP Reputation == Safe:* Close ticket as False Positive.
5. **Action 3 (Containment):** Phantom executes an API call to **CrowdSec** (`cscli decisions add --ip 10.98.90.102 --type ban`) to block the IP.
6. **Action 4 (Escalation):** Phantom automatically generates a case ticket in **TheHive**, populating it with the IP details, VirusTotal score, and containment confirmation. 

---

## 2. Playbook Test Documentation
**Objective:** Simulate a phishing alert in Wazuh and verify that the playbook executed successfully.

**Simulation Details:**
- **Simulated Attack:** Triggered a Wazuh alert using a test script imitating a phishing payload connection to `10.98.90.102`.
- **Execution Log:**

| Playbook Step          | Status  | Notes                                                                 |
|------------------------|---------|-----------------------------------------------------------------------|
| 1. Ingest Alert        | Success | Wazuh alert received by Phantom successfully.                         |
| 2. Check IP Reputation | Success | IP `10.98.90.102` flagged as malicious (VT Score: 15/90).             |
| 3. Block IP            | Success | CrowdSec successfully banned `10.98.90.102` across all network nodes. |
| 4. Create Ticket       | Success | Case #105 generated in TheHive with full incident context.            |

*Note: In your actual report, take a screenshot of your Splunk Phantom visual playbook editor and the resulting TheHive ticket.*

---

## 3. Playbook Summary
**Objective:** Write a 50-word playbook summary for your final report (Google Docs).

**Summary (Copy to Google Docs):**
> This automated incident response playbook streamlines phishing remediation by instantly ingesting Wazuh alerts, extracting suspicious IP addresses, and querying external threat intelligence. Upon verifying malicious intent, the playbook actively orchestrates containment by banning the IP via CrowdSec and simultaneously raises an enriched escalation ticket in TheHive for final analyst review.
