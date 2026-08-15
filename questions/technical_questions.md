# SOC Analyst Technical Interview Questions


## Category 1, Networking Fundamentals

**Q1: What is the OSI model and why does it matter to a SOC analyst?**

```
Seven layers: Physical, Data Link, Network, Transport,
Session, Presentation, Application.

Why it matters:
- Attacks land at specific layers, and the layer narrows
  the vector before you have any other evidence
- Port scans hit Layer 4, Transport
- ARP spoofing hits Layer 2, Data Link
- Phishing hits Layer 7, Application
- Layer knowledge is what makes a Wireshark filter or a
  SIEM search targeted instead of exploratory
```

Do not just recite the layers. Tie one to real work: a MAC address is Layer 2, which is why it identifies a device even after the IP changes. That is the answer that sounds like you have used it.

---

**Q2: What is the difference between TCP and UDP?**

```
TCP:
- Connection oriented, handshake before data
- Reliable, guarantees delivery and order
- Slower, acknowledgement overhead
- HTTP, HTTPS, SSH, FTP, SMTP

UDP:
- Connectionless, no handshake
- No delivery guarantee
- Faster, no overhead
- DNS, DHCP, VoIP, streaming

SOC relevance:
Unusual UDP volume can mean DNS tunnelling or exfiltration.
UDP is attractive to attackers precisely because it is
noisy, expected, and rarely inspected.
A SYN flood is the TCP side of the same idea.
```

---

**Q3: Explain the TCP three way handshake.**

```
SYN      Client to server, "I want to connect"
SYN-ACK  Server to client, "Acknowledged, ready"
ACK      Client to server, "Established"
```

The SOC answer is what happens when it does not complete.

A SYN flood sends thousands of SYNs and never sends ACK, holding server resources open. In Wireshark that is a wall of SYN with no matching SYN-ACK.

A SYN scan looks similar and is not the same thing. The scanner wants the SYN-ACK, because the SYN-ACK is the answer. It just never completes the session. Volume distinguishes a flood from a scan, and intent distinguishes both from a busy server.

Say that out loud. Most candidates stop at the three arrows.

---

**Q4: What ports should every SOC analyst know?**

```
20/21  FTP, unencrypted, credentials in cleartext
22     SSH, brute force target
23     Telnet, unencrypted, should not exist
25     SMTP, mail transfer
53     DNS, tunnelling and exfiltration path
80     HTTP, unencrypted web
110    POP3, mail retrieval
143    IMAP, mail retrieval
443    HTTPS, and where attackers hide plain HTTP
445    SMB, WannaCry's path
3306   MySQL, should never face the internet
3389   RDP, the most common ransomware ingress
8080   HTTP alternate, proxies
```

The port list is table stakes. The follow up is what they are testing: why does port 443 carrying plain HTTP matter? Because everyone assumes 443 is TLS and stops inspecting. The port is not the protocol.

---

## Category 2, Security Fundamentals

**Q5: What is the CIA triad?**

```
Confidentiality: only authorised users can access the data
  Violated by: a breach exposing customer records

Integrity: the data is accurate and untampered
  Violated by: an attacker modifying financial records

Availability: systems are reachable when needed
  Violated by: a DDoS taking a service down

SOC relevance: every incident violates at least one.
Naming which one drives the priority. An integrity
violation on financial data outranks an availability
hit on a marketing page.
```

---

**Q6: Authentication versus authorisation?**

```
Authentication: proving who you are
  Username and password, MFA

Authorisation: what you may do once proven
  Your account reads files but cannot delete them

SOC relevance:
Credential theft defeats authentication.
Privilege escalation defeats authorisation.
An attacker usually needs both, and the gap between
them is where detection lives.
```

---

**Q7: What is defence in depth?**

```
Layered controls, so one failure does not become a breach.

Perimeter    Firewall, IPS
Network      Segmentation, VLANs
Endpoint     EDR, antivirus
Application  WAF, input validation
Data         Encryption, DLP
Human        Awareness training

SOC relevance: when an attack succeeds, the question is
not only what caught it. It is which layers should have
and did not.
```

Worth adding from real work: a control only works where it sits in the path. A firewall rule that does not filter the interface the service binds to is a correct rule with no effect. Layers only help if they are actually in the traffic path.

---

## Category 3, SOC Operations

**Q8: SIEM versus EDR?**

```
SIEM:
- Aggregates logs across the whole environment
- Correlates events into patterns
- Splunk, Microsoft Sentinel, IBM QRadar
- Answers: is this happening anywhere else?

EDR:
- Watches individual endpoints in real time
- Can isolate a host, kill a process, pull forensics
- CrowdStrike, Carbon Black, Microsoft Defender
- Answers: what exactly happened on this machine?

Used together. SIEM finds the campaign, EDR works
the host.
```

Honesty rule: if you have not used CrowdStrike, QRadar, or Defender, say so when asked. "I have worked Splunk and Sentinel, the concepts transfer" is a strong answer. Implying hands on with a tool you have read about is the one lie an interviewer can catch in a single follow up question.

---

**Q9: What is a playbook and why use them?**

```
A documented step by step procedure for a specific
incident type.

Why:
- Consistency, every analyst runs the same process
- Speed, no time spent deciding what comes next
- Compliance, proves due process
- Training, new analysts learn the shape of the job

Common types:
Phishing, ransomware, brute force, data exfiltration
```

The real answer: playbooks exist because analysts under pressure skip steps. They contain and never preserve evidence. They reset the password and leave the session alive. The playbook removes improvisation from the worst hour of someone's week.

---

**Q10: Walk me through the incident response lifecycle.**

