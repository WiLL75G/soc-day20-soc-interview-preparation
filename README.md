# SOC Analyst Interview Prep Guide

Two files. A technical question bank and five incident scenarios. Both built to be practised out loud, not just read.

## What This Is

Knowing the material and explaining it under pressure are different skills.

An interview tests both. You need the technical knowledge, but you also need to explain your reasoning in order, defend your conclusions, and communicate clearly to someone who may already know the answer.

That is a performance skill, and the only way to build it is reps.

Personal reference. Not a portfolio piece.

## Technical Question Bank

[questions/technical_questions.md](questions/technical_questions.md)

16 questions with structured answers across six categories.

Networking: OSI model, TCP vs UDP, three way handshake, common ports.

Security fundamentals: CIA triad, authentication vs authorisation, defence in depth.

SOC operations: SIEM vs EDR, playbooks, incident response.

Tools: Splunk SPL, Wireshark workflow.

Threat intelligence: MITRE ATT&CK, intelligence types and sources.

Malware: virus, worm, trojan, RAT, ransomware, MITM.

Plus a quick fire round of definitions to know cold.

What matters:

Depth over breadth. Four things explained properly beats twelve things recited.

Every answer connects back to the work. A definition is a definition. A definition plus why it matters during an investigation shows analyst thinking.

The quick fire round prepares for the short technical questions that can appear during an initial screen.

## Incident Response Scenarios

[scenarios/incident_scenarios.md](scenarios/incident_scenarios.md)

| Scenario | Alert | Severity | What It Tests |
|---|---|---|---|
| 1 | SSH brute force with successful login | High | Investigating the success after the failures |
| 2 | Phishing email with attachment | High | Header analysis and scope |
| 3 | Ransomware mass encryption | Critical | Rapid containment and evidence preservation |
| 4 | PowerShell spawned by Word | High | Reading the process tree before decoding |
| 5 | Impossible travel | High | Validating before containment |

What matters:

They test the process, not just the final answer.

Saying "I'd block the IP" skips the investigation.

Explaining why you checked whether the successful authentication belonged to the same source and what happened after authentication shows the reasoning behind the action.

Order matters, but there is no single sequence that fits every incident.

A rapidly spreading ransomware event may justify immediate containment while another high severity alert may need validation before disruptive action.

Investigation should also inform eradication so the response removes what the evidence supports rather than only the first artifact discovered.

Each scenario has one step people commonly skip. That is the thing to drill.

MITRE mapping can add depth to an answer, but only when the mapping matches the evidence.

A technique being plausible is different from a technique being observed.

## How to Use It

```text
WEEK 1, technical foundations
  Read technical_questions.md daily
  Test yourself with the answers covered
  Networking and fundamentals first

WEEK 2, scenarios
  Work all 5 without reading the model answer first
  Score honestly
  Repeat anything below 4

WEEK 3, out loud
  Say the answers, do not read them
  Record yourself, listen back
  Compare what you knew with what you could explain

DAY BEFORE
  Quick fire round, all of it, cold
  Portfolio projects, every detail
  STAR answers with real artifacts behind them
  Know which tools you have used and which you have not
```

Week 3 is the one that gets skipped and it is the one I want to test directly.

Reading an answer builds recognition.

Saying it without the answer in front of you tests recall and whether the reasoning can actually be communicated.

## Lessons Learned

The MITRE tables in both files were originally more confident than the model answers underneath them.

Several techniques were listed as settled when the model answer's own investigation steps treated the same behaviour as an open question: whether a document arrived by email, whether data actually left over a C2 channel, whether SMB was the lateral movement path, or whether an impossible travel alert represented real account compromise.

The table and the answer were making different claims, and the table was asserting more than the investigation had established.

A second pass caught a different problem.

Scenario 2's alert listed an attachment, while an earlier mapping used Spearphishing Link.

That was not an evidence confidence problem. The relevant detail was already sitting directly in the alert and had not been checked closely enough against the ATT&CK mapping.

The two errors are different.

Overclaiming happens when reasoning outruns evidence.

Verification failure happens when the evidence is available but is not checked carefully enough against the conclusion.

The correction was not to remove every technique that still matters to the investigation.

It was to distinguish three things:

```text
Observed
      ↓
Supported by current evidence

Conditional
      ↓
Requires a specific investigation result

Not established
      ↓
Do not report it as observed
```

That distinction applies just as much to interview rehearsal as it does to a technical investigation.

## What I Would Improve

I would add a sixth scenario built around a genuinely ambiguous alert where investigation ultimately supports closing the alert as benign.

The five current scenarios primarily exercise malicious or potentially malicious activity.

Practising the point where the evidence supports standing down would test a different analyst skill: knowing when not to escalate.

I would also record an actual pass through Week 3 and note where the gap between reading an answer and explaining it from memory appears.

That would test the learning method against my own performance rather than assuming it works because the method sounds reasonable.

## MITRE Coverage Across Scenarios

### Supported by the scenario evidence

| Technique | ID | Scenario | Note |
|---|---|---|---|
| Brute force, password guessing | T1110.001 | 1 | |
| Valid accounts | T1078 | 1 | Confirmed once step 2 identifies the account behind the successful login, a quick check given the login immediately follows the brute force from the same source, not left open the way scenario 5's is |
| Data encrypted for impact | T1486 | 3 | |
| Command and scripting interpreter, PowerShell | T1059.001 | 4 | |

### Conditional on investigation

| Technique | ID | Scenario | What Would Establish It |
|---|---|---|---|
| Phishing, spearphishing attachment | T1566.001 | 2 | Establish that invoice.pdf is the malicious attachment used for delivery |
| Exfiltration over C2 channel | T1041 | 3 | Establish that data was actually exfiltrated over the C2 channel |
| Remote services, SMB admin shares | T1021.002 | 3 | Establish that SMB admin shares were used for lateral movement |
| User execution, malicious file | T1204.002 | 4 | Establish that the user executed or opened the malicious file |
| Phishing, spearphishing attachment | T1566.001 | 4 | Establish that the Word document arrived through a phishing attachment |
| Valid accounts | T1078 | 5 | Establish unauthorized use of the user's valid account |

Removed during review:

T1036.005, Masquerading: Match Legitimate Resource Name or Location, was previously associated with scenario 2's spoofed sender. That subtechnique does not describe spoofing a From header and was removed.

T1566.002, Spearphishing Link, was also removed from scenario 2 because the alert shows an attachment rather than an evidenced malicious link.

The attachment itself does not automatically establish T1566.001. The investigation still needs to establish that the attachment was the malicious delivery mechanism, so scenario 2 and scenario 4 carry the same subtechnique as conditional rather than confirmed, consistent with each other.

## Repository Structure

```text
.
├── README.md
├── questions/
│   └── technical_questions.md
└── scenarios/
    └── incident_scenarios.md
```

---

## Author

William Gokah

SOC Analyst Portfolio

[![LinkedIn](https://img.shields.io/badge/LinkedIn-WilliamInCyber-blue?style=flat&logo=linkedin)](https://linkedin.com/in/WilliamInCyber) [![X](https://img.shields.io/badge/X-WilliamInCyber-black?style=flat&logo=x)](https://x.com/WilliamInCyber)
