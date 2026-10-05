# Week 4 Lab – Injection Testing

Targets: DVWA / WebGoat

Suggested tools: Browser, Burp Suite Proxy and Repeater

## Estimated Time

Approximately 2 hours for core activities, with additional time for advanced extension tasks.

## Learning Objectives

By the end of this lab, students should be able to:


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

Use Burp Suite Repeater to reproduce and modify requests.

> **Alert:** Do not test university systems, public websites, other students' systems, or any application that has not been explicitly authorised.

## Part A – Prepare the Week 4 Environment

### Task 1 – Create the Week 4 Evidence Folder

1. On **Kali-Attacker**, open a terminal.
2. Create the Week 4 evidence folder:

```bash
mkdir -p ~/lab-evidence/week4
```

3. Change into the folder:

```bash
cd ~/lab-evidence/week4
```

4. Confirm the current path:

```bash
pwd
```

**Expected result:** The working folder should be similar to:

```text
/home/<your-user>/lab-evidence/week4
```

#### Evidence to capture

Take one screenshot showing the Week 4 evidence folder.

### Task 2 – Verify the Authorised Application

Ubuntu-Server terminal for the containers; Kali-Attacker browser/terminal for the connectivity check.

First, identify Ubuntu's current lab IP, then confirm that the assigned DVWA or WebGoat container is running and note its published port. From Kali, test HTTP reachability and then open the application in the browser.

1. **Ubuntu-Server**, check the running containers:

```bash
sudo docker ps
```

If any application isn't running, go back to Lab03 and run the applications. For example, if WebGoat is marked “unhealthy”. Its container is running, but its configured health check is failing. 
You need to remove and recreate it:

```bash
sudo docker stop webgoat
```

```bash
sudo docker rm webgoat
```

Then create it correctly:

```bash
sudo docker run -d --name webgoat \
-p 8081:8080 \
webgoat/webgoat
```

Then check:

```bash
sudo docker ps
```

2. Record the published host port shown in the `PORTS` column. Typical lab examples are:

```text
DVWA      0.0.0.0:80->80/tcp
WebGoat   0.0.0.0:8081->8080/tcp
```

3. On **Kali-Attacker**, test DVWA reachability:

```bash
curl -I http://<UBUNTU_IP>/
```

4. If WebGoat is assigned, use the published host port. A common lab example is:

```bash
curl -I http://<UBUNTU_IP>:8081/WebGoat
```

5. Open the assigned application in the Kali browser.

DVWA example:

```text
http://<UBUNTU_IP>/
```

WebGoat example:

```text
http://<UBUNTU_IP>:8081/WebGoat
```

6. Capture one screenshot showing the authorised application loaded in the browser.

> If the expected container does not exist, do not create a replacement unless the instructor specifically asks you to do so.

Complete the table below:

| Item | Observed value |
| --- | --- |
| Application | DVWA / WebGoat |
| Target IP |  |
| Port |  |
| HTTP status |  |
| Application page loads? | Yes / No |

### Task 3 – Confirm the SQL Injection on DVWA and WebGoat

**DVWA**

After logging in to DVWA with the instructor-provided account (username: admin; password: password), use the left-hand menu.

1. For SQL Injection lesson: select the SQL Injection vulnerability from the left-hand menu. The page normally contains an input field such as User ID
2. The browser address bar will show a path similar to:

```text
http://<DVWA-IP>/vulnerabilities/sqli/
```

3. For Command Injection lesson: select the Command Injection vulnerability from the left-hand menu. The page normally contains an input field such as Enter an IP address
4. The browser address bar will show a path similar to:

```text
http://<DVWA-IP>/vulnerabilities/exec/
```

5. For the DVWA security level: select DVWA Security from the left-hand menu
6. The page shows the currently selected level, such as:
   - Low
   - Medium
   - High
   - Impossible

Complete the table below:

| Item | Observation |
| --- | --- |
| Application |  |
| Lesson/module |  |
| URL/path |  |
| DVWA security level, if applicable |  |

Capture a screenshot of the lesson page before sending modified input for the tasks below.

**WebGoat**

7. Login or create a login to WebGoat. Use the navigation panel on the left. Look under categories related to Injection.
8. Depending on the WebGoat version, the lesson may be named something like:
   - SQL Injection
   - SQL Injection (Intro)
   - SQL Injection (Advanced)
   - Command Injection
   - OS Command Injection

Complete the table below:

