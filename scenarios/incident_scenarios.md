# SOC Analyst Incident Response Scenarios

## How to Use This Guide

Read each scenario as if it is a real alert.
Write your response before reading the answer.
Score yourself honestly.
These are the exact scenarios interviewers use.

---

## Scenario 1 — SSH Brute Force

**The Alert:**
```
Time: 02:14 AM
Rule: SSH Brute Force Detected
Source IP: 185.220.101.45
Destination: 10.0.0.15 (Linux server — DMZ)
Failed attempts: 47 in 90 seconds
Followed by: 1 successful login
Severity: HIGH
```

**What do you do? Walk me through step by step.**

---

### Model Answer:

```
Step 1 — Acknowledge the alert in the ticketing system
         Log time received and initial observations

Step 2 — Investigate the source IP
         → VirusTotal: check reputation
         → AbuseIPDB: check abuse score
         → Whois: check owner and country
         Result: 185.220.101.45 = known Tor exit node,
         100% abuse confidence

Step 3 — Confirm the successful login
         → Check auth logs on 10.0.0.15
         → Identify which account was compromised
         → Check what the attacker did after login

Step 4 — Contain immediately
         → Block 185.220.101.45 at the firewall
         → Isolate 10.0.0.15 from the network
         → Disable the compromised user account

Step 5 — Escalate to Tier 2
         → Full triage notes included
         → Confirmed compromise host needs forensics

Step 6 — Document everything
         → Source IP, destination, timeline, actions taken

MITRE: T1110.001 Brute Force, T1078 Valid Accounts
```

---

## Scenario 2 — Phishing Email

**The Alert:**
```
Time: 10:32 AM
Rule: Phishing Email Detected
From: security@paypal.com
Reply-To: attacker@secure-login-verify.com
To: brad@company.com
Subject: Urgent: Your account has been compromised
Sending IP: 45.131.214.85
Attachment: invoice.pdf
Severity: HIGH
```

**What do you do?**

---

### Model Answer:

```
Step 1 — Do not click any links or open the attachment

Step 2 — Analyse the email headers
         → From: paypal.com spoofed
         → Reply-To: secure-login-verify.com real attacker
         → Sending IP: 45.131.214.85 check reputation
         → SPF/DKIM: check if present

Step 3 — Investigate the IOCs
         → VirusTotal: check sending IP and domain
         → MXToolbox: analyse full headers
         → URLScan.io: check any links in the email

Step 4 — Check scope
         → Did other users receive this email?
         → Did anyone click the link or open the attachment?
         → Check proxy logs for any visits to the malicious domain

Step 5 — Contain
         → Quarantine the email at the gateway
         → Block the sending domain
         → Block the sending IP

Step 6 — Notify and educate
         → Alert the recipient do not click anything
         → Send company-wide awareness reminder

Step 7 — Document and escalate if user clicked

MITRE: T1566.001 Spearphishing, T1036.005 Masquerading
```

---

## Scenario 3 — Ransomware Alert

**The Alert:**
```
Time: 03:47 AM
Rule: Mass File Encryption Detected
Host: DESKTOP-HR-04 (HR department workstation)
User: sarah.johnson
Activity: 3,847 files renamed to .locked extension
          in 4 minutes
Network: Outbound connection to 91.238.14.7:443
Severity: CRITICAL
```

**What do you do?**

---

### Model Answer:

