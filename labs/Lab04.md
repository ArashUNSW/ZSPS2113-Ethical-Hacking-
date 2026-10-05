**ZSPS2113 Ethical Hacking and Penetration Testing | Week 4 Lab**

Authorised training environment only

# Week 4 Lab – Injection Testing

Tutorial / Stage: Injection Testing

Targets: DVWA / WebGoat

Suggested tools: Browser, Burp Suite Proxy and Repeater

Estimated Time: Approximately 2 hours for core activities, with additional time for advanced extension tasks.

## Learning Objectives

By the end of this lab, students should be able to:

- verify the authorised DVWA/WebGoat target is reachable;
- identify user-controlled parameters;
- establish normal application behaviour before testing;
- capture and replay HTTP requests using Burp Repeater;
- demonstrate SQL injection within designated training lessons;
- observe unsafe command/input handling using controlled inputs;
- compare normal and modified requests and responses;
- distinguish demonstrated evidence from unsupported assumptions;
- explain confidentiality, integrity and availability implications;
- recommend parameterised queries for SQL injection;
- recommend server-side input validation and safer APIs for command injection;
- retest a control after remediation where supported;
- produce evidence-based findings suitable for a penetration-testing report.

## Scenario

You are continuing an authorised penetration-testing exercise.

This week focuses on injection vulnerabilities, particularly:

- SQL injection;
- command/input handling;
- identifying vulnerable parameters;
- assessing demonstrated impact;
- recommending appropriate remediation.
Kali-Attacker is the testing workstation.

Ubuntu-Server hosts the authorised training applications.

DVWA / WebGoat are deliberately vulnerable applications.

Burp Suite Repeater is used to reproduce and modify requests.

> **Alert:** Do not test university systems, public websites, other students' systems, or any application that has not been explicitly authorised.

## Part A – Prepare the Week 4 Environment

### Task 1 – Create the Week 4 Evidence Folder

> **Practical guidance**
> **VM / Tool:** Kali-Attacker terminal.
> **Instructions:** Create a dedicated Week 4 folder before any testing so screenshots, notes and exported evidence stay separate from previous weeks.
> **Commands / Inputs:** `mkdir -p ~/lab-evidence/week4`; `cd ~/lab-evidence/week4`; `pwd` ; `ls -ld .`
> **Expected evidence:** Capture the terminal showing the Week 4 path and folder.

On Kali-Attacker:

```bash
mkdir -p ~/lab-evidence/week4
cd ~/lab-evidence/week4
pwd
```

**Expected location**

```text
/home/<your-user>/lab-evidence/week4
```

#### Evidence to capture

Take one screenshot showing the Week 4 evidence folder.

### Task 2 – Verify the Authorised Application

> **Practical guidance**
> **VM / Tool:** Ubuntu-Server terminal for the containers; Kali-Attacker browser/terminal for the connectivity check.
> **Instructions:** First identify Ubuntu's current lab IP, then confirm that the assigned DVWA or WebGoat container is running and note its published port. From Kali, test HTTP reachability and then open the application in the browser.
> **Commands / Inputs:** Ubuntu: `ip -br addr` ; `sudo docker ps --format 'table {{`.Names}}\t{{.Status}}\t{{.Ports}}'. If needed: `sudo docker start dvwa OR sudo docker start webgoat`. Kali: `curl -I http://<UBUNTU_IP>/ for DVWA`; for WebGoat use the instructor-published port (commonly http://<UBUNTU_IP>:8081/WebGoat in this lab).
> **Expected evidence:** Record target IP, container status, published port, HTTP status and a screenshot of the application page.

Only use the application assigned by your instructor.

#### DVWA

```bash
curl -I http://<UBUNTU_IP>/
```

If required:

```bash
sudo docker ps
```

#### WebGoat

If WebGoat has already been provided:

```bash
sudo docker ps
```

Verify the published port and open the application in the browser.

| Item | Observed value |
| --- | --- |
| Application | DVWA / WebGoat |
| Target IP |  |
| Port |  |
| HTTP status |  |
| Application page loads? | Yes / No |

### Task 3 – Confirm the Injection Lesson

> **Practical guidance**
> **VM / Tool:** Kali-Attacker browser.
> **Instructions:** Log in to the assigned training application and navigate only to the instructor-designated SQL Injection and Command/Input lesson. For DVWA, also record the selected DVWA security level before testing.
> **Commands / Inputs:** DVWA: Vulnerabilities -> SQL Injection or Command Injection. WebGoat: open the designated Injection lesson from the lesson menu.
> **Expected evidence:** Record the application, lesson name, URL/path and visible input field.