| Item | Observation |
| --- | --- |
| Application |  |
| Lesson/module |  |
| URL/path |  |
| Input field observed |  |

## Part B – Establish a Normal Baseline

### Task 4 – Capture a Normal Request

First observe how the application behaves with normal, expected input. Then compare later modified requests against that normal behaviour. A baseline request is a normal request generated by using the application exactly as intended.

1. Use the **Kali-Attacker browser** with the browser's Web Developer Tools.
2. Open the authorised injection lesson.
3. Submit one normal value for User ID that matches the intended field purpose.

SQL Injection baseline example:

```text
1
```

Command Injection baseline example:

```text
127.0.0.1
```

4. Do not modify any other parameter, cookie or token.
5. Observe the normal page response.
6. Record:
   - HTTP method;
   - request path;
   - parameter name;
   - normal parameter value;
   - response status;
   - response length;
   - visible application behaviour.
7. Save or screenshot the baseline response so it can be compared with later modified requests.

**Expected result:** a reproducible normal request exists before injection testing begins.

#### Knowledge Check

Why is it important to record normal behaviour before modifying the request?

### Task 5 – Capture the Request in Burp Suite

The purpose of this task is to make sure the student can see the exact HTTP request generated by the normal baseline action before any injection testing begins.

Kali-Attacker running Burp Suite and the lab browser. Burp acts as an intercepting proxy between the browser and the training application. That means it can record the HTTP requests and responses generated by the browser.

Start Burp, browse to the authorised application through Burp, submit the baseline request and locate it in Proxy -> HTTP history. Use the Burp browser or the instructor-configured browser proxy.

1. On **Kali-Attacker**, start Burp Suite:

```bash
burpsuite
```

2. In Burp, open **Proxy → HTTP history**.
3. You should see requests appearing.

For example:
GET / HTTP/1.1
Host: 192.168.1.100

If requests appear in HTTP history, the browser is successfully using Burp.

If nothing appears, the browser is probably not configured to use the Burp proxy.

7. Locate the request for the injection lesson.
8. Select the request and inspect:
   - method;
   - path;
   - parameters;
   - cookies;
   - response status;
   - response length.
9. Confirm the request belongs to the authorised target IP/host.
10. Capture one screenshot of the baseline request and response.
11. Redact passwords, session IDs and tokens before submitting evidence.

**Expected result:** the baseline HTTP request is visible in Burp and ready to be reused in Repeater.

## Part C – SQL Injection Testing

### Task 6 – Identify the SQL Parameter

Kali-Attacker browser and Burp.

Open the SQL injection lesson, submit a normal value, then inspect the resulting request to identify the exact user-controlled parameter and whether it is sent using GET or POST. Record the actual parameter used by your application version.

1. Use the **Kali-Attacker browser and Burp**.
2. Open the designated SQL injection lesson.
3. Submit a normal value such as:

```text
1
```

4. In Burp **Proxy → HTTP history**, locate the resulting request.
5. Identify exactly where the user input appears.
6. Record the actual parameter name. In some DVWA versions this may be `id`; WebGoat parameter names may differ.
7. Record whether the request uses `GET` or `POST`.
8. Record the normal value and normal response.
9. Do not assume a parameter name from the lab sheet if your application version shows a different one.

**Expected result:** one specific user-controlled SQL parameter is identified from the observed request.

Open the designated SQL injection lesson.

Record the user-controlled input.

Complete the table below:

| Property | Observation |
| --- | --- |
| Parameter name |  |
| GET / POST |  |
| Normal value |  |
| Response |  |

### Task 7 – Test a SQL Metacharacter

Kali-Attacker browser or Burp Repeater.

Change only the identified SQL parameter to a single quote and compare the response with the baseline. Do not change unrelated parameters, cookies or session values.

1. Use the **Kali-Attacker browser or Burp Repeater**.
2. Start from the baseline request captured in Task 6.
3. Change only the identified SQL parameter to:

```text
'
```

4. Keep the method, path, cookies and all unrelated parameters unchanged.
5. Send the request.
6. Compare the response with the baseline.
7. Look for:
   - database error text;
   - application error text;
   - altered content;
   - different record count;
   - different status or response length;
   - no observable difference.
8. Record the actual result rather than forcing the response to match an example.
9. Capture evidence if the response changes.

**Expected result:** the student records whether the quote character changes observable server behaviour. A difference is a lead, not automatic proof of SQL injection.

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

Complete the table below:

