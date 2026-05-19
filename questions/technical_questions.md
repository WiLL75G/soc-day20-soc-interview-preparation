# SOC Analyst Technical Interview Questions

## Analyst: William | @WilliamCyberSec | GitHub: WilL75G

---

## Category 1 --Networking Fundamentals

**Q1: What is the OSI model and why does it matter to a SOC anyst?**
```
The OSI model has 7 layers Physical, Data Link, Network,
Transport, Session, Presentation, Application.

Why it matters to SOC:
- Attacks happen at specific layers knowing which layer
  helps narrow down the attack vector
- Port scans target Layer 4 (Transport)
- Phishing targets Layer 7 (Application)
- ARP spoofing targets Layer 2 (Data Link)
- A SOC analyst uses layer knowledge to filter SIEM alerts
  and Wireshark captures more efficiently
```

---

**Q2: What is the difference between TCP and UDP?**
```
TCP (Transmission Control Protocol):
- Connection-oriented requires handshake before data transfer
- Reliable guarantees delivery and order
- Slower overhead from acknowledgements
- Used for: HTTP, HTTPS, SSH, FTP, SMTP

UDP (User Datagram Protocol):
- Connectionless — no handshake required
- Unreliable — no guaranteed delivery
- Faster — no overhead
- Used for: DNS, DHCP, VoIP, streaming, gaming

SOC relevance: Unusual UDP traffic can indicate DNS
tunnelling or data exfiltration. Unexpected TCP SYN floods
indicate a DoS attack.
```

---

**Q3: Explain the TCP 3-way handshake.**
```
Step 1 — SYN: Client sends SYN to server
         "I want to connect"

Step 2 — SYN-ACK: Server responds with SYN-ACK
         "Acknowledged — I'm ready"

Step 3 — ACK: Client sends ACK
         "Connection established"

SOC relevance: A SYN flood attack sends thousands of SYN
packets without completing the handshake consuming server
resources. This is detected in Wireshark by a high volume
of SYN packets with no corresponding SYN-ACK responses.
```

---

**Q4: What ports should every SOC analyst know?**
```
20/21  — FTP (file transfer unencrypted)
22     — SSH (secure remote access)
23     — Telnet (remote access unencrypted — dangerous)
25     — SMTP (email sending)
53     — DNS (domain name resolution)
80     — HTTP (web traffic unencrypted)
110    — POP3 (email retrieval)
143    — IMAP (email retrieval)
443    — HTTPS (encrypted web traffic)
445    — SMB (Windows file sharing WannaCry used this)
3306   — MySQL (database)
3389   — RDP (Windows remote desktop brute force target)
8080   — HTTP alternate (often used by web proxies)
```

---

## Category 2 — Security Fundamentals

**Q5: What is the CIA Triad?**
```
Confidentiality: Data is only accessible to authorised users
Example violation: Data breach exposing customer records

Integrity: Data is accurate and has not been tampered with
Example violation: Attacker modifies financial records

Availability: Systems and data are accessible when needed
Example violation: DDoS attack taking down a web service

SOC relevance: Every security incident violates one or more
of these principles. Classifying which principle is violated
helps prioritise the response.
```

---

**Q6: What is the difference between authentication and authorisation?**
```
Authentication: Proving who you are
Example: Entering your username and password

Authorisation: What you are allowed to do after proving identity
Example: Your account can read files but not delete them

SOC relevance: Many attacks exploit both credential theft
bypasses authentication, privilege escalation bypasses
authorisation.
```

---

**Q7: What is defence in depth?**
```
Defence in depth is a layered security approach where multiple
controls exist at each layer so that if one fails another
catches the attack.

Example layers:
- Perimeter: Firewall, IPS
- Network: Network segmentation, VLANs
- Endpoint: EDR, antivirus
- Application: WAF, input validation
- Data: Encryption, DLP
- Human: Security awareness training

SOC relevance: Understanding which layer an attack targets
tells the analyst which controls should have caught it
and which ones failed.
```

---

## Category 3 — SOC Operations

**Q8: What is the difference between a SIEM and an EDR?**
```
SIEM (Security Information and Event Management):
- Aggregates logs from across the entire environment
- Correlates events to detect patterns
- Examples: Splunk, Microsoft Sentinel, IBM QRadar
- Best for: Detecting attack campaigns across multiple systems

EDR (Endpoint Detection and Response):
- Monitors individual endpoints in real time
- Can isolate hosts, kill processes, collect forensic data
- Examples: CrowdStrike, Carbon Black, Microsoft Defender
- Best for: Detecting and responding to threats on endpoints

A SOC uses both together SIEM for the big picture,
EDR for granular endpoint investigation.
```

---

**Q9: What is a playbook and why do SOC analysts use them?**
```
A playbook is a documented, step-by-step procedure for
responding to a specific type of incident.

Why analysts use them:
- Consistency every analyst follows the same process
- Speed no time wasted deciding what to do next
- Compliance proves due process was followed
- Training new analysts learn from playbooks

Common playbook types:
- Phishing response playbook
- Ransomware response playbook
- Brute force response playbook
- Data exfiltration playbook
```

---