Locate only the instructor-designated lesson.

Possible examples:

- DVWA → SQL Injection
- DVWA → Command Injection
- WebGoat → designated SQL Injection lesson
- WebGoat → designated command/input lesson
**Record**

| Item | Observation |
| --- | --- |
| Application |  |
| Lesson/module |  |
| URL/path |  |
| Input field observed |  |

## Part B – Establish a Normal Baseline

### Task 4 – Capture a Normal Request

> **Practical guidance**
> **VM / Tool:** Kali-Attacker browser with Browser Developer Tools or Burp Proxy.
> **Instructions:** Submit one normal, expected value before changing any input. This establishes the baseline against which every later request will be compared.
> **Commands / Inputs:** SQL baseline example: 1. Command/input baseline example: 127.0.0.1.
> **Expected evidence:** Record method, path, parameter, normal value, response status/length and normal application behaviour.

Submit a normal value before attempting injection.

Example SQL input:

```text
1
```

Example command/input value:

```text
127.0.0.1
```

Using Browser Developer Tools or Burp:

| Item | Observation |
| --- | --- |
| HTTP method |  |
| Path |  |
| Parameter name |  |
| Normal value |  |
| Response status |  |
| Response length |  |
| Application behaviour |  |

#### Knowledge Check

Why is it important to record normal behaviour before modifying the request?

### Task 5 – Capture the Request in Burp Suite

> **Practical guidance**
> **VM / Tool:** Kali-Attacker running Burp Suite and the lab browser.
> **Instructions:** Start Burp, browse to the authorised application through Burp, submit the baseline request and locate it in Proxy -> HTTP history. Use the Burp browser or the instructor-configured browser proxy.
> **Commands / Inputs:** `burpsuite`. Typical local Burp proxy listener: 127.0.0.1:8080.
> **Expected evidence:** Capture one baseline request/response showing the target path and parameter; redact passwords, session IDs and tokens.

Start Burp:

```bash
burpsuite
```

Open:

Proxy → HTTP history

Locate the request generated by the designated lesson.

Inspect:

- method;
- path;
- parameters;
- cookies;
- status;
- response length.
#### Evidence to capture

Take one screenshot of the baseline request and response.

Do not expose passwords, session identifiers or authentication tokens.

## Part C – SQL Injection Testing

### Task 6 – Identify the SQL Parameter

> **Practical guidance**
> **VM / Tool:** Kali-Attacker browser and Burp.
> **Instructions:** Open the SQL injection lesson, submit a normal value, then inspect the resulting request to identify the exact user-controlled parameter and whether it is sent using GET or POST. Record the actual parameter used by your application version.
> **Commands / Inputs:** For DVWA the SQL Injection lesson commonly uses an id parameter; WebGoat parameter names vary by lesson, so use the value actually observed in Burp.
> **Expected evidence:** Record the parameter name, request method, normal value and normal response.

Open the designated SQL injection lesson.

Record the user-controlled input.

| Property | Observation |
| --- | --- |
| Parameter name |  |
| GET / POST |  |
| Normal value |  |
| Response |  |

### Task 7 – Test a SQL Metacharacter

> **Practical guidance**
> **VM / Tool:** Kali-Attacker browser or Burp Repeater.
> **Instructions:** Change only the identified SQL parameter to a single quote and compare the response with the baseline. Do not change unrelated parameters, cookies or session values.
> **Commands / Inputs:** Test input: '
> **Expected evidence:** Record whether you observe a database/application error, altered content, changed record count or no difference.

**Submit**

```text
'
```

**Observe the response.**

**Look for**

- database error;
- application error;
- different content;
- altered number of records;
- no observable difference.
| Test | Input | Observation |
| --- | --- | --- |
| Baseline | Normal value |  |
| Quote test | ' |  |

A changed response is a lead for further investigation, not automatic proof of SQL injection.

### Task 8 – Send the SQL Request to Repeater

> **Practical guidance**
> **VM / Tool:** Kali-Attacker, Burp Suite Repeater.
> **Instructions:** From Proxy -> HTTP history, send the SQL request to Repeater. Send the untouched baseline request at least once before modifying it so you know the request is reproducible.
> **Commands / Inputs:** Burp: right-click request -> Send to Repeater -> Repeater -> Send.
> **Expected evidence:** Keep the baseline response available for side-by-side comparison with later modified requests.

