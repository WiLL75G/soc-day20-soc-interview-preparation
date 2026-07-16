# SOC Analyst Incident Response Scenarios

Read each alert as if it just fired. Write your answer before reading the model. Score honestly.

Every scenario here has one step candidates skip. It is named at the bottom of each model answer, because knowing what you would miss is worth more than knowing what you would do.

---

## Scenario 1, SSH Brute Force

**The alert:**

```
Time:            02:14
Rule:            SSH brute force detected
Source IP:       185.220.101.45
Destination:     10.0.0.15, Linux server, DMZ
Failed attempts: 47 in 90 seconds
Followed by:     1 successful login
Severity:        HIGH
```

**Walk me through it.**

### Model answer

```
1. Acknowledge in the ticketing system
   Log time received and initial observations

2. Enrich the source IP
   VirusTotal reputation
   AbuseIPDB abuse score and distinct reporter count
   whois for owner, netrange, country
   Result: known Tor exit node, 100 percent confidence

3. Confirm the successful login
   Check auth logs on 10.0.0.15
   Identify which account
   Establish what happened after the login

4. Contain
   Block the source at the firewall
   Isolate 10.0.0.15
   Disable the compromised account and revoke sessions

5. Escalate to Tier 2 with full triage notes
   Confirmed compromise, host needs forensics

6. Document
   Source, destination, timeline, every action taken

MITRE: T1110.001 brute force, T1078 valid accounts
```

**The step candidates skip:** step 3. Forty seven failures is an attack that failed. Forty seven failures and one success is an attacker with a shell. If the answer stops at "block the IP," the alert has been closed with the intruder still inside.

**Second miss:** revoking sessions. Disabling the account does not kill a session already open. The attacker keeps working while the ticket says resolved.

**Third:** block the netrange, not the IP. A Tor exit sits in a range of 32 addresses. Blocking one blocks one.

---

## Scenario 2, Phishing Email

**The alert:**

```
Time:        10:32
Rule:        Phishing email detected
From:        security@paypal.com
Reply-To:    attacker@secure-login-verify.com
To:          brad@company.com
Subject:     Urgent: Your account has been compromised
Sending IP:  45.131.214.85
Attachment:  invoice.pdf
Severity:    HIGH
```

**What do you do?**

### Model answer

```
1. Do not click, do not open the attachment

2. Read the headers
   From paypal.com, spoofed
   Reply-To secure-login-verify.com, the real attacker
   Sending IP 45.131.214.85, enrich it
   SPF and DKIM, present or absent

3. Investigate the IOCs
   VirusTotal on the IP and the domain
   MXToolbox for full header analysis
   URLScan for any links, do not visit them

4. Scope it
   Who else received this?
   Did anyone click?
   Check proxy logs for visits to the domain

5. Contain
   Quarantine across all mailboxes
   Block the sender domain at the gateway
   Block the sending IP

6. Notify
   The recipient first, then a targeted awareness note

7. Document, and escalate if anyone clicked

MITRE: T1566.002 spearphishing link,
       T1036.005 masquerading
```

**The step candidates skip:** step 4. Quarantining the email feels like resolution. It stops the next victim and does nothing for the twenty people who already got it, or the one who already typed their password in. Until you know who clicked, you do not know the size of the incident.

**Note on the Reply-To:** it is the strongest indicator in the header. The From can lie freely. The Reply-To has to be true, because the attacker needs the reply to reach them.

---

## Scenario 3, Ransomware

**The alert:**

```
Time:     03:47
Rule:     Mass file encryption detected
Host:     DESKTOP-HR-04, HR workstation
User:     sarah.johnson
Activity: 3,847 files renamed to .locked in 4 minutes
Network:  Outbound to 91.238.14.7:443
Severity: CRITICAL
```

**What do you do?**

### Model answer

```
1. Contain first. Do not investigate first.
   Every second is more files.

2. Isolate the host
   Use EDR network isolation, not the power button
   Preserve memory state

3. Cut the C2
   Block 91.238.14.7 at the firewall
   Check whether any other host is talking to it

4. Scope it
   Any other host showing the same file activity?
   SIEM for lateral movement from DESKTOP-HR-04
   SMB traffic specifically, that is how it spreads

5. Escalate immediately
   Tier 2 and IR
   Management
   Legal and compliance if PII is in scope

6. Preserve evidence
   Do not reboot
   Memory dump if possible
   Preserve all logs

7. Recovery planning
   Identify the last clean backup
   Do not restore until eradication is confirmed

MITRE: T1486 data encrypted for impact,
       T1041 exfiltration over C2 channel,
       T1021.002 SMB admin shares
```