**Q10: Walk me through the incident response lifecycle.**
```
Phase 1 — Preparation:
Tools ready, playbooks written, team trained

Phase 2 — Identification:
Alert fires — is this a real incident or false positive?

Phase 3 — Containment:
Stop the spread isolate host, block IP, disable account

Phase 4 — Eradication:
Remove the threat delete malware, patch vulnerability

Phase 5 — Recovery:
Restore systems bring services back online safely

Phase 6 — Lessons Learned:
Document what happened improve detection and response

Memory aid: PICERL
Prepare → Identify → Contain → Eradicate → Recover → Learn
```

---

## Category 4 — Tools

**Q11: What is Splunk and how do SOC analysts use it?**
```
Splunk is a SIEM platform that ingests, indexes, and
searches machine generated log data from across an
environment.

How SOC analysts use it:
- Write SPL (Search Processing Language) queries to hunt
  for suspicious activity
- Build dashboards to visualise security metrics
- Create alert rules that fire when attack patterns are detected
- Investigate incidents by searching historical log data

Example SPL query detect brute force:
index=auth sourcetype=linux_secure
"Failed password" | stats count by src_ip
| where count > 10
```

---

**Q12: How would you use Wireshark to investigate suspicious traffic?**
```
Step 1: Load the PCAP file
Step 2: Apply a display filter to isolate suspicious IP
        Example: ip.addr == 185.220.101.45
Step 3: Look for unusual protocols or ports
Step 4: Follow TCP stream to read the full conversation
Step 5: Check Statistics → Conversations for data volumes
Step 6: Use DHCP filter to identify the infected host
Step 7: Check DNS queries for suspicious domains
Step 8: Document all findings as IOCs
```

---

## Category 5 — Threat Intelligence

**Q13: What is MITRE ATT&CK and how do you use it?**
```
MITRE ATT&CK is a knowledge base of adversary tactics,
techniques, and procedures (TTPs) based on real-world
observations.

Structure:
- Tactics: The attacker's goal (e.g. Initial Access)
- Techniques: How they achieve it (e.g. T1566 Phishing)
- Sub-techniques: Specific method (e.g. T1566.001 Spearphishing)

How SOC analysts use it:
- Map detected activity to known attacker behaviour
- Identify detection gaps using the Navigator
- Build detection rules targeting specific techniques
- Communicate findings to stakeholders in a standardised way
- Understand what comes next in an attack chain
```

---

**Q14: What is threat intelligence and why does it matter?**
```
Threat intelligence is information about current and emerging
threats who the attackers are, what they want, and how
they operate.

Types:
- Strategic: High level industry threats and trends
- Operational: Campaign level attacker TTPs
- Tactical: Technical level IOCs (IPs, domains, hashes)

How SOC analysts use it:
- Enrich alerts with context from threat intel feeds
- Block known malicious IOCs proactively
- Hunt for known attacker TTPs in the environment
- Prioritise patching based on exploited vulnerabilities

Sources: VirusTotal, AbuseIPDB, AlienVault OTX,
         MITRE ATT&CK, CISA alerts, vendor threat reports
```

---

## Category 6 — Malware & Attacks

**Q15: What is the difference between a virus, worm, trojan, and RAT?**
```
Virus: Attaches to a file spreads when file is shared
       Requires human action to propagate

Worm: Self-replicates across networks automatically
      No human action needed spreads faster than viruses

Trojan: Disguised as legitimate software
        Opens a backdoor when executed
        Named after the Trojan Horse

RAT (Remote Access Trojan): A trojan that gives the attacker
        full remote control of the infected machine
        Used for surveillance, data theft, lateral movement
        Example: NetSupport Manager RAT (Day 18 PCAP)

Ransomware: Encrypts files and demands payment for decryption
        Combines trojan delivery with worm like spreading
        Example: WannaCry (used SMB port 445)
```

---

**Q16: What is a man-in-the-middle attack?**
```
A man-in-the-middle (MITM) attack occurs when an attacker
intercepts communication between two parties without
either party knowing.

How it works:
1. Attacker positions themselves between victim and server
2. Victim sends data to attacker thinking it's the server
3. Attacker reads, modifies, or relays the data

Common methods:
- ARP spoofing — Layer 2 MITM on local network
- SSL stripping — downgrade HTTPS to HTTP
- Evil twin WiFi — fake access point intercepts traffic

Detection: Unexpected ARP entries, certificate warnings,
           unusual gateway MAC addresses
```

---

## Quick Fire Round — Know These Cold

```
Q: What is port 443?          A: HTTPS
Q: What is port 22?           A: SSH
Q: What is port 3389?         A: RDP
Q: What is a zero-day?        A: Unknown vulnerability with no patch
Q: What is a CVE?             A: Common Vulnerabilities and Exposures ID
Q: What is CVSS?              A: Common Vulnerability Scoring System (0-10)
Q: What is IOC?               A: Indicator of Compromise
Q: What is TTP?               A: Tactics, Techniques, and Procedures
Q: What does SIEM stand for?  A: Security Information and Event Management
Q: What does EDR stand for?   A: Endpoint Detection and Response
Q: What does SOC stand for?   A: Security Operations Center
Q: What is a PCAP?            A: Packet Capture file
Q: What is SPL?               A: Splunk Processing Language
Q: What is lateral movement?  A: Attacker moving through the network
Q: What is persistence?       A: Attacker maintaining access after reboot
```