```
Step 1 — This is CRITICAL act immediately
         Do not wait. Do not investigate first. CONTAIN FIRST.

Step 2 — Isolate the host immediately
         → Disconnect DESKTOP-HR-04 from the network
         → Use EDR to isolate do not physically unplug yet
         → Preserve memory state for forensics

Step 3 — Block C2 communication
         → Block 91.238.14.7 at the firewall immediately
         → Check if other hosts are connecting to same IP

Step 4 — Assess the scope
         → Are other hosts showing similar file activity?
         → Check SIEM for lateral movement from DESKTOP-HR-04
         → Check SMB traffic ransomware spreads via SMB

Step 5 — Escalate immediately
         → Tier 2 + Incident Response team
         → Management notification
         → Legal and compliance if PII may be affected

Step 6 — Preserve evidence
         → Do not reboot the machine
         → Capture memory dump if possible
         → Preserve all logs

Step 7 — Recovery planning
         → Identify last clean backup of affected files
         → Do not restore until malware is fully eradicated

MITRE: T1486 Data Encrypted for Impact,
       T1041 Exfiltration Over C2 Channel,
       T1021.002 SMB/Windows Admin Shares
```

---

## Scenario 4 — Suspicious PowerShell

**The Alert:**
```
Time: 11:23 AM
Rule: Suspicious PowerShell Execution
Host: WORKSTATION-22
User: mike.chen
Command: powershell.exe -ExecutionPolicy Bypass
         -EncodedCommand
         SQBFAFgAIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQA...
Parent Process: WINWORD.EXE
Severity: HIGH
```

**What do you do?**

---

### Model Answer:

```
Step 1 — This is a strong malware indicator
         PowerShell launched by Word = macro-based malware

Step 2 — Decode the base64 command
         → Use CyberChef to decode the EncodedCommand
         → Identify what the PowerShell is actually doing

Step 3 — Investigate the parent process
         → WINWORD.EXE spawning PowerShell = malicious macro
         → Check what document mike.chen opened recently
         → Was the document received by email?

Step 4 — Check EDR telemetry
         → What processes did the PowerShell spawn?
         → Did it download anything from the internet?
         → Did it modify any files or registry keys?

Step 5 — Contain
         → Isolate WORKSTATION-22 via EDR
         → Disable mike.chen's account temporarily
         → Block any external IPs contacted

Step 6 — Escalate to Tier 2
         → Full PowerShell command decoded and included
         → EDR telemetry included in ticket

Step 7 — Check for lateral movement
         → Did mike.chen's credentials get used elsewhere?
         → Check DC logs for unusual auth events

MITRE: T1059.001 PowerShell,
       T1566.001 Spearphishing Attachment,
       T1204.002 Malicious File
```

---

## Scenario 5 — Impossible Travel

**The Alert:**
```
Time: 08:15 AM
Rule: Impossible Travel Detected
User: james.wilson@company.com
Login 1: 07:45 AM from London, UK — IP 81.2.69.144
Login 2: 08:10 AM from Lagos, Nigeria — IP 105.112.0.1
Time between logins: 25 minutes
Distance: 5,000 miles
Severity: HIGH
```

**What do you do?**

---

### Model Answer:

```
Step 1 — This indicates credential compromise
         No human can travel 5,000 miles in 25 minutes

Step 2 — Investigate both logins
         → Check if both IPs are known to james.wilson
         → Check if VPN or proxy could explain the location
         → Check the user's travel schedule with HR

Step 3 — Contact the user immediately
         → Call james.wilson directly — not email
         → Ask if they are in Lagos or London right now
         → If London: Lagos login is attacker
         → If neither: both logins may be attacker

Step 4 — Contain if compromise confirmed
         → Reset james.wilson's password immediately
         → Revoke all active sessions
         → Disable account temporarily if needed
         → Block the suspicious IP

Step 5 — Investigate what the attacker accessed
         → What did the Lagos login session do?
         → What data or systems were accessed?
         → Were any files downloaded or emails read?

Step 6 — Escalate and document
         → Full timeline included in ticket
         → Both IPs investigated and documented

MITRE: T1078 Valid Accounts,
       T1133 External Remote Services
```

---

## Scenario Scoring Guide

After each scenario score yourself:

```
5/5 — All steps covered in correct order
4/5 — Most steps covered — minor gaps
3/5 — Key steps covered — missed containment or escalation
2/5 — Some correct ideas — significant gaps
1/5 — Needs significant improvement

Target score: 4/5 or above on all 5 scenarios
before your interview
```