In Burp:

1. Locate the request in HTTP history.

2. Right-click.

3. Select Send to Repeater.

4. Open Repeater.

5. Send the original request.

Confirm that the response is reproducible.

### Task 9 – Compare Boolean Conditions

> **Practical guidance**
> **VM / Tool:** Kali-Attacker, Burp Repeater.
> **Instructions:** Keep the request identical except for the SQL parameter. Send one condition expected to evaluate true and one expected to evaluate false. Compare status, body content and response length. Do not attempt database extraction.
> **Commands / Inputs:** Controlled examples: ' OR '1'='1  and  ' OR '1'='2
> **Expected evidence:** Capture the three comparable responses: baseline, true condition and false condition.

Where supported by the designated lesson, test a controlled condition such as:

```text
' OR '1'='1
```

Then compare with:

```text
' OR '1'='2
```

| Feature | Baseline | True condition | False condition |
| --- | --- | --- | --- |
| Status |  |  |  |
| Response length |  |  |  |
| Records/content |  |  |  |
| Error/message |  |  |  |
| Behaviour changed? |  |  |  |

#### Evidence to capture

Capture the Repeater requests and the corresponding responses.

### Task 10 – Identify the Injection Point

> **Practical guidance**
> **VM / Tool:** Kali-Attacker, Burp Repeater and your evidence notes.
> **Instructions:** Use the request/response comparison to identify exactly which parameter changes application behaviour. Describe only what your evidence supports, not what you assume the database contains.
> **Commands / Inputs:** No new payload is required. Reuse the baseline and controlled requests already captured.
> **Expected evidence:** Write the affected parameter, method, normal behaviour and modified behaviour, with the relevant screenshot/request reference.

**Complete**

Affected parameter: __________

Request method: __________

Normal behaviour: __________

Modified behaviour: __________

#### Knowledge Check

What evidence supports the conclusion that user input is influencing SQL query behaviour?

## Part D – Command / Input Handling

### Task 11 – Establish the Command Baseline

> **Practical guidance**
> **VM / Tool:** Kali-Attacker browser.
> **Instructions:** Open the authorised command/input-handling lesson and submit a legitimate value that matches the field's intended purpose. For DVWA Command Injection, use a normal IP address first.
> **Commands / Inputs:** Normal example: 127.0.0.1
> **Expected evidence:** Record parameter name, intended function, normal value and normal output.

Open the designated command/input-handling lesson.

Use a normal value, for example:

```text
127.0.0.1
```

**Record**

| Item | Observation |
| --- | --- |
| Input parameter |  |
| Intended purpose |  |
| Normal value |  |
| Normal output |  |

### Task 12 – Capture the Request in Burp

> **Practical guidance**
> **VM / Tool:** Kali-Attacker, Burp Proxy and Repeater.
> **Instructions:** Locate the normal command/input request in HTTP history, inspect where the user value appears, then send that request to Repeater without changing it.
> **Commands / Inputs:** Burp: Proxy -> HTTP history -> right-click request -> Send to Repeater.
> **Expected evidence:** Record method, path, parameter and normal value; keep the baseline response for comparison.

Locate the request in Burp and send it to Repeater.

Identify where the user input appears.

| Item | Observation |
| --- | --- |
| Method |  |
| Path |  |
| Parameter |  |
| Normal value |  |

### Task 13 – Perform a Harmless Command-Handling Test

> **Practical guidance**
> **VM / Tool:** Kali-Attacker, Burp Repeater, against the authorised DVWA/WebGoat lesson only.
> **Instructions:** Change only the input parameter and append one harmless operating-system command. Use the smallest proof necessary and stop if you obtain clear evidence. Never modify files, users, permissions or services.
> **Commands / Inputs:** Examples for the Linux lab target: 127.0.0.1; whoami  or  127.0.0.1 && whoami
> **Expected evidence:** Capture the request and any additional output that demonstrates whether the second command was interpreted.

Only within the designated vulnerable lesson, use a controlled test such as:

```text
127.0.0.1; whoami
```

or, where supported:

```text
127.0.0.1 && whoami
```

Do not use commands that:

- modify files;
- create users;
- change permissions;
- stop services;
- establish shells;
- alter system configuration.

### Task 14 – Compare Baseline and Modified Input

