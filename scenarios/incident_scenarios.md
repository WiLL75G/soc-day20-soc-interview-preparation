# SOC Analyst Incident Response Scenarios

Read each alert as if it just fired.

Write your answer before reading the model.

Score honestly.

Every scenario has one step candidates commonly skip. It is named after each model answer because knowing what you would miss is worth more than memorizing a perfect response.

---

## Scenario 1, SSH Brute Force

**The alert:**

```text
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

```text
1. Acknowledge the alert
   Record the time and initial observations

2. Validate the authentication pattern
   Confirm the failed attempts
   Confirm the successful authentication
   Determine whether the success came from the same source
   Identify the account involved

3. Investigate what followed
   Review authentication and host telemetry
   Look for commands, processes, network activity,
   privilege changes, file changes or persistence

4. Enrich the source
   VirusTotal
   AbuseIPDB
   WHOIS
   Treat reputation as context, not attribution

5. Contain according to the evidence and procedure
   Block the confirmed malicious source where appropriate
   Isolate the affected host if compromise is established
   Disable or reset the affected account if required
   Revoke active sessions

6. Escalate
   Provide the authentication evidence, affected account,
   timeline, containment actions and unresolved questions

7. Document
   Record the evidence and every response action
```

MITRE:

```text
T1110.001 Password Guessing

T1078 Valid Accounts once step 2 identifies the account
behind the successful login. That confirmation is quick
here, the login immediately follows the brute force from
the same source, unlike scenario 5 where the same technique
stays open until the user is actually reached.
```

**The step candidates skip:** confirming and investigating the successful authentication.

Forty seven failures show repeated password guessing.

A subsequent success from the same source against the targeted account changes the investigation significantly, but the analyst still needs to establish the account involved and what occurred after authentication.

**Second miss:** session revocation.

Changing or disabling credentials does not necessarily terminate every existing authenticated session.

**Third:** broader network blocking is a containment decision, not an evidence conclusion.

If the source belongs to a larger Tor exit range, the evidence establishes activity from the observed address. Expanding a block to the wider range requires a risk decision based on the environment and active threat.

---

## Scenario 2, Phishing Email

**The alert:**

```text
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

```text
1. Preserve the message
   Do not open the attachment
   Do not interact with suspicious links

2. Review the headers
   Examine From
   Examine Reply-To
   Review the sending infrastructure
   Review SPF, DKIM and DMARC results when available

3. Investigate the indicators
   Enrich the sending IP
   Enrich relevant domains
   Hash and safely investigate the attachment if available
   Review URLs without directly visiting them

4. Scope the campaign
   Who else received the message?
   Did anyone open the attachment?
   Did anyone visit associated infrastructure?
   Check email gateway, proxy, endpoint and identity
   telemetry where available

5. Contain based on confirmed indicators
   Quarantine matching messages
   Block confirmed malicious infrastructure
   Apply endpoint or account containment if interaction
   is established

6. Notify and escalate according to procedure

7. Document the evidence, scope and response
```

MITRE:

```text
T1566.001 Spearphishing Attachment is conditional.

The email contains invoice.pdf, but the investigation still
needs to establish that the attachment is the malicious
delivery mechanism before reporting the subtechnique as
observed.
```

**The step candidates skip:** scope.

Quarantining one message does not answer whether other recipients received the same campaign or whether anyone interacted with it.

**Reply-To note:** the mismatch is useful evidence.

`Reply-To` is sender controlled just like `From`. It may point toward infrastructure useful to the attacker, but it is not an authenticated statement of the attacker's identity.

`T1036.005 Match Legitimate Resource Name or Location` is not mapped merely because the displayed sender impersonates a brand.

---

## Scenario 3, Ransomware

**The alert:**

```text
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

```text
1. Begin rapid containment
   Mass encryption is actively affecting the endpoint

2. Isolate the host
   Prefer an approved isolation method that stops network
   spread while preserving useful evidence where possible

3. Investigate the external connection
   Determine what 91.238.14.7 represents
   Check whether other hosts communicated with it
   Do not call the traffic exfiltration without evidence

4. Scope the incident
   Search for the same encryption behaviour elsewhere
   Investigate possible lateral movement
   Examine SMB activity if telemetry makes it relevant
   Do not assume SMB was the propagation path

