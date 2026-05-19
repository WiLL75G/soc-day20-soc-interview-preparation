# Day 20 – SOC Analyst Interview Prep Guide

---

## Summary

- **Resource Type:** Interview Preparation System
- **Target Role:** SOC Analyst Tier 1 / Tier 2
- **Resources Produced:** 2 — Technical Question Bank, Incident Response Scenarios
- **Questions Covered:** 16 Technical + 5 Full Scenarios + Quick Fire Round
- **Status:** Complete — Interview Ready

---

## Executive Summary

A comprehensive SOC analyst interview preparation system was built covering every dimension of the hiring process. A technical question bank was produced spanning networking, security fundamentals, SOC operations, tools, threat intelligence, and malware — each with full structured answers. Five realistic incident response scenarios were built with model answers and MITRE ATT&CK mappings — covering SSH brute force, phishing, ransomware, malicious PowerShell, and impossible travel. This guide is designed to take a candidate from nervous to confident in every interview format.

---

## What's Inside

---

### 1. Technical Question Bank

16 full questions with structured answers across 6 categories:

- **Category 1 — Networking:** OSI model, TCP vs UDP, 3-way handshake, common ports
- **Category 2 — Security Fundamentals:** CIA Triad, authentication vs authorisation, defence in depth
- **Category 3 — SOC Operations:** SIEM vs EDR, playbooks, incident response lifecycle (PICERL)
- **Category 4 — Tools:** Splunk SPL, Wireshark investigation workflow
- **Category 5 — Threat Intelligence:** MITRE ATT&CK, threat intel types and sources
- **Category 6 — Malware & Attacks:** Virus vs worm vs trojan vs RAT, MITM attacks

Plus a **Quick Fire Round** — 15 definitions every analyst must know cold.

#### Key Insight:

- Interviewers test depth not breadth — know fewer things thoroughly rather than many things superficially
- Every answer should connect back to the SOC analyst role — not just define the concept
- The Quick Fire Round covers the most commonly asked 1-line questions in technical screens

---

### 2. Incident Response Scenarios

5 full scenarios with model answers and MITRE ATT&CK mapping:

| Scenario | Alert Type | Severity | Key Skill Tested |
|---|---|---|---|
| 1 | SSH Brute Force + Successful Login | HIGH | Triage + containment + escalation |
| 2 | Phishing Email with Attachment | HIGH | Header analysis + scope check |
| 3 | Ransomware — Mass File Encryption | CRITICAL | Speed + containment + IR activation |
| 4 | Suspicious PowerShell from Word | HIGH | Malware indicators + EDR investigation |
| 5 | Impossible Travel — Credential Compromise | HIGH | User verification + session management |

#### Key Insight:

- Scenario interviews test your thinking process — not just your answer
- Always state containment before investigation for CRITICAL severity alerts
- MITRE ATT&CK mappings in your answers signal Tier 2 level thinking to interviewers

---

## How to Use This Guide

```
WEEK 1 — Technical foundations
Read technical_questions.md daily
Test yourself without looking at answers
Focus on networking and security fundamentals first

WEEK 2 — Scenario practice
Work through all 5 scenarios in incident_scenarios.md
Score yourself honestly using the scoring guide
Repeat any scenario where you scored below 4/5

WEEK 3 — Mock interviews
Ask a friend or use this guide to run mock interviews
Practice saying answers out loud — not just reading them
Record yourself and review for clarity and confidence

DAY BEFORE — Final review
Quick Fire Round — know all 15 answers cold
Review your portfolio projects — know every detail
Prepare your STAR method answers using real lab work
```

---

## MITRE ATT&CK Coverage

| Technique ID | Technique | Scenario |
|---|---|---|
| T1110.001 | Brute Force: Password Guessing | Scenario 1 |
| T1078 | Valid Accounts | Scenario 1 + 5 |
| T1566.001 | Phishing: Spearphishing Link | Scenario 2 |
| T1036.005 | Masquerading | Scenario 2 |
| T1486 | Data Encrypted for Impact | Scenario 3 |
| T1041 | Exfiltration Over C2 Channel | Scenario 3 |
| T1059.001 | PowerShell | Scenario 4 |
| T1204.002 | Malicious File | Scenario 4 |
| T1133 | External Remote Services | Scenario 5 |

---

## Analyst Insight

Interview preparation is itself a skill. The difference between a candidate who knows the material and one who gets the offer is the ability to communicate under pressure — clearly, confidently, and in the right order. Containment before investigation. MITRE before closing. Documentation always. Practicing these scenarios out loud before the interview is what converts knowledge into performance. Your portfolio from Days 1–19 is your proof of concept — this guide is how you talk about it.

---

## Learning Outcome

- Answer 16 core technical SOC interview questions with confidence
- Walk through 5 incident response scenarios using structured analyst thinking
- Apply the PICERL incident response framework under interview pressure
- Map incident findings to MITRE ATT&CK in real time during a scenario
- Know the Quick Fire Round definitions cold — no hesitation
- Use real portfolio projects as evidence in technical and behavioural answers

---

## Repository Structure

```
soc-interview-prep-guide/
├── README.md
├── questions/
│   └── technical_questions.md
└── scenarios/
    └── incident_scenarios.md
```

---

## Conclusion

This interview prep system covers every format a SOC analyst candidate will face — technical screens, scenario panels, and behavioural rounds. With 16 technical questions, 5 full incident scenarios, and a Quick Fire Round, every dimension of the hiring process is covered. Combined with the 28-day portfolio, this guide represents a complete interview-ready package for any SOC Analyst Tier 1 or Tier 2 role.