> **Practical guidance**
> **VM / Tool:** Kali-Attacker, Burp Repeater.
> **Instructions:** Place the baseline and modified responses side by side and compare status, response length, normal application output and any additional command output.
> **Commands / Inputs:** Reuse the Task 11 baseline and Task 13 controlled request.
> **Expected evidence:** Complete the comparison table and capture the response section that contains the observable difference.

| Feature | Normal input | Modified input |
| --- | --- | --- |
| HTTP status |  |  |
| Response length |  |  |
| Normal application output |  |  |
| Additional command output |  |  |
| Username returned? |  |  |
| Other difference |  |  |

#### Evidence requirement

Capture one Burp Repeater request/response demonstrating the observed behaviour.

### Task 15 – Confirm Reproducibility

> **Practical guidance**
> **VM / Tool:** Kali-Attacker, Burp Repeater.
> **Instructions:** Repeat the same harmless request once to confirm that the result is reproducible. Do not escalate to additional commands after sufficient proof has been obtained.
> **Commands / Inputs:** Resend the exact Task 13 request once.
> **Expected evidence:** State the confirmed observation, affected parameter and evidence of command interpretation.

Repeat the same controlled request once.

Record only what is demonstrated.

Confirmed observation:

Affected parameter:

Evidence of command interpretation:

Stop when sufficient evidence has been collected.

## Part E – Understand the Impact

### Task 16 – Separate Evidence from Assumption

> **Practical guidance**
> **VM / Tool:** Kali-Attacker evidence folder or your report workstation; no new probing is required.
> **Instructions:** Review the screenshots and Burp responses already collected. For each observation, write one supported interpretation and one claim that would be unsupported by the current evidence.
> **Commands / Inputs:** Optional note file: `nano ~/lab-evidence/week4/evidence-vs-assumption`.txt
> **Expected evidence:** Complete the evidence/interpretation/assumption table using only results already demonstrated.

| Observed evidence | Supported interpretation | Unsupported assumption |
| --- | --- | --- |
| True/false SQL responses differ | Input may influence query logic | Entire database is compromised |
| whoami output appears | Input may reach OS command execution | Root access has been obtained |
| Database error appears | Input reaches database-related processing | All SQL injection attacks will succeed |
| Your example |  |  |

### Task 17 – Assess Security Impact

> **Practical guidance**
> **VM / Tool:** Kali-Attacker evidence folder or report workstation.
> **Instructions:** Assess the demonstrated issue against confidentiality, integrity, availability and privilege. Base each statement on the observed behaviour and clearly mark impacts that would require further verification.
> **Commands / Inputs:** No additional testing command is required.
> **Expected evidence:** Complete the impact table for SQL injection and command/input handling.

For each demonstrated weakness, consider:

#### Confidentiality

Could the weakness expose information?

#### Integrity

Could it potentially modify information or commands?

#### Availability

Could misuse affect application/service availability?

#### Privilege

What account or application privilege is involved?

**Complete**

| Impact area | SQL Injection | Command/Input Handling |
| --- | --- | --- |
| Confidentiality |  |  |
| Integrity |  |  |
| Availability |  |  |
| Privilege considerations |  |  |

Do not claim an impact that was not demonstrated.

## Part F – Remediation

### Task 18 – Understand SQL Injection Remediation

> **Practical guidance**
> **VM / Tool:** Kali-Attacker browser; use DVWA View Source or the WebGoat lesson explanation where provided.
> **Instructions:** Review how the vulnerable lesson handles database input and identify where user data is combined with SQL. Compare that pattern with a parameterised/prepared-statement design. Do not edit the target container unless the instructor explicitly asks you to.
> **Commands / Inputs:** Conceptual safe pattern: SELECT ... WHERE id = ? with the supplied value bound separately. Example PDO pattern: prepare(...); execute([$id]).
> **Expected evidence:** Explain why separating SQL code from user data prevents the input from changing query structure.

Consider the unsafe approach:

```text
query = "SELECT * FROM users WHERE id = '" + user_input + "'"
```

A safer approach separates SQL code from data:

```sql
SELECT * FROM users WHERE id = ?
```

with the value supplied separately.

#### Main control

Parameterised queries / prepared statements

**Complete**

Parameterised queries reduce SQL injection risk because __________.

### Task 19 – Identify Command/Input Remediation

