# SOC Analyst Technical Interview Questions

## Category 1, Networking Fundamentals

**Q1: What is the OSI model and why does it matter to a SOC analyst?**

```text
Seven layers: Physical, Data Link, Network, Transport,
Session, Presentation, Application.

Why it matters:
- Different security activity appears at different layers
- A TCP SYN scan is primarily visible through Layer 4 behaviour
- ARP spoofing operates at Layer 2
- Phishing is primarily an application layer activity
- Layer knowledge helps make Wireshark filters and SIEM
  searches more targeted
```

Do not just recite the layers.

Tie the model to investigation evidence.

A MAC address operates at Layer 2 while an IP address operates at Layer 3.

DHCP evidence can correlate a leased IP with a hostname and MAC address during a particular period. Those identifiers provide additional investigative leads, but they should not automatically be treated as permanent device or user identity.

---

**Q2: What is the difference between TCP and UDP?**

```text
TCP:
- Connection oriented
- Uses a handshake before normal data transfer
- Provides reliable ordered delivery
- Has acknowledgement and connection management overhead
- Used by protocols such as HTTP, HTTPS, SSH and SMTP

UDP:
- Connectionless
- No TCP style handshake
- Does not guarantee delivery or ordering
- Lower protocol overhead
- Used by protocols such as DNS and DHCP
```

SOC relevance:

Unusual TCP or UDP activity can become an investigation lead depending on destination, volume, timing, protocol, and surrounding evidence.

For example, DNS can be abused for tunnelling, but high DNS or UDP volume alone does not establish tunnelling.

---

**Q3: Explain the TCP three way handshake.**

```text
SYN      Client to server, requests a connection
SYN ACK  Server to client, acknowledges and responds
ACK      Client to server, completes the handshake
```

The SOC answer should go beyond memorizing the three packets.

A SYN flood generates a large volume of connection attempts that are not completed normally. A server may still send SYN ACK responses, but the expected final ACKs are absent or insufficient and half open connection state can consume resources.

A SYN scan also intentionally avoids completing normal connections to many ports.

The difference is established through the wider packet pattern, rate, destinations, responses, and investigation context rather than one SYN packet alone.

---

**Q4: What ports should every SOC analyst know?**

```text
20/21  FTP
22     SSH
23     Telnet
25     SMTP
53     DNS
80     HTTP
110    POP3
143    IMAP
443    HTTPS
445    SMB
3306   MySQL
3389   RDP
8080   Common HTTP alternate
```

The port number is a starting point, not proof of the application protocol.

Port 443 commonly carries HTTPS, but seeing TCP destination port 443 does not by itself prove the payload is TLS.

The same principle applies to service exposure. MySQL on 3306 should never face the internet directly. There is essentially no legitimate reason for a raw database port to be reachable from outside the network, application layers exist to mediate that access.

---

## Category 2, Security Fundamentals

**Q5: What is the CIA triad?**

```text
Confidentiality
Only authorised users can access protected information.

Example violation:
Sensitive customer records are exposed.

Integrity
Information remains accurate and is not modified
without authorisation.

Example violation:
An attacker changes financial records.

Availability
Systems and information remain accessible when needed.

Example violation:
A DDoS attack makes a service unavailable.
```

The CIA triad provides a useful way to describe the security impact of an incident.

The affected property can also help explain business impact and response priority.

---

**Q6: Authentication versus authorisation?**

```text
Authentication:
Proving an identity.

Examples:
Password
MFA
Certificate

Authorisation:
Determining what an authenticated identity is permitted
to access or perform.
```

A compromised credential can defeat an authentication control.

Privilege escalation can allow an attacker to obtain permissions beyond those originally granted.

The distinction matters because successful authentication does not automatically mean the resulting activity was authorised.

---

**Q7: What is defence in depth?**

```text
Multiple security controls placed across different layers
so one control failure does not automatically expose the
entire environment.

Examples:

Perimeter     Firewall, IPS
Network       Segmentation
Endpoint      EDR, antivirus
Application   WAF, input validation
Data          Encryption, DLP
Human         Awareness and verification procedures
```

A useful operational lesson is that a control only protects the traffic or activity it actually covers.

A configured firewall rule, for example, should be validated against actual network exposure rather than assuming configuration equals enforcement.

---

## Category 3, SOC Operations

**Q8: SIEM versus EDR?**

```text
SIEM:
- Aggregates telemetry from multiple systems
- Searches and correlates events
- Supports detections, dashboards and investigations
- Examples include Splunk and Microsoft Sentinel

EDR:
- Collects endpoint focused telemetry
- Supports process and host investigation
- Depending on the product and permissions, can support
  response actions such as host isolation
- Examples include Microsoft Defender for Endpoint,
  CrowdStrike Falcon and Carbon Black
```

They can complement each other.

The SIEM can expose activity across multiple systems while EDR can provide deeper endpoint context.

Honesty rule:

If you have not used a product directly, say so.

Transferable concepts are useful. Invented hands on experience is not.

---

**Q9: What is a playbook and why use one?**

```text
A documented procedure for responding to a particular
security situation.

Benefits:
- Consistency
- Repeatability
- Faster decision making
- Documentation
- Training
- Reduced chance of skipped steps
```

Playbooks are particularly useful when analysts are working under pressure.

They do not remove analyst judgment.

They provide a repeatable starting structure while allowing the response to adapt to the evidence.

---

**Q10: Walk me through incident response.**

A practical interview sequence is:

```text
Preparation
Detection
Triage
Containment
Investigation
Eradication
Recovery
Lessons learned
```