**Why EDR isolation and not unplugging:** pulling the cable kills volatile memory, and the encryption key may be sitting in it. It also tips the attacker. EDR isolation cuts the network and keeps the machine alive for forensics.

**Why do not reboot:** same reason. Reboot destroys memory, and memory may be the only place the key exists.

**The inversion:** this scenario is the exception to every other one here. Normally you validate before you act. Ransomware moves faster than triage, so containment comes first and the investigation happens to an isolated host. A candidate who runs the standard order on this one has watched 3,847 files become 30,000 while they enriched an IP.

---

## Scenario 4, Suspicious PowerShell

**The alert:**

```
Time:           11:23
Rule:           Suspicious PowerShell execution
Host:           WORKSTATION-22
User:           mike.chen
Command:        powershell.exe -ExecutionPolicy Bypass
                -EncodedCommand SQBFAFgAIAAoAE4AZQB3...
Parent Process: WINWORD.EXE
Severity:       HIGH
```

**What do you do?**

### Model answer

```
1. Read the parent process first
   WINWORD.EXE spawning PowerShell is macro malware.
   Word does not do this. That single line is the alert.

2. Decode the base64
   CyberChef, or PowerShell script block logging,
   Event ID 4104, which logs the decoded command
   Find out what it actually does

3. Trace the document
   What did mike.chen open?
   Did it arrive by email?
   Who else received it?

4. Check EDR telemetry
   What did the PowerShell spawn?
   Did it download anything?
   Registry or file modifications?

5. Contain
   Isolate WORKSTATION-22 via EDR
   Disable mike.chen's account and revoke sessions
   Block any external IPs contacted

6. Escalate with the decoded command in the ticket

7. Check for lateral movement
   Were mike.chen's credentials used elsewhere?
   DC logs for unusual auth

MITRE: T1059.001 PowerShell,
       T1566.001 spearphishing attachment,
       T1204.002 malicious file
```

**The step candidates skip:** step 1. The base64 is the flashy part and everyone reaches for CyberChef. But `-EncodedCommand` alone is not conclusive, plenty of legitimate tooling uses it. WINWORD.EXE as the parent is what makes this malicious before anything is decoded. Read the process tree first.

**Worth naming:** `-ExecutionPolicy Bypass` is not a security control being defeated. Execution policy was never a security boundary and Microsoft says so. It is an indicator, not a breach.

---

## Scenario 5, Impossible Travel

**The alert:**

```
Time:     08:15
Rule:     Impossible travel detected
User:     james.wilson@company.com
Login 1:  07:45, London UK, 81.2.69.144
Login 2:  08:10, Lagos Nigeria, 105.112.0.1
Gap:      25 minutes
Distance: 5,000 miles
Severity: HIGH
```

**What do you do?**

### Model answer

```
1. Physics says this is not one person

2. Investigate both logins before assuming
   Are either IP known for this user?
   Could a VPN or corporate proxy explain it?
   Any travel on record with HR?

3. Contact the user out of band
   Call them. Do not email.
   If the account is compromised the attacker
   reads the email.
   Ask where they physically are.

4. Contain if confirmed
   Reset the password
   Revoke all active sessions
   Disable the account if needed
   Block the suspicious IP

5. Investigate the session
   What did the Lagos session do?
   What was accessed, downloaded, or read?

6. Escalate and document with the full timeline

MITRE: T1078 valid accounts
```

**The step candidates skip:** step 2. Impossible travel is the noisiest rule in most SIEMs. A VPN, a mobile carrier CGNAT, a cloud sync client, a badly geolocated IP block — all produce this alert on a completely innocent user. Jumping to containment locks out a real employee and teaches the business that the SOC cries wolf.

**But do not swing the other way.** Investigate fast, do not investigate slowly. If it is real, the attacker has a live session right now.

**Why call and not email:** if the account is compromised, the attacker is in the mailbox. An email asking "are you in Lagos?" tells the attacker they have been spotted.

---

## Scoring

```
5  All steps, correct order, named the skipped step
4  Most steps, minor gaps
3  Key steps, missed containment or escalation
2  Some correct ideas, significant gaps
1  Needs work

Target: 4 or above on all five before interviewing.
```

Score the order, not just the content. Listing every step in the wrong sequence is the same failure as missing one.