| Test | Input | Observation |
| --- | --- | --- |
| Baseline | Normal value |  |
| Quote test | ' |  |

A changed response is a lead for further investigation, not automatic proof of SQL injection.

### Task 8 – Send the SQL Request to Repeater

Kali-Attacker, Burp Suite Repeater.

From Proxy -> HTTP history, send the SQL request to Repeater. Send the untouched baseline request at least once before modifying it so you know the request is reproducible.

1. On **Kali-Attacker**, open Burp **Proxy → HTTP history**.
2. Locate the baseline SQL request.
3. Right-click the request.
4. Select **Send to Repeater**.
5. Open the **Repeater** tab.
6. Before changing anything, click **Send** once.
7. Confirm the baseline response in Repeater matches the original application behaviour.
8. Keep this baseline response available for comparison.
9. Duplicate the Repeater tab if useful, so one copy remains unchanged.

**Expected result:** the original SQL request can be reproduced reliably in Repeater before modification.

In Burp:

1. Locate the request in HTTP history.

2. Right-click.

3. Select Send to Repeater.

4. Open Repeater.

5. Send the original request.

Confirm that the response is reproducible.

### Task 9 – Compare Boolean Conditions

Kali-Attacker, Burp Repeater.

Keep the request identical except for the SQL parameter. Send one condition expected to evaluate true and one expected to evaluate false. Compare status, body content and response length. Do not attempt database extraction.

1. Use **Kali-Attacker → Burp Repeater**.
2. Keep one tab containing the normal baseline request.
3. Duplicate the request into two additional Repeater tabs.
4. In the first modified tab, change only the SQL parameter to the designated true condition:

```text
' OR '1'='1
```

5. Send the request and record the status, response length and visible content.
6. In the second modified tab, use the designated false condition:

```text
' OR '1'='2
```

7. Send the request and record the same fields.
8. Compare all three responses:
   - baseline;
   - true condition;
   - false condition.
9. Do not enumerate tables or extract database data.
10. Capture screenshots showing the comparable request/response evidence.

**Expected result:** the student can explain whether the true and false conditions produce consistently different application responses.

Where supported by the designated lesson, test a controlled condition such as:

```text
' OR '1'='1
```

Then compare with:

```text
' OR '1'='2
```

Complete the table below:

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

Kali-Attacker, Burp Repeater and your evidence notes.

Use the request/response comparison to identify exactly which parameter changes application behaviour. Describe only what your evidence supports, not what you assume the database contains.

1. Use the evidence from Tasks 6–9; no new payload is required.
2. In Burp Repeater, confirm which parameter was changed between the baseline and modified requests.
3. Record the request method and path.
4. Describe the normal behaviour.
5. Describe the modified behaviour.
6. Reference the specific Repeater tab or screenshot that supports the observation.
7. Write a supported interpretation only.
8. Do not state that the database is compromised unless that was actually demonstrated.

**Expected result:** the report clearly identifies the affected parameter and the observable behaviour that supports further SQL injection analysis.

**Complete**

Affected parameter: __________

Request method: __________

Normal behaviour: __________

Modified behaviour: __________

#### Knowledge Check

What evidence supports the conclusion that user input is influencing SQL query behaviour?

## Part D – Command / Input Handling

### Task 11 – Establish the Command Baseline


 Kali-Attacker browser.
 Open the authorised command/input-handling lesson and submit a legitimate value that matches the field's intended purpose. For DVWA Command Injection, use a normal IP address first.
> **Commands / Inputs:** Normal example: 127.0.0.1
> **Expected evidence:** Record parameter name, intended function, normal value and normal output.

1. Use the **Kali-Attacker browser**.
2. Open the authorised command/input-handling lesson.
3. Identify the intended purpose of the input field.
4. For the DVWA Command Injection lesson, submit a normal IP address first:

```text
127.0.0.1
```

5. Observe the normal application output.
6. Record:
   - input parameter;
   - intended function;
   - normal value;
   - normal output.
7. Capture a baseline screenshot.

**Expected result:** a normal command/input request is documented before any separator or command is added.

Open the designated command/input-handling lesson.

Use a normal value, for example:

```text
127.0.0.1
```

Complete the table below:

| Item | Observation |
| --- | --- |
| Input parameter |  |
| Intended purpose |  |
| Normal value |  |
| Normal output |  |

### Task 12 – Capture the Request in Burp

Kali-Attacker, Burp Proxy and Repeater.