```
Preparation    Tools ready, playbooks written, team trained
Detection      Alert fires, is it real or false positive?
Triage         Assess severity and scope, validate
Containment    Stop the spread, isolate, block, disable
Investigation  Understand what the attacker did, capture IOCs
Eradication    Remove the threat and every persistence artefact
Recovery       Restore with validation
Lessons Learned Improve detection, share IOCs
```

The order is the answer. Containment before investigation limits damage. Investigation before eradication means you remove all of it rather than the part you saw. Recovery before either means you do the incident twice.

Framework note: this maps to NIST SP 800-61, which is the one to name in an interview.

---

## Category 4, Tools

**Q11: What is Splunk and how do analysts use it?**

```
A SIEM that ingests, indexes, and searches machine
generated log data.

Analysts:
- Write SPL to hunt for suspicious activity
- Build dashboards for operational visibility
- Create alert rules on attack patterns
- Search historical data during investigations

Brute force detection:

index=main "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count as failed_attempts by src_ip
| where failed_attempts > 10
```

Explain the query, do not just show it. The rex extracts the IP from raw log text. Stats turns thousands of events into a ranked list. The where clause is the threshold that separates a typo from an attack.

Then say the next part, because it is what they want: the failures are not the finding. The finding is whether a success followed from the same IP.

---

**Q12: How would you use Wireshark to investigate suspicious traffic?**

```
1. Load the PCAP, read the protocol mix before filtering
2. Filter to the suspicious host
   ip.addr == 45.131.214.85
3. Check the direction. Outbound from an internal host
   means resident malware, not a probe
4. Look for beaconing, regular intervals mean automation
5. Follow TCP stream to read the conversation
6. Statistics, Conversations for everything else the
   host touched
7. DHCP filter, Option 12 gives you the hostname and MAC
8. DNS filter for every domain it tried, including
   the failures
9. Document as IOCs
```

Step 7 is the one worth naming. An IP is a lease and nobody can walk to an IP address. The hostname and MAC are the machine, and that is what turns an alert into a person the IT team can find.

---

## Category 5, Threat Intelligence

**Q13: What is MITRE ATT&CK and how do you use it?**

```
A knowledge base of adversary tactics, techniques, and
procedures built from real world observation.

Structure:
Tactic        The goal, e.g. Initial Access
Technique     How, e.g. T1566 Phishing
Sub technique The specific method, e.g. T1566.002
              Spearphishing Link

Analysts:
- Map observed activity to known behaviour
- Find detection gaps using Navigator
- Build rules against specific techniques
- Communicate in a shared vocabulary
- Anticipate the next stage of a chain
```

The gap analysis use is the one that shows depth. If a technique has a playbook but no rule behind it, the gap is visible on paper instead of discovered during an incident.

Do not overclaim on mapping. Techniques you hunted for are not techniques you observed, and an interviewer who knows the framework will hear the difference.

---

**Q14: What is threat intelligence and why does it matter?**

```
Information about current and emerging threats. Who,
what they want, how they operate.

Strategic    Industry level trends, for leadership
Operational  Campaign level TTPs
Tactical     IOCs, IPs, domains, hashes

Analysts:
- Enrich alerts with external context
- Block known IOCs before they are used
- Hunt for known TTPs in the environment
- Prioritise patching by what is actually exploited

Sources: VirusTotal, AbuseIPDB, AlienVault OTX,
MITRE ATT&CK, CISA alerts, vendor reports
```

Reading intel critically is the part that matters. On AbuseIPDB, 6,000 reports from 600 distinct sources is consensus. 6,000 reports from one source is one opinion repeated. Volume is not agreement.

---

## Category 6, Malware and Attacks

**Q15: Virus, worm, trojan, RAT, ransomware?**

```
Virus       Attaches to a file, needs a human to spread
Worm        Self replicates across the network, no human
            needed, which is why it scales
Trojan      Disguised as legitimate software, opens a
            backdoor on execution
RAT         A trojan giving full remote control.
            Surveillance, theft, lateral movement.
            Example: NetSupport Manager RAT
Ransomware  Encrypts and demands payment. Often trojan
            delivery plus worm style spreading.
            Example: WannaCry, via SMB 445
```

The operational distinction: a worm means containment is a race against the clock. A virus means containment is a search.

---

**Q16: What is a man in the middle attack?**

```
An attacker sits between two parties, and neither knows.

1. Attacker positions between victim and server
2. Victim sends data believing it is the server
3. Attacker reads, modifies, or relays it

Methods:
ARP spoofing     Layer 2, local network
SSL stripping    Downgrade HTTPS to HTTP
Evil twin WiFi   Fake AP intercepts traffic

Detection: unexpected ARP entries, certificate warnings,
gateway MAC that changed

Prevention: this is why weak TLS ciphers matter. A
downgrade attack does not break encryption, it asks
politely for worse encryption and the server agrees.
```

---

## Quick Fire

```
Port 443?           HTTPS
Port 22?            SSH
Port 445?           SMB
Port 3389?          RDP
Zero day?           Vulnerability with no patch available
CVE?                Common Vulnerabilities and Exposures ID
CVSS?               Common Vulnerability Scoring System, 0 to 10
IOC?                Indicator of Compromise
TTP?                Tactics, Techniques, and Procedures
SIEM?               Security Information and Event Management
EDR?                Endpoint Detection and Response
SOC?                Security Operations Center
PCAP?               Packet capture file
SPL?                Search Processing Language
KQL?                Kusto Query Language, Sentinel
Lateral movement?   Attacker moving between hosts
Persistence?        Maintaining access across reboot
C2?                 Command and control
Beaconing?          Regular interval callbacks to C2
Dwell time?         Time between compromise and detection