> **Practical guidance**
> **VM / Tool:** Kali-Attacker browser and lesson source/explanation; Ubuntu changes are not required unless specifically instructed.
> **Instructions:** Identify how the lesson accepts command/input data. Propose a safer design: validate the expected format on the server, reject unexpected metacharacters, avoid invoking a shell, use a safer API and run with least privilege.
> **Commands / Inputs:** For an IP-address field, an example validation concept is strict IP-format validation/allow-listing before the value is used.
> **Expected evidence:** Complete the remediation table for unsafe shell construction, weak validation and excessive privileges.

Recommended controls include:

- strict server-side allow-list validation;
- expected data type and format checks;
- length restrictions;
- rejecting unexpected separators/metacharacters;
- avoiding construction of shell commands from user input;
- using safer application APIs;
- least-privilege service accounts.
**Complete**

| Weakness | Recommended control |
| --- | --- |
| SQL injection | Parameterised queries / prepared statements |
| Unsafe shell command construction |  |
| Weak input validation |  |
| Excessive application privileges |  |

## Part G – Retest the Control

### Task 20 – Retest After Remediation

> **Practical guidance**
> **VM / Tool:** Kali-Attacker browser and Burp Repeater; DVWA security settings or the WebGoat remediated lesson.
> **Instructions:** Where the training application provides a stronger/remediated implementation, repeat the exact same baseline and modified requests. Change only the control/security level, not the test case.
> **Commands / Inputs:** DVWA: DVWA Security -> select the instructor-specified higher level, then resend the same Repeater request. WebGoat: use the lesson's fixed/remediated stage if available.
> **Expected evidence:** Record before/after behaviour and identify the specific control that changed.

If the training application provides a remediated or higher-security version, repeat the same request.

| Behaviour | Before remediation | After remediation |
| --- | --- | --- |
| Normal input works |  |  |
| Modified input accepted |  |  |
| SQL behaviour changes |  |  |
| Command output appears |  |  |
| Input safely rejected |  |  |

#### Interpretation

State the specific control demonstrated by the retest.

Do not simply write:

“High is secure.”

Instead write:

“The modified input was rejected because __________.”

## Part H – Compare DVWA Security Levels

### Task 21 – Compare the Same Injection Test

> **Practical guidance**
> **VM / Tool:** Kali-Attacker browser and Burp Repeater; DVWA only.
> **Instructions:** Set DVWA to Low, Medium and High one at a time. For each level, capture the same function and same test input. Keep separate Repeater tabs and compare only like-for-like requests.
> **Commands / Inputs:** DVWA: DVWA Security -> Low / Medium / High. Verify the selected level in the application (and security cookie if visible).
> **Expected evidence:** Complete the Low/Medium/High comparison table and use 'Not observed' where no control is demonstrated.

If DVWA is used, repeat the same controlled workflow at:

- Low;
- Medium;
- High.
| Feature | Low | Medium | High |
| --- | --- | --- | --- |
| Same parameter tested |  |  |  |
| Normal request works |  |  |  |
| Quote accepted |  |  |  |
| Boolean test changes output |  |  |  |
| Input validation observed |  |  |  |
| Error behaviour |  |  |  |
| Other control |  |  |  |

Use Not observed when no evidence is available.

Do not infer a control because the level is named High.

## Part I – Evidence Collection

### Task 22 – Save Required Evidence

> **Practical guidance**
> **VM / Tool:** Kali-Attacker evidence folder.
> **Instructions:** Save screenshots and text evidence using the recommended filenames. Ensure no password, PHPSESSID, WebGoat token or other sensitive value is visible. Check that every file opens before finishing.
> **Commands / Inputs:** `ls -lh ~/lab-evidence/week4`
> **Expected evidence:** A complete, clearly named evidence set matching the task list.

**Recommended evidence**

1. authorised application running;

2. designated injection lesson;

3. baseline request;

4. SQL quote test;

5. SQL Boolean comparison;

6. Burp Repeater SQL evidence;

7. command/input baseline;

8. controlled command-handling evidence;

9. remediation/retest evidence;

10. completed findings table.

**Recommended filenames**

```text
01-target-running.png
02-injection-lesson.png
03-baseline-request.png
04-sqli-quote-test.png
05-sqli-boolean-test.png
06-sqli-repeater.png
07-command-baseline.png
08-command-test.png
09-remediation-retest.png
10-findings-summary.txt
```

## Part J – Findings Table

### Task 23 – Produce Evidence-Based Findings