Locate the normal command/input request in HTTP history, inspect where the user value appears, then send that request to Repeater without changing it.

1. Use **Kali-Attacker → Burp Proxy**.
2. Submit the normal value from Task 11.
3. Open **Proxy → HTTP history**.
4. Locate the matching request.
5. Inspect where the user-supplied value appears.
6. Record the method, path, parameter name and normal value.
7. Right-click the request and select **Send to Repeater**.
8. Open Repeater and send the request once without modification.
9. Keep the baseline response for comparison.

**Expected result:** the normal command/input request is reproducible in Repeater and the input parameter is known.

Locate the request in Burp and send it to Repeater.

Identify where the user input appears.

Complete the table below:

| Item | Observation |
| --- | --- |
| Method |  |
| Path |  |
| Parameter |  |
| Normal value |  |

### Task 13 – Perform a Harmless Command-Handling Test

Kali-Attacker, Burp Repeater, against the authorised DVWA/WebGoat lesson only.

Change only the input parameter and append one harmless operating-system command. Use the smallest proof necessary and stop if you obtain clear evidence. Never modify files, users, permissions or services.

1. Use **Kali-Attacker → Burp Repeater** against the authorised vulnerable lesson only.
2. Start with the baseline request from Task 12.
3. Change only the input parameter.
4. Use one harmless proof-of-concept input, for example:

```text
127.0.0.1; whoami
```

5. If the designated lesson uses a different supported separator, the instructor may permit:

```text
127.0.0.1 && whoami
```

6. Send the request once.
7. Look for additional output that would indicate the second command was interpreted.
8. Do not modify files, create users, change permissions, stop services, establish shells or alter configuration.
9. Stop after sufficient evidence is obtained.
10. Capture the request and relevant response output.

**Expected result:** the student demonstrates only the minimum harmless evidence required to assess command interpretation.

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

Kali-Attacker, Burp Repeater.

Place the baseline and modified responses side by side and compare status, response length, normal application output and any additional command output.

1. Use **Kali-Attacker → Burp Repeater**.
2. Place the Task 11/12 baseline response and Task 13 modified response side by side.
3. Compare:
   - HTTP status;
   - response length;
   - normal application output;
   - any additional command output;
   - whether a username is returned;
   - any other visible difference.
4. Fill in the comparison table using only observed results.
5. Capture the response section that contains the relevant difference.

**Expected result:** the baseline and modified requests are compared using objective response evidence.

Complete the table below:

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

Kali-Attacker, Burp Repeater.

Repeat the same harmless request once to confirm that the result is reproducible. Do not escalate to additional commands after sufficient proof has been obtained.

1. Use **Kali-Attacker → Burp Repeater**.
2. Resend the exact harmless request used in Task 13 once more.
3. Do not add another command or increase the test.
4. Confirm whether the same additional output is observed again.
5. Record:
   - confirmed observation;
   - affected parameter;
   - evidence of command interpretation.
6. If the result is inconsistent, record it as inconclusive rather than escalating the test.

**Expected result:** the observation is either reproducible or explicitly recorded as inconclusive.

Repeat the same controlled request once.

Record only what is demonstrated.

Confirmed observation:

Affected parameter:

Evidence of command interpretation:

Stop when you have collected sufficient evidence.

## Part E – Understand the Impact

### Task 16 – Separate Evidence from Assumption

Kali-Attacker evidence folder or your report workstation; no new probing is required.

Review the screenshots and Burp responses already collected. For each observation, write one supported interpretation and one claim that would be unsupported by the current evidence.

1. No new probing is required.
2. Use the **Kali-Attacker evidence folder** or your report workstation.
3. Review the screenshots and Burp responses from the SQL and command/input tasks.
4. For each important observation, write:
   - what was directly observed;
   - what the evidence reasonably supports;
   - one claim that is not supported.
5. If useful, create a note file:

```bash
nano ~/lab-evidence/week4/evidence-vs-assumption.txt
```

6. Save the completed table in the Week 4 evidence folder.

**Expected result:** the report separates evidence from assumptions and avoids overstating exploitability.

Complete the table below:

| Observed evidence | Supported interpretation | Unsupported assumption |
| --- | --- | --- |
| True/false SQL responses differ | Input may influence query logic | Entire database is compromised |
| whoami output appears | Input may reach OS command execution | Root access has been obtained |
| Database error appears | Input reaches database-related processing | All SQL injection attacks will succeed |
| Your example |  |  |