This is a practical interview workflow, not the official current NIST lifecycle.

NIST SP 800-61 Rev. 3, published in April 2025, integrates incident response recommendations with the NIST Cybersecurity Framework 2.0, mapping to all six CSF functions, Govern, Identify, Protect, Detect, Respond and Recover, rather than the four phase model Rev. 2 used.

The important interview skill is explaining why the response sequence may change with the incident.

For example, rapidly spreading ransomware may require immediate containment while another alert may require additional validation before disruptive action.

Investigation should inform eradication, and recovery should include validation that the threat has actually been addressed.

---

## Category 4, Tools

**Q11: What is Splunk and how do analysts use it?**

```text
Splunk can ingest, index and search machine generated
data.

SOC analysts can use it to:

- Search telemetry
- Investigate alerts
- Correlate events
- Build dashboards
- Create detections
- Review historical activity
```

Example SSH failure query:

```spl
index=main "Failed password"
| rex "from (?<src_ip>\d+\.\d+\.\d+\.\d+)"
| stats count as failed_attempts by src_ip
| where failed_attempts > 10
```

Explain the query.

`rex` extracts the source IP from the raw event.

`stats` aggregates failures by source.

`where` applies the chosen threshold.

The threshold creates a detection lead. It does not establish compromise.

The next investigation question is whether the same source was associated with a successful authentication and what happened afterward.

---

**Q12: How would you use Wireshark to investigate suspicious traffic?**

```text
1. Load the PCAP and review the protocol mix

2. Filter on the suspicious host or destination

   ip.addr == 45.131.214.85

3. Establish connection direction

4. Examine timing and repeated patterns

5. Follow relevant TCP streams where appropriate

6. Review Statistics and Conversations

7. Correlate DHCP information where available

8. Review relevant DNS activity

9. Document indicators and unresolved questions
```

Outbound connection initiation can rule out some inbound activity, but it does not by itself establish resident malware.

Regular timing can be consistent with automated beaconing, but legitimate software can also communicate periodically.

DHCP information can correlate an IP address with a hostname and MAC address during the capture period.

Those are useful investigative identifiers, not proof of a specific person or permanent device identity.

---

## Category 5, Threat Intelligence

**Q13: What is MITRE ATT&CK and how do you use it?**

```text
MITRE ATT&CK is a knowledge base describing adversary
tactics and techniques based on observed behaviour.

Tactic
The adversary objective.

Technique
How the objective is pursued.

Subtechnique
A more specific implementation of a technique.
```

Analysts can use ATT&CK to:

```text
Map observed behaviour
Communicate findings
Assess detection coverage
Identify coverage gaps
Support detection engineering
Organize adversary behaviour
```

The evidence boundary matters.

A technique you investigated is not automatically a technique you observed.

---

**Q14: What is threat intelligence and why does it matter?**

Threat intelligence provides external context about threats, infrastructure, campaigns, vulnerabilities, techniques, and indicators.

Common categories include:

```text
Strategic
Higher level information for organisational decisions.

Operational
Information about campaigns and adversary operations.

Tactical and technical
TTPs, indicators and other information used during
defensive operations.
```

Sources can include:

```text
VirusTotal
AbuseIPDB
AlienVault OTX
MITRE ATT&CK
CISA
Vendor research
```

Reputation information is context, not automatic attribution.

For example, thousands of abuse reports do not by themselves identify the actor behind one specific connection.

---

## Category 6, Malware and Attacks

**Q15: Virus, worm, trojan, RAT, ransomware?**

```text
Virus
Malicious code that infects or modifies another file
or program and commonly depends on execution to spread.

Worm
Malware capable of self propagation between systems.

Trojan
Malware presented as legitimate or desirable software
to encourage execution.

RAT
Remote access software or malware that provides remote
control of a system. Legitimate remote administration
software can also be abused by attackers.

Ransomware
Malware that denies access to data or systems, commonly
through encryption, and demands payment.
```

The operational distinction matters because different propagation and execution mechanisms change containment and scoping priorities.

---

**Q16: What is a man in the middle attack?**

A man in the middle attack occurs when an attacker positions themselves between communicating parties and intercepts or manipulates the communication.

Possible mechanisms include:

```text
ARP spoofing
Rogue wireless access points
Traffic interception
Protocol downgrade techniques
```

Evidence depends on the mechanism.

Examples can include unexpected ARP changes, certificate warnings, unusual gateway information, or protocol behaviour inconsistent with the expected secure connection.

Weak cryptographic configuration can increase exposure to some downgrade or interception scenarios, but a weak cipher finding alone does not establish that a man in the middle attack occurred.

---

## Quick Fire

```text
Port 443?           HTTPS commonly
Port 22?            SSH
Port 445?           SMB
Port 3389?          RDP
Zero day?           Newly discovered or exploited vulnerability
                    for which effective remediation may not yet
                    be available
CVE?                Common Vulnerabilities and Exposures identifier
CVSS?               Common Vulnerability Scoring System
IOC?                Indicator of Compromise
TTP?                Tactics, Techniques, and Procedures
SIEM?               Security Information and Event Management
EDR?                Endpoint Detection and Response
SOC?                Security Operations Center
PCAP?               Packet capture
SPL?                Search Processing Language
KQL?                Kusto Query Language
Lateral movement?   Movement between systems or resources after
                    obtaining access
Persistence?        Maintaining access across interruptions
C2?                 Command and control
Beaconing?          Repeated communication that may occur at
                    regular intervals
Dwell time?         Time an adversary remains in an environment
                    before detection or removal
```