> **Practical guidance**
> **VM / Tool:** Kali-Attacker or report workstation; use evidence already collected.
> **Instructions:** For each injection type, summarise the target, parameter, baseline, modified input, observed response, supported interpretation, impact, remediation and retest result. Do not add claims that are not supported by screenshots or Burp evidence.
> **Commands / Inputs:** Optional: `nano ~/lab-evidence/week4/10-findings-summary`.txt
> **Expected evidence:** A completed two-column findings table for SQL injection and command/input handling.

| Area | SQL Injection | Command/Input Handling |
| --- | --- | --- |
| Target |  |  |
| Parameter |  |  |
| Baseline behaviour |  |  |
| Modified input |  |  |
| Observed response |  |  |
| Supported interpretation |  |  |
| Potential impact |  |  |
| Remediation |  |  |
| Retest result |  |  |

## Part K – Findings Summary

### Task 24 – Write a 300–400 Word Findings Summary

> **Practical guidance**
> **VM / Tool:** Kali-Attacker evidence folder or report workstation.
> **Instructions:** Write 300-400 words that connect the evidence into a concise professional finding summary. Reference the baseline, Burp comparison, demonstrated impact, remediation, retest and limitations.
> **Commands / Inputs:** Optional: `nano ~/lab-evidence/week4/10-findings-summary`.txt
> **Expected evidence:** A 300-400 word summary that clearly states whether each injection vulnerability was actually validated.

Include:

- authorised application tested;
- target address;
- designated lessons;
- baseline behaviour;
- SQL injection evidence;
- command/input-handling evidence;
- Burp Repeater observations;
- security impact supported by evidence;
- remediation recommendations;
- retesting performed;
- limitations;
- whether an injection vulnerability was actually validated.
#### Suggested structure

Injection testing was conducted against the authorised DVWA/WebGoat training application using the browser and Burp Suite. Baseline requests showed [observation].

SQL injection testing of the parameter [parameter] demonstrated [observed evidence]. Comparison of controlled true and false conditions produced [result].

Command/input testing of [parameter] demonstrated [observed evidence]. The result suggests [supported interpretation].

Recommended remediation includes parameterised queries for database access and server-side allow-list validation / safer APIs for command handling.

Testing was limited to the designated training lessons. Findings therefore describe only behaviour directly observed during the authorised assessment.

### Task 25 – Check Your Work

> **Practical guidance**
> **VM / Tool:** Both VMs for final checks: Kali-Attacker for evidence/application access; Ubuntu-Server for target/container state.
> **Instructions:** Verify the target is still the authorised system, confirm required evidence is saved, and confirm no destructive change was made. Do not perform additional testing just to fill gaps after the assessment is complete.
> **Commands / Inputs:** Kali: `ls -lh ~/lab-evidence/week4`; `curl -I http://<UBUNTU_IP>:<PORT>/`. Ubuntu: `sudo docker ps`.
> **Expected evidence:** Completed checklist with all required items confirmed.

- [ ] Created Week 4 evidence folder
- [ ] Verified DVWA/WebGoat target
- [ ] Confirmed designated injection lesson
- [ ] Captured baseline request
- [ ] Identified user-controlled parameter
- [ ] Tested SQL metacharacter
- [ ] Compared controlled Boolean conditions
- [ ] Used Burp Repeater
- [ ] Tested designated command/input lesson
- [ ] Used only harmless proof-of-concept commands
- [ ] Recorded observable impact
- [ ] Distinguished evidence from assumptions
- [ ] Recommended parameterised queries
- [ ] Recommended server-side input validation
- [ ] Retested where supported
- [ ] Protected passwords/session identifiers
- [ ] Saved required evidence
- [ ] Completed findings summary
- [ ] Remained within the authorised training scope

## Part L – Injection Analysis

### Task 26 – Compare the Same SQL Injection Request Across Security Levels

> **Practical guidance**
> **VM / Tool:** Kali-Attacker browser and Burp Repeater; DVWA only.
> **Instructions:** Capture the same SQL injection request at Low, Medium and High. Duplicate the request into separate Repeater tabs and keep method, parameter and input constant; only the security level should change.
> **Commands / Inputs:** DVWA Security -> Low, Medium, High. Re-send the same request at each level.
> **Expected evidence:** Record response status/length, records returned, error message and the exact validation/control difference observed.

Using DVWA, select one SQL injection request and capture the same request at Low, Medium and High security.