### Task 17 – Assess Security Impact

Kali-Attacker evidence folder or report workstation.

Assess the demonstrated issue against confidentiality, integrity, availability and privilege. Base each statement on the observed behaviour and clearly mark impacts that would require further verification.

1. No additional attack request is required.
2. Review the demonstrated SQL and command/input behaviour.
3. For each finding, consider **confidentiality**:
   - was information exposed;
   - or would exposure require further verification?
4. Consider **integrity**:
   - was data or command behaviour changed;
   - or is this only a potential impact?
5. Consider **availability**:
   - was the service disrupted;
   - or is disruption only theoretical?
6. Consider **privilege**:
   - what application/service account is involved;
   - was its privilege level actually observed?
7. Complete the impact table.
8. Clearly label untested impacts as requiring further verification.

**Expected result:** impact statements remain tied to the evidence collected in the lab.

For each demonstrated weakness, consider:

#### Confidentiality

Could the weakness expose information?

#### Integrity

Could it potentially modify information or commands?

#### Availability

Could misuse affect application/service availability?

#### Privilege

What account or application privilege is involved?

Complete the table below:

| Impact area | SQL Injection | Command/Input Handling |
| --- | --- | --- |
| Confidentiality |  |  |
| Integrity |  |  |
| Availability |  |  |
| Privilege considerations |  |  |

Do not claim an impact you did not demonstrate.

## Part F – Remediation

### Task 18 – Understand SQL Injection Remediation

Kali-Attacker browser; use DVWA View Source or the WebGoat lesson explanation where provided.

Review how the vulnerable lesson handles database input and identify where user data is combined with SQL. Compare that pattern with a parameterised/prepared-statement design. Do not edit the target container unless the instructor explicitly asks you to.

1. Use the **Kali-Attacker browser**.
2. If using DVWA, open **View Source** for the SQL Injection lesson where available.
3. If using WebGoat, review the lesson explanation or remediation section.
4. Identify the unsafe pattern where user-controlled data is combined with SQL.
5. Compare the unsafe pattern conceptually with a parameterised query.

Unsafe concept:

```text
query = "SELECT * FROM users WHERE id = '" + user_input + "'"
```

Safer SQL structure:

```sql
SELECT * FROM users WHERE id = ?
```

6. Explain that the value is bound separately from the SQL structure.
7. Where relevant, note a prepared-statement pattern such as `prepare(...)` followed by parameter binding/execution.
8. Do not modify the target container unless specifically instructed.

**Expected result:** the student can explain why parameterised queries prevent user input from changing SQL syntax.

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

Kali-Attacker browser and lesson source/explanation; Ubuntu changes are not required unless specifically instructed.

Identify how the lesson accepts command/input data. Propose a safer design: validate the expected format on the server, reject unexpected metacharacters, avoid invoking a shell, use a safer API and run with least privilege.

1. Use the lesson source/explanation and the evidence collected earlier.
2. Identify the expected format of the command/input field.
3. Propose server-side validation that permits only the required format.
4. Identify unexpected separators/metacharacters that should not be accepted.
5. Explain why constructing a shell command directly from user input is unsafe.
6. Recommend use of a safer application/API function instead of invoking a shell where possible.
7. Recommend least-privilege execution for the application/service.
8. Complete the remediation table for:
   - SQL injection;
   - unsafe shell command construction;
   - weak input validation;
   - excessive privileges.

**Expected result:** remediation is specific to the observed input-handling weakness rather than a generic recommendation.

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

Kali-Attacker browser and Burp Repeater; DVWA security settings or the WebGoat remediated lesson.

Where the training application provides a stronger/remediated implementation, repeat the exact same baseline and modified requests. Change only the control/security level, not the test case.

1. Use **Kali-Attacker browser and Burp Repeater**.
2. Retain the original baseline and modified requests.
3. If DVWA provides a stronger security level, change only the DVWA security level.
4. If WebGoat provides a fixed/remediated stage, open that stage.
5. Resend the exact same baseline request.
6. Resend the exact same modified request.
7. Compare:
   - whether normal input still works;
   - whether modified input is accepted;
   - whether SQL behaviour still changes;
   - whether command output still appears;
   - whether validation now rejects the input.
8. Record the specific control observed.
9. Do not conclude only that “High is secure.”

**Expected result:** the student demonstrates whether the same test behaves differently after a stronger control is applied.

