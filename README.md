# SOC Analyst Interview Prep Guide

Two files. A technical question bank and five incident scenarios. Both built to be practised out loud, not read.

## What This Is

Knowing the material and getting the offer are different problems.

The interview does not test what you know. It tests whether you can explain it in order, under pressure, to someone who already knows the answer. That is a performance skill, and the only way to build it is reps.

Personal reference. Not a portfolio piece.

## Technical Question Bank

[questions/technical_questions.md](questions/technical_questions.md)

16 questions with structured answers across six categories.

Networking: OSI model, TCP vs UDP, three way handshake, common ports.

Security fundamentals: CIA triad, authentication vs authorisation, defence in depth.

SOC operations: SIEM vs EDR, playbooks, the incident response lifecycle.

Tools: Splunk SPL, Wireshark workflow.

Threat intelligence: MITRE ATT&CK, intel types and sources.

Malware: virus, worm, trojan, RAT, ransomware, MITM.

Plus a quick fire round of definitions to know cold.

What matters:

Depth over breadth. Four things explained properly beats twelve things recited.

Every answer connects back to the work. A definition is a definition. A definition plus why it matters on an alert is an analyst.

The quick fire round is the technical screen. Those are the questions that decide whether you get to the panel.

## Incident Response Scenarios

[scenarios/incident_scenarios.md](scenarios/incident_scenarios.md)

| Scenario | Alert | Severity | What It Tests |
|---|---|---|---|
| 1 | SSH brute force with successful login | High | Spotting the success in the noise |
| 2 | Phishing email with attachment | High | Header analysis and scope |
| 3 | Ransomware mass encryption | Critical | Containing before investigating |
| 4 | PowerShell spawned by Word | High | Reading the process tree first |
| 5 | Impossible travel | High | Not locking out an innocent user |

What matters:

They test the process, not the answer. Saying "I'd block the IP" gets you nothing. Saying why you checked for a successful login before blocking anything gets you the job.

Order is the answer. Containment before investigation on critical. Investigation before eradication always. Get the sequence wrong and every individual step being correct does not save it.

Each scenario has one step people skip. That is the thing to drill.

MITRE mapping mid answer signals depth, but only if it is honest. Mapping a technique you did not observe is worse than not mapping.

## How to Use It

```
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
  The gap between knowing and saying is the whole problem

DAY BEFORE
  Quick fire round, all of it, cold
  Portfolio projects, every detail
  STAR answers with real artifacts behind them
  Know which tools you have used and which you have not
```

Week 3 is the one that gets skipped and it is the one that works. Reading an answer builds recognition. Saying it builds recall, and the interview only tests recall.

## MITRE Coverage Across Scenarios

| Technique | ID | Scenario |
|---|---|---|
| Brute force, password guessing | T1110.001 | 1 |
| Valid accounts | T1078 | 1, 5 |
| Phishing, spearphishing link | T1566.002 | 2 |
| Masquerading, match legitimate name | T1036.005 | 2 |
| Data encrypted for impact | T1486 | 3 |
| Exfiltration over C2 channel | T1041 | 3 |
| Remote services, SMB admin shares | T1021.002 | 3 |
| Command and scripting interpreter, PowerShell | T1059.001 | 4 |
| User execution, malicious file | T1204.002 | 4 |

## Repository Structure

```
soc-interview-prep-guide/
├── README.md
├── questions/
│   └── technical_questions.md
└── scenarios/
    └── incident_scenarios.md