Send each request to Burp Repeater.

| Feature | Low | Medium | High |
| --- | --- | --- | --- |
| HTTP method |  |  |  |
| Parameter |  |  |  |
| Input value |  |  |  |
| Response status |  |  |  |
| Response length |  |  |  |
| Error/message |  |  |  |
| Records returned |  |  |  |
| Validation/control observed |  |  |  |

#### Challenge

Identify the specific technical control that changes. Do not simply state that “High is more secure.”

### Task 27 – Perform Controlled Boolean-Based Response Analysis

> **Practical guidance**
> **VM / Tool:** Kali-Attacker, Burp Repeater.
> **Instructions:** Run a disciplined three-request comparison: baseline, true Boolean condition, false Boolean condition. Use response length and visible content as evidence even if no SQL error is displayed. Do not enumerate tables or extract data.
> **Commands / Inputs:** Examples: baseline normal value; ' OR '1'='1 ; ' OR '1'='2
> **Expected evidence:** Complete the comparison table and explain what the response differences support.

Where the designated SQL injection lesson supports it, compare:

```text
' OR '1'='1
```

with:

```text
' OR '1'='2
```

Do not attempt database extraction.

| Test | Status | Response length | Visible difference | Interpretation |
| --- | --- | --- | --- | --- |
| Baseline |  |  |  |  |
| True condition |  |  |  |  |
| False condition |  |  |  |  |

#### Knowledge Check

If the page displays no SQL error, what evidence could still indicate that the application is evaluating the injected condition?

### Task 28 – Examine Encoded Input Handling

> **Practical guidance**
> **VM / Tool:** Kali-Attacker, Burp Repeater (and Burp Decoder if useful).
> **Instructions:** Compare the same logical input in its normal and URL-encoded forms. Inspect the actual request sent because browsers/Burp may automatically encode query parameters. Change only the encoding, not the meaning of the test.
> **Commands / Inputs:** Examples: single quote ' -> %27 ; space -> %20 or +. Burp Decoder can be used to encode/decode the test string.
> **Expected evidence:** Record original value, encoded value, server response and whether application behaviour changes.

Capture an authorised request in Burp Repeater and compare how the application handles:

- the original input;
- URL-encoded input;
- the same special character represented in encoded form.
For example, compare how a single quote appears before and after URL encoding.

| Test | Value sent | Server response | Application behaviour |
| --- | --- | --- | --- |
| Original |  |  |  |
| Encoded |  |  |  |
| Repeated request |  |  |  |

#### Purpose

Determine whether validation occurs before or after decoding.

Do not claim that encoding bypasses a control unless your evidence demonstrates it.

### Task 29 – Compare Harmless Command Separators

> **Practical guidance**
> **VM / Tool:** Kali-Attacker, Burp Repeater, against the authorised Linux training lesson only.
> **Instructions:** Send a small controlled set of separator variants using the same harmless whoami command. One request per separator is sufficient; stop after collecting the comparison evidence.
> **Commands / Inputs:** 127.0.0.1; whoami   |   127.0.0.1 && whoami   |   127.0.0.1 | whoami
> **Expected evidence:** Record which separators are accepted and whether additional output appears. Do not use shells, file changes, privilege escalation or service-control commands.

Only within the authorised command-injection lesson, test a small controlled set of harmless separators using the same benign command.

Examples:

```text
127.0.0.1; whoami
```

```text
127.0.0.1 && whoami
```

```text
127.0.0.1 | whoami
```

Do not use file modification, shells, privilege escalation, or service-control commands.

| Separator | Accepted? | Additional output? | Response difference |  |
| --- | --- | --- | --- | --- |
| ; |  |  |  |  |
| && |  |  |  |  |
| \| |  |  |  |  |

#### Interpretation

Record which input forms are actually interpreted by the application.

### Task 30 – Correlate Burp Evidence with Server Logs

> **Practical guidance**
> **VM / Tool:** Kali-Attacker for the Burp request and Ubuntu-Server for server-side logs.
> **Instructions:** On Ubuntu, open the relevant application log, then generate one controlled request from Kali through Burp and match the method, path, status and timestamp. Remember that standard web access logs may not record POST bodies.
> **Commands / Inputs:** DVWA: `sudo docker exec -it dvwa bash`; `tail -f /var/log/apache2/access`.log. WebGoat: `sudo docker logs --tail 50 -f webgoat (or the actual container name)`. Use Ctrl+C to stop log follow; exit to leave the container shell.
> **Expected evidence:** Complete the Burp-versus-server-log table and identify information visible in Burp that is absent from the standard log.