If the training application provides a remediated or higher-security version, repeat the same request.

Complete the table below:

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

Kali-Attacker browser and Burp Repeater; DVWA only.

Set DVWA to Low, Medium and High one at a time. For each level, capture the same function and same test input. Keep separate Repeater tabs and compare only like-for-like requests.

1. This task applies to **DVWA**.
2. Use the **Kali-Attacker browser and Burp Repeater**.
3. Set DVWA to **Low**.
4. Capture and send the chosen injection request with the same test input.
5. Record the response.
6. Set DVWA to **Medium**.
7. Repeat the exact same request and input.
8. Record the response.
9. Set DVWA to **High**.
10. Repeat again without changing the test case.
11. Keep separate Repeater tabs for Low, Medium and High.
12. Compare only like-for-like requests.
13. Record:
   - whether the normal request works;
   - quote handling;
   - Boolean-condition behaviour;
   - validation;
   - error behaviour;
   - other observable controls.
14. Use **Not observed** where no evidence is available.

**Expected result:** the comparison identifies specific technical differences rather than relying on the security-level names.

If DVWA is used, repeat the same controlled workflow at:

- Low;
- Medium;
- High.

Complete the table below:

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

Kali-Attacker evidence folder.

Save screenshots and text evidence using the recommended filenames. Ensure no password, PHPSESSID, WebGoat token or other sensitive value is visible. Check that every file opens before finishing.

1. Use the **Kali-Attacker evidence folder**.
2. Review the evidence required by Tasks 1–21.
3. Save screenshots using the recommended filenames.
4. Confirm no password, session ID, WebGoat token or other sensitive value is visible.
5. List the evidence directory:

```bash
ls -lh ~/lab-evidence/week4
```

6. Open/check each saved file before finishing.
7. Ensure the filenames clearly map to the relevant task.
8. Save the completed findings table and summary in the same folder.

**Expected result:** a complete, readable and clearly named Week 4 evidence set is available for submission.

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

Kali-Attacker or report workstation; use evidence already collected.

For each injection type, summarise the target, parameter, baseline, modified input, observed response, supported interpretation, impact, remediation and retest result. Do not add claims that are not supported by screenshots or Burp evidence.

1. No new probing is required.
2. Use the evidence already collected.
3. If desired, open a text file:

```bash
nano ~/lab-evidence/week4/10-findings-summary.txt
```

4. For **SQL Injection**, record:
   - target;
   - parameter;
   - baseline behaviour;
   - modified input;
   - observed response;
   - supported interpretation;
   - potential impact;
   - remediation;
   - retest result.
5. Repeat the same fields for **Command/Input Handling**.
6. Ensure every important statement can be traced back to a screenshot or Burp result.
7. Do not add unsupported claims.

**Expected result:** the findings table provides a concise evidence-based record for both injection categories.

Complete the table below:

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

## Part K – Injection Analysis

### Task 24 – Compare the Same SQL Injection Request Across Security Levels

Kali-Attacker browser and Burp Repeater; DVWA only.

Capture the same SQL injection request at Low, Medium and High. Duplicate the request into separate Repeater tabs and keep method, parameter and input constant; only the security level should change.

1. This advanced task applies to **DVWA**.
2. Use **Kali-Attacker browser and Burp Repeater**.
3. Select one SQL injection request that can be repeated consistently.
4. Set DVWA to **Low** and capture the request.
5. Send it to Repeater and record status, length, returned records and error/message.
6. Duplicate the same request for later comparison.
7. Set DVWA to **Medium**.
8. Capture the equivalent request with the same input and record the same fields.
9. Repeat at **High**.
10. Keep method, parameter and logical test input consistent.
11. Compare the three results.
12. Identify the specific control or behaviour that changed.
13. Do not write only “High is more secure.”

**Expected result:** the student can point to concrete request/response differences across security levels.

Using DVWA, select one SQL injection request and capture the same request at Low, Medium and High security.

Send each request to Burp Repeater.

Complete the table below:

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

### Task 25 – Perform Controlled Boolean-Based Response Analysis

Kali-Attacker, Burp Repeater.

Run a disciplined three-request comparison: baseline, true Boolean condition, false Boolean condition. Use response length and visible content as evidence even if no SQL error is displayed. Do not enumerate tables or extract data.

1. Use **Kali-Attacker → Burp Repeater**.
2. Prepare three comparable requests:
   - baseline;
   - true Boolean condition;
   - false Boolean condition.