5. Escalate immediately
   Tier 2 or incident response
   Appropriate management
   Legal or compliance when required by data and policy

6. Preserve evidence
   Preserve relevant endpoint and network telemetry
   Capture volatile evidence when appropriate and possible
   Avoid unnecessary rebooting or destruction of evidence

7. Plan eradication and recovery
   Determine persistence and scope
   Identify known clean recovery points
   Validate eradication before restoration
```

MITRE:

```text
CONFIRMED

T1486 Data Encrypted for Impact


CONDITIONAL

T1041 Exfiltration Over C2 Channel
Requires evidence that data was actually exfiltrated.

T1021.002 SMB Windows Admin Shares
Requires evidence that SMB admin shares were actually
used for lateral movement.
```

**The step candidates skip:** adapting the sequence to the incident.

Rapidly progressing encryption can justify containment before a full investigation is complete.

That does not mean evidence preservation stops mattering.

The response should contain the damage while preserving as much useful evidence as circumstances allow.

---

## Scenario 4, Suspicious PowerShell

**The alert:**

```text
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

```text
1. Examine the process relationship
   WINWORD.EXE spawning PowerShell is highly suspicious
   context and deserves immediate investigation

2. Decode and inspect the PowerShell
   Decode the EncodedCommand safely
   Review PowerShell Script Block Logging such as
   Event ID 4104 when available
   Determine what the command actually attempted

3. Trace the document
   Identify the document involved
   Determine how it reached the endpoint
   Determine whether the user opened it
   Check whether other users received the same artifact

4. Review endpoint telemetry
   Child processes
   Downloads
   File changes
   Registry activity
   Network connections
   Credential or persistence activity

5. Contain according to the findings
   Isolate the host when warranted
   Apply account containment if credentials are affected
   Block confirmed malicious infrastructure

6. Escalate with the decoded command and evidence

7. Scope for related activity
   Search for the same document, command, hash,
   destination or authentication activity elsewhere
```

MITRE:

```text
CONFIRMED

T1059.001 PowerShell


CONDITIONAL

T1204.002 Malicious File
Requires evidence that the user executed or opened the
malicious file.

T1566.001 Spearphishing Attachment
Requires evidence that the document was delivered through
a phishing attachment.
```

**The step candidates skip:** the process relationship.

The encoded command is visually interesting, but `EncodedCommand` alone is not enough to establish malicious execution.

The unusual parent child relationship makes the activity a strong investigation lead while decoding and endpoint telemetry establish what actually occurred.

**Execution policy note:** `ExecutionPolicy Bypass` is useful context, but PowerShell execution policy is not intended to function as a security boundary.

---

## Scenario 5, Impossible Travel

**The alert:**

```text
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

```text
1. Validate the alert
   The locations cannot represent ordinary physical travel
   by one person in the stated time, but the IP locations
   may not represent the user's physical location

2. Investigate both authentication events
   Are either IP addresses known for this user?
   Could VPN, proxy, mobile carrier or cloud infrastructure
   explain the locations?
   Review device, MFA and identity context where available

3. Verify with the user through an approved independent
   communication channel when appropriate

4. Contain if unauthorized access is established
   Reset credentials where required
   Revoke active sessions
   Disable the account if necessary
   Block confirmed malicious infrastructure

5. Investigate the suspicious session
   What resources were accessed?
   Were files downloaded?
   Were security settings changed?
   Was additional authentication activity observed?

6. Escalate and document the complete timeline
```

MITRE:

```text
T1078 Valid Accounts is conditional.

Map it when investigation establishes unauthorized use of
the valid account rather than from the impossible travel
alert alone.
```

**The step candidates skip:** validation before disruptive containment.

VPNs, proxies, mobile carrier infrastructure, cloud services and inaccurate IP geolocation can all complicate geographic login detections.

The alert is a reason to investigate quickly.

It is not proof of account compromise.

---

## Scoring

```text
5  Complete investigation logic, appropriate sequence,
   evidence boundaries explained

4  Most important steps present, minor gaps

3  Core investigation present but important response,
   scoping or evidence steps missing

2  Several correct ideas but major gaps

1  Needs work
```

Target: **4 or above on all five before interviewing.**

Score the reasoning as well as the steps.

A strong answer should explain:

```text
What the alert establishes
        ↓
What still needs investigation
        ↓
What action is justified now
        ↓
What evidence would change the verdict
```