For DVWA on Ubuntu:

```bash
sudo docker exec -it dvwa bash
```

Then inspect the web-server log, for example:

```bash
tail -f /var/log/apache2/access.log
```

Generate one controlled injection request from Kali through Burp.

| Field | Burp observation | Server-log observation |
| --- | --- | --- |
| Source IP |  |  |
| Method |  |  |
| Resource/path |  |  |
| Status |  |  |
| User-Agent |  |  |
| Timestamp |  |  |

#### Knowledge Check

Which parts of the request are visible in Burp but not necessarily recorded in the standard access log?

### Task 31 – Produce an Evidence-Based Injection Finding

> **Practical guidance**
> **VM / Tool:** Kali-Attacker evidence folder or report workstation; no further probing is required.
> **Instructions:** Create one professional finding with five sections: observed evidence, supported interpretation, security relevance, recommended remediation and further verification required. Reference specific task evidence rather than restating assumptions.
> **Commands / Inputs:** Optional: `nano ~/lab-evidence/week4/11-advanced-finding`.txt
> **Expected evidence:** A complete evidence-based finding that clearly states what was demonstrated and what was not tested.

Prepare one structured finding.

#### Observed Evidence

State exactly what was seen.

Example:

A modified value in parameter id produced a different response when a true Boolean SQL condition was supplied.

#### Supported Interpretation

Explain only what the evidence reasonably supports.

Example:

The parameter appears to influence server-side SQL query logic.

#### Security Relevance

Explain why the finding matters.

Consider:

- confidentiality;
- integrity;
- application privileges;
- reachability;
- whether authentication is required.
#### Recommended Remediation

For SQL injection:

- parameterised queries;
- prepared statements;
- least-privilege database accounts;
- server-side validation.
For command injection:

- avoid invoking a shell;
- use safer application APIs;
- allow-list expected input;
- execute services with least privilege.
#### Further Verification Required

State what was not demonstrated.

Examples:

- database contents were not extracted;
- administrative privileges were not tested;
- no destructive commands were executed;
- application-wide exposure was not assessed.

## Troubleshooting

## Problem – DVWA/WebGoat Is Not Reachable

On Ubuntu:

```bash
sudo docker ps
```

Check the relevant listening port.

From Kali:

```bash
ip -br addr
ip route
curl -I http://<UBUNTU_IP>:<PORT>/
```

## Problem – Burp Does Not Capture Traffic

Check:

- browser proxy configuration;
- Burp listener;
- Proxy → HTTP history;
- whether the correct browser profile is being used.
## Problem – Modified Request Produces No Difference

Check:

- correct lesson/module;
- correct parameter;
- correct HTTP method;
- whether the parameter was modified in the request actually sent;
- application security level;
- response body as well as status code and response length.
## Problem – Behaviour Differs from the Lab Sheet

Record the behaviour actually observed.

Do not force results to match an example. Application version, security level, database state, browser state and request syntax can change observable behaviour.

## Knowledge Check

1. What is an injection vulnerability?

2. Why should a baseline request be recorded first?

3. What information does Burp Repeater provide that is useful for injection testing?

4. Why can a single quote be useful when assessing SQL input handling?

5. Why compare a true and false SQL condition?

6. What is the difference between an open input field and a confirmed injection vulnerability?

7. Why should a harmless command such as whoami be used instead of a destructive command?

8. What evidence would suggest that operating-system input is being interpreted?

9. Why are parameterised queries effective against SQL injection?

10. Why should input validation be performed on the server side?

11. What is allow-list validation?

12. Why should applications avoid constructing shell commands directly from user input?

13. How does least privilege reduce the impact of injection?

14. Why should remediation be retested?

15. Why should observed evidence and potential impact be reported separately?

## Summary

In this lab, students use the following workflow:

Baseline → Capture → Modify → Compare → Validate → Assess Impact → Remediate → Retest

They use DVWA/WebGoat, the browser, and Burp Repeater to investigate designated SQL injection and command/input-handling lessons.

The main remediation outcomes are:

- SQL injection → parameterised queries / prepared statements
- Command/input handling → safer APIs + strict server-side input validation
- Impact reduction → least privilege
- Reporting → evidence first; vulnerability claims only when validated