3. Use the instructor-approved examples where supported:

```text
' OR '1'='1
```

```text
' OR '1'='2
```

4. Send the baseline and record status, length and visible content.
5. Send the true condition and record the same fields.
6. Send the false condition and record the same fields.
7. Compare the responses even if no SQL error is displayed.
8. Do not enumerate tables or extract data.
9. Explain only what the response differences support.

**Expected result:** the student can identify Boolean-based response evidence without performing database extraction.

Where the designated SQL injection lesson supports it, compare:

```text
' OR '1'='1
```

with:

```text
' OR '1'='2
```

Do not attempt database extraction.

Complete the table below:

| Test | Status | Response length | Visible difference | Interpretation |
| --- | --- | --- | --- | --- |
| Baseline |  |  |  |  |
| True condition |  |  |  |  |
| False condition |  |  |  |  |

#### Knowledge Check

If the page displays no SQL error, what evidence could still indicate that the application is evaluating the injected condition?

### Task 26 – Examine Encoded Input Handling

Kali-Attacker, Burp Repeater (and Burp Decoder if useful).

Compare the same logical input in its normal and URL-encoded forms. Inspect the actual request sent because browsers/Burp may automatically encode query parameters. Change only the encoding, not the meaning of the test.

1. Use **Kali-Attacker → Burp Repeater**; Burp Decoder may also be used.
2. Start with one authorised request already captured.
3. Record the original test value.
4. Create an encoded representation without changing the logical meaning.

Examples:

```text
'  ->  %27
space  ->  %20
```

5. Inspect the actual request because the browser or Burp may encode values automatically.
6. Send the original request and record the response.
7. Send the encoded equivalent and record the response.
8. Repeat once if required to confirm consistency.
9. Record whether application behaviour changes.
10. Do not claim a validation bypass unless the evidence actually demonstrates one.

**Expected result:** the report documents how the application handles equivalent input representations.

Capture an authorised request in Burp Repeater and compare how the application handles:

- the original input;
- URL-encoded input;
- the same special character represented in encoded form.
For example, compare how a single quote appears before and after URL encoding.

Complete the table below:

| Test | Value sent | Server response | Application behaviour |
| --- | --- | --- | --- |
| Original |  |  |  |
| Encoded |  |  |  |
| Repeated request |  |  |  |

#### Purpose

Determine whether validation occurs before or after decoding.

Do not claim that encoding bypasses a control unless your evidence demonstrates it.

### Task 27 – Compare Harmless Command Separators

Kali-Attacker, Burp Repeater, against the authorised Linux training lesson only.

Send a small controlled set of separator variants using the same harmless whoami command. One request per separator is sufficient; stop after collecting the comparison evidence.

1. Use **Kali-Attacker → Burp Repeater** against the authorised Linux training lesson only.
2. Start from the normal baseline request.
3. Send one controlled request using:

```text
127.0.0.1; whoami
```

4. Record whether additional output appears.
5. Return to the same baseline and test:

```text
127.0.0.1 && whoami
```

6. Record the result.
7. If the instructor-authorised lesson supports it, test:

```text
127.0.0.1 | whoami
```

8. Use one request per separator only.
9. Do not use shells, file changes, privilege escalation or service-control commands.
10. Compare which separators are interpreted.

**Expected result:** the student records separator-specific input-handling behaviour with minimal harmless testing.

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

Complete the table below:

| Separator | Accepted? | Additional output? | Response difference |  |
| --- | --- | --- | --- | --- |
| ; |  |  |  |  |
| && |  |  |  |  |
| \| |  |  |  |  |

#### Interpretation

Record which input forms are actually interpreted by the application.

### Task 28 – Correlate Burp Evidence with Server Logs

Kali-Attacker for the Burp request and Ubuntu-Server for server-side logs.

On Ubuntu, open the relevant application log, then generate one controlled request from Kali through Burp and match the method, path, status and timestamp. Remember that standard web access logs may not record POST bodies.

1. Use **Kali-Attacker** for Burp and **Ubuntu-Server** for server logs.
2. On Ubuntu, identify the running application container:

```bash
sudo docker ps
```

3. For DVWA, enter the container:

```bash
sudo docker exec -it dvwa bash
```

4. Follow the Apache access log:

```bash
tail -f /var/log/apache2/access.log
```

5. Leave the log running.
6. On Kali, send one controlled request through Burp Repeater.
7. Return to Ubuntu and identify the corresponding log entry.
8. Record the source IP, method, resource/path, status, user-agent and timestamp.
9. Stop log following with `Ctrl+C`.
10. Exit the container shell with:

```bash
exit
```

11. For WebGoat, if applicable, use the actual container name and follow Docker logs:

```bash
sudo docker logs --tail 50 -f webgoat
```

12. Compare what Burp shows with what the server log records. Remember that standard access logs may not record POST bodies.

**Expected result:** one client-side request is successfully correlated with its server-side log evidence.

For DVWA on Ubuntu:

```bash
sudo docker exec -it dvwa bash
```

Then inspect the web-server log, for example:

```bash
tail -f /var/log/apache2/access.log
```

Generate one controlled injection request from Kali through Burp.

Complete the table below:

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

## Part L – Findings Summary

### Task 29 – Write a 400–500 Word Findings Summary

Kali-Attacker evidence folder or report workstation.

Write 300-400 words that connect the evidence into a concise professional finding summary. Reference the baseline, Burp comparison, demonstrated impact, remediation, retest and limitations.

1. Use your Week 4 evidence and findings table.
2. Create or open the summary file:

```bash
nano ~/lab-evidence/week4/10-findings-summary.txt
```

3. Write approximately **300–400 words**.
4. Begin with the authorised target/application and designated lessons.
5. Summarise the baseline behaviour.
6. Summarise the SQL injection evidence.
7. Summarise the command/input-handling evidence.
8. Reference the Burp Repeater comparisons.
9. State only the impacts demonstrated or clearly label potential impacts.
10. Include remediation:
    - parameterised queries/prepared statements;
    - server-side validation/safer APIs;
    - least privilege where relevant.
11. State whether a retest was performed and what changed.
12. Include limitations.
13. State clearly whether an injection vulnerability was actually validated.
14. Save the file.

**Expected result:** a concise professional findings summary links evidence, interpretation, impact and remediation.

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

### Task 30 – Check Your Work

Both VMs for final checks: Kali-Attacker for evidence/application access; Ubuntu-Server for target/container state.

Verify the target is still the authorised system, confirm required evidence is saved, and confirm no destructive change was made. Do not perform additional testing just to fill gaps after the assessment is complete.

1. Use **Kali-Attacker** for the evidence/application checks and **Ubuntu-Server** for the target/container check.
2. On Kali, verify the evidence folder:

```bash
ls -lh ~/lab-evidence/week4
```

3. Confirm the authorised application still responds:

```bash
curl -I http://<UBUNTU_IP>:<PORT>/
```

4. On Ubuntu, confirm the assigned container is still in the expected state:

```bash
sudo docker ps
```

5. Work through the checklist one item at a time.
6. Confirm no destructive action was performed.
7. Confirm sensitive values were removed from screenshots.
8. Do not perform additional testing merely to fill missing checklist items after the authorised assessment is complete.

**Expected result:** all required evidence and reporting tasks are complete and the target remains within the authorised state.

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


### Task 31 – Produce an Evidence-Based Injection Finding

Kali-Attacker evidence folder or report workstation; no further probing is required.

Create one professional finding with five sections: observed evidence, supported interpretation, security relevance, recommended remediation and further verification required. Reference specific task evidence rather than restating assumptions.

1. No additional probing is required.
2. Use the **Kali-Attacker evidence folder** or report workstation.
3. If desired, create the advanced finding file:

```bash
nano ~/lab-evidence/week4/11-advanced-finding.txt
```

4. Write the finding under five headings:

   **Observed Evidence**
   - state exactly what was seen;
   - reference a specific task/screenshot/request.

   **Supported Interpretation**
   - explain what the evidence reasonably indicates;
   - avoid broader assumptions.

   **Security Relevance**
   - discuss confidentiality, integrity, privilege, reachability and authentication only where relevant.

   **Recommended Remediation**
   - SQL injection: parameterised queries/prepared statements, least privilege and validation;
   - command injection: avoid shell invocation, use safer APIs, allow-list input and least privilege.

   **Further Verification Required**
   - state what was not tested or demonstrated.

5. Explicitly state that database extraction, privilege escalation, destructive commands and application-wide exposure were not performed unless the instructor authorised and evidence actually demonstrates otherwise.
6. Save the finding in the Week 4 evidence folder.

**Expected result:** a professional finding clearly distinguishes demonstrated evidence, interpretation, remediation and remaining uncertainty.

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
