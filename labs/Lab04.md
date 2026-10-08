# Week 4 Lab – Injection Testing

Targets: DVWA / WebGoat

Suggested tools: Browser, Burp Suite Proxy and Repeater

## Estimated Time

Approximately 2 hours for core activities, with additional time for advanced extension tasks.

## Learning Objectives

By the end of this lab, students should be able to:
- Prepare and verify an authorised injection-testing environment using DVWA or WebGoat and maintain appropriate evidence of the test setup.
- Capture and analyse baseline HTTP requests and responses using the browser, Burp Suite Proxy, and Burp Repeater.
- Identify user-controlled parameters and conduct controlled SQL injection tests using response comparison and observable evidence.
- Assess command/input-handling weaknesses using harmless proof-of-concept testing and compare modified responses against a known baseline.
- Differentiate observed evidence from supported interpretation and unsupported assumptions when analysing injection-related findings.
- Assess the demonstrated security impact of SQL injection and command/input-handling weaknesses in terms of confidentiality, integrity, availability, and privilege.

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

### Task 5 – Capture the Baseline Request in Burp Suite

The purpose of this task is to confirm that the lab browser is sending traffic through Burp Suite and to capture the normal baseline request before any injection testing begins.

Burp Suite acts as an intercepting proxy between the browser and the authorised training application. It allows you to inspect the HTTP request generated by the browser and the corresponding server response.

1. On **Kali-Attacker**, start Burp Suite:

```bash
burpsuite
```

2. Use either:
   - the Burp built-in browser, or
   - the instructor-configured browser that is already using the Burp proxy.

3. In Burp, open:

   Proxy → HTTP history

4. Browse to the authorised DVWA or WebGoat injection lesson.
5. Submit the same normal baseline value used in Task 4.

For SQL Injection, for example:

```bash
1
```

For Command Injection, for example:

```bash
127.0.0.1
```

6. Return to Proxy → HTTP history and locate the request generated by that submission.
7. Confirm that the request belongs to the authorised target by checking the Host/IP address.

For example:

Host: 192.168.1.100

8. Select the request and inspect the following:
   - HTTP method;
   - request path;
   - parameters;
   - cookies;
   - response status;
   - response length.

9. Confirm that the normal input value is visible somewhere in the request.
10. Capture one screenshot showing the selected baseline request and its response.
11. Before submitting evidence, redact or hide:
   - passwords;
   - session identifiers;
   - authentication tokens;
   - other sensitive values.

**Evidence to capture**
Capture one screenshot showing:
   - the authorised target;
   - the selected request in Burp;
   - the request method and path;
   - the normal input value;
   - the response status;
   - the response length.

**Expected result**
The normal baseline HTTP request is visible in Burp Suite, belongs to the authorised target, and is ready for later comparison or reuse in Repeater.

## Part C – SQL Injection Testing

### Task 6 – Identify the User-Controlled SQL Parameter

This task identifies the exact request parameter that carries the user-supplied SQL lesson input.

Do not assume the parameter name. Record the value shown in your DVWA or WebGoat request.

1. Using the **Kali-Attacker browser**, open the instructor-designated SQL Injection lesson.

2. Submit a normal value such as:

```text
1
```

3. In Burp Suite, open:

```text
Proxy → HTTP history
```

4. Locate the request generated by the SQL lesson submission.

5. Select the request and determine where the value `1` appears.

   Depending on the application, the value may appear:

   - in the URL query string for a `GET` request; or
   - in the request body for a `POST` request.

6. Record the exact parameter name associated with the user input.

   For example, DVWA may show something similar to:

```text
id=1
```

   In that case:

```text
Parameter name: id
Normal value: 1
```

   WebGoat may use a different parameter name, so record what your own environment shows.

7. Record whether the request uses:

```text
GET
```

   or:

```text
POST
```

8. Record the normal server response, including:

   - response status;
   - visible application behaviour;
   - response length, if required.

9. Do not modify the value yet. This task is only for identifying the input location and establishing the normal request structure.

### Complete the table

| **Property** | **Observed value** |
|---|---|
| Parameter name | |
| Request method | GET / POST |
| Normal value | |
| Response status | |
| Response length | |
| Normal application behaviour | |

### Expected result

One specific user-controlled SQL parameter is identified from the observed request, together with its request method and normal response behaviour.

### Task 7 – Test a SQL Metacharacter

The purpose of this task is to determine whether a single SQL metacharacter changes the application's observable behaviour when it is supplied to the user-controlled parameter identified in Task 6.

A single quote (`'`) is commonly used as a simple diagnostic input because it may affect SQL parsing if the application places user input directly into a query. However, a changed response is only an indication for further investigation and does **not** by itself confirm SQL injection.

1. Use the **Kali-Attacker browser or Burp Repeater**.

2. Start from the normal baseline request captured in Task 6.

3. Change **only** the identified SQL parameter value to:

```text
'
```

4. Keep all other request elements unchanged, including:

   - HTTP method;
   - request path;
   - cookies;
   - session values;
   - tokens;
   - unrelated parameters.

5. Send the modified request.

6. Compare the response with the baseline request from Task 6.

7. Look for any observable difference, such as:

   - database error text;
   - application error text;
   - altered page content;
   - different number of records;
   - different HTTP status code;
   - different response length;
   - no observable difference.

8. Record the **actual result** from your environment. Do not force the result to match an example.

9. Capture evidence if the response differs from the baseline.

### Evidence to capture

If the response changes, capture one screenshot showing:

- the modified parameter value;
- the response status;
- the response length;
- the relevant response content or error message.

Do not expose passwords, session identifiers or authentication tokens.

### Complete the table

| **Test** | **Input** | **Response status** | **Response length** | **Observed behaviour** |
|---|---|---|---|---|
| Baseline | Normal value from Task 6 | | | |
| Quote test | `'` | | | |

### Expected result

The student records whether the single quote changes the observable server or application behaviour.

A changed response is a **lead for further investigation**, not automatic proof of SQL injection. A normal or unchanged response also does not prove that SQL injection is impossible.

### Task 8 – Send the SQL Request to Repeater

The purpose of this task is to confirm that the baseline SQL request captured in Burp can be reproduced reliably in **Repeater** before any further modification.

Repeater allows you to resend the same HTTP request manually and compare the server response after making controlled changes. Before editing anything, first confirm that the original request produces the same normal behaviour observed in Tasks 4–6.

1. On **Kali-Attacker**, open Burp Suite.

2. Go to:

```text
Proxy → HTTP history
```

3. Locate the normal baseline SQL request captured in Task 6.

4. Confirm that the request belongs to the authorised DVWA or WebGoat target.

5. Right-click the request and select:

```text
Send to Repeater
```

6. Open the **Repeater** tab.

7. Before changing any parameter, click Send

8. Inspect the response and confirm that it matches the original baseline behaviour.

Compare:

- HTTP status code;
- response length;
- visible response content;
- redirect behaviour, if present.

9. Keep the baseline request unchanged for comparison.

10. If useful, duplicate the Repeater tab so that:

- one tab remains as the unchanged baseline;
- the second tab can be used for later modified requests.

11. Do not change cookies, session values, tokens, or unrelated parameters unless a later task specifically instructs you to do so.

### Complete the table

| **Item** | **Observed value** |
|---|---|
| Target host/IP | |
| Request method | |
| Request path | |
| Baseline parameter | |
| Baseline value | |
| Response status | |
| Response length | |
| Baseline behaviour reproduced? | Yes / No |

### Evidence to capture

Capture one screenshot showing:

- the SQL request in Repeater;
- the unchanged baseline parameter and value;
- the response status;
- the response length;
- enough response content to show that the baseline behaviour was reproduced.

Redact passwords, session identifiers, authentication tokens, and other sensitive values before submitting evidence.

### Expected result

The original SQL baseline request can be resent successfully in Burp Repeater and produces the same normal response as the request observed in the browser or Proxy HTTP history.

This confirms that the request is reproducible and provides a stable baseline for controlled modifications in later tasks.

### Task 9 – Compare Boolean Conditions

The purpose of this task is to determine whether the authorised SQL Injection lesson produces different responses when the same user-controlled parameter is changed between a condition expected to evaluate **true** and one expected to evaluate **false**.

Keep the request identical in every other respect. This is a controlled comparison task only. Do **not** enumerate database objects or extract database contents.

1. On **Kali-Attacker**, open **Burp Suite → Repeater**.

2. Keep one Repeater tab containing the unchanged baseline request from Task 8.

3. Duplicate the baseline request into two additional Repeater tabs (right-click on the Request editor and choose Send to Repeater), name them accordingly:

   - one **baseline** request;
   - one **true-condition** request;
   - one **false-condition** request.

4. In the **true-condition** tab, change only the identified SQL parameter to the instructor-designated true condition:

```text
' OR '1'='1
```

5. Send the request.

6. Record:

   - HTTP status code;
   - response length;
   - visible response content;
   - record count, if clearly shown;
   - error or application message;
   - whether the behaviour differs from the baseline.

7. In the **false-condition** tab, change only the same SQL parameter to the instructor-designated false condition:

```text
' OR '1'='2
```

8. Send the request and record the same observations.

9. Compare the three responses:

   - baseline;
   - true condition;
   - false condition.

10. Keep the following unchanged unless the lesson specifically requires otherwise:

   - HTTP method;
   - request path;
   - cookies;
   - session values;
   - tokens;
   - unrelated parameters.

11. Confirm that all requests belong to the authorised DVWA or WebGoat target.

12. Do **not** attempt to enumerate tables, columns, users, credentials, or other database content.

### Complete the table

| **Feature** | **Baseline** | **True condition** | **False condition** |
|---|---|---|---|
| Input value | Normal value from Task 8 | `' OR '1'='1` | `' OR '1'='2` |
| HTTP status | | | |
| Response length | | | |
| Records/content | | | |
| Error/message | | | |
| Behaviour changed? | | | |

### How to interpret the comparison

Look for a **consistent difference** between the true and false conditions.

Examples of observable differences may include:

- different response length;
- different page content;
- different number of records;
- different application message;
- different HTTP status;
- no observable difference.

A difference between the true and false conditions may support further investigation, but it is **not by itself proof of SQL injection**. Likewise, no visible difference does not prove that SQL injection is impossible.

### Evidence to capture

Capture screenshots showing:

- the baseline request and response;
- the true-condition request and response;
- the false-condition request and response;
- comparable status and response-length evidence.

Redact passwords, session identifiers, authentication tokens, and other sensitive values before submitting evidence.

### Expected result

The student can explain whether the baseline, true condition, and false condition produce consistently different application responses while changing only the authorised SQL parameter.

The task should demonstrate controlled comparison and evidence-based interpretation without database enumeration or data extraction.

### Task 10 – Identify the Injection Point

The purpose of this task is to use the evidence collected in Tasks 6–9 to identify the exact user-controlled parameter whose modification changes the application's observable behaviour.

No new payload is required. This task is about **evidence-based interpretation**, not further exploitation.

1. Review the evidence collected in Tasks 6–9.

2. In **Burp Repeater**, identify the parameter that was changed between:

   - the baseline request;
   - the single-quote test;
   - the true-condition request;
   - the false-condition request.

3. Record the exact parameter name.

4. Record the HTTP request method used by the affected request:

```text
GET
```

or:

```text
POST
```

5. Record the request path.

6. Describe the **normal baseline behaviour** observed when the parameter contained the expected value.

7. Describe the **modified behaviour** observed when the same parameter was changed during Tasks 7 and 9.

8. Reference the specific evidence that supports your conclusion, such as:

   - Repeater tab name or number;
   - screenshot filename;
   - response status;
   - response length;
   - visible content difference;
   - error or application message.

9. Write only a **supported interpretation** based on the observed request/response differences.

10. Do not claim that:

   - the database has been compromised;
   - data has been extracted;
   - authentication has been bypassed;
   - arbitrary SQL execution has been achieved;

   unless those outcomes were explicitly demonstrated in an authorised task.

### Complete the table

| **Item** | **Observed value** |
|---|---|
| Affected parameter | |
| Request method | |
| Request path | |
| Baseline value | |
| Normal behaviour | |
| Modified behaviour | |
| Evidence reference | |
| Supported interpretation | |

### Example of a supported interpretation

> Changing the value of the identified parameter produced a consistent difference between the baseline, true-condition, and false-condition responses. This indicates that the parameter influences server-side application behaviour and warrants further authorised SQL injection analysis.

Do not write:

> The database is compromised.

unless that conclusion has actually been demonstrated.

### Evidence to capture

Use evidence already collected in Tasks 6–9. No new payload is required.

Your evidence should show, where available:

- the affected parameter;
- the baseline request;
- the modified request;
- the relevant response difference;
- response status and length;
- visible application behaviour.

Redact passwords, session identifiers, authentication tokens, and other sensitive values before submitting evidence.

### Expected result

The student clearly identifies the user-controlled parameter associated with the observed behaviour change and supports the conclusion with specific request/response evidence.

The result should identify an **injection point for further authorised investigation**, not make unsupported claims about database compromise.

### Knowledge Check

**What evidence supports the conclusion that user input is influencing SQL query behaviour?**

A strong answer should refer to a reproducible difference between the baseline and modified requests while all unrelated request elements remain unchanged. Relevant evidence may include different response content, response length, record count, error messages, or consistent differences between true and false Boolean conditions.

## Part D – Command / Input Handling

### Task 11 – Establish the Command Baseline

The purpose of this task is to document the application's **normal behaviour** before any command separator or modified input is introduced.

A baseline gives you a known-good request and response that can be compared with later command-injection tests. Use only a legitimate value that matches the intended purpose of the field.

1. On **Kali-Attacker**, open the authorised command/input-handling lesson in the browser.

2. Identify the purpose of the input field.

   For example, in the DVWA **Command Injection** lesson, the field is intended to accept an IP address.

3. Submit a normal value that matches the intended field purpose.

   For DVWA Command Injection, use:

```text
127.0.0.1
```

4. Do **not** add command separators, shell operators, extra commands, or unrelated input.

5. Observe the normal application response.

6. Record:

   - input parameter name;
   - intended function of the field;
   - normal value submitted;
   - normal visible output;
   - response status, if available;
   - response length, if available.

7. Capture one screenshot showing the normal input and resulting application behaviour.

### Complete the table

| **Item** | **Observed value** |
|---|---|
| Input parameter | |
| Intended purpose | |
| Normal value | `127.0.0.1` |
| Response status | |
| Response length | |
| Normal output | |

### Evidence to capture

Capture one screenshot showing:

- the authorised lesson;
- the normal input value;
- the resulting application output.

If Browser Developer Tools or Burp Suite is available, you may also record the corresponding response status and response length.

Do not expose passwords, session identifiers, authentication tokens, or other sensitive values in submitted evidence.

### Expected result

Document a reproducible normal request and response before testing any command separator or modified command input.

Use this baseline to compare later requests and determine whether modified input causes a meaningful change in application behaviour.

### Task 12 – Capture the Request in Burp

The purpose of this task is to capture the normal command/input request from Task 11, identify exactly where the user-controlled value appears, and confirm that the request can be reproduced in **Burp Repeater** before any modification is made.

1. On **Kali-Attacker**, open Burp Suite and use the lab browser configured to send traffic through Burp.

2. In the authorised command/input-handling lesson, submit the same normal value used in Task 11.

   For DVWA Command Injection, for example:

```text
127.0.0.1
```

3. In Burp Suite, open:

```text
Proxy → HTTP history
```

4. Locate the request generated by the normal submission.

5. Confirm that the request belongs to the authorised DVWA or WebGoat target.

6. Select the request and identify where the user-supplied value appears.

   Depending on the application, the value may appear:

   - in the URL query string; or
   - in the request body.

7. Record:

   - HTTP method;
   - request path;
   - parameter name;
   - normal parameter value;
   - response status;
   - response length.

8. Right-click on the Request text field and select:

```text
Send to Repeater
```

9. The orange light will blink on Repeater; it means the request is being sent. Open the **Repeater** tab.

10. Before changing anything, click:

```text
Send
```

11. Confirm that the response in Repeater matches the normal application behaviour observed in Task 11.

12. Keep this request and response unchanged as the **command/input baseline** for later comparison.

13. Do not change cookies, session values, tokens, request method, path, or unrelated parameters unless a later task specifically instructs you to do so.

### Complete the table

| **Item** | **Observed value** |
|---|---|
| HTTP method | |
| Request path | |
| Parameter name | |
| Normal value | `127.0.0.1` |
| Response status | |
| Response length | |
| Baseline behaviour reproduced in Repeater? | Yes / No |

### Evidence to capture

Capture one screenshot showing:

- the normal command/input request in Burp;
- the identified parameter and normal value;
- the response status;
- the response length;
- enough response content to confirm the baseline behaviour.

Redact passwords, session identifiers, authentication tokens, and other sensitive values before submitting evidence.

### Expected result

The normal command/input request is visible in Burp, the user-controlled parameter is clearly identified, and the same request can be reproduced successfully in Repeater without modification.

This provides a stable baseline for controlled comparison in the later command-injection tasks.

### Task 13 – Perform a Harmless Command-Handling Test

The purpose of this task is to determine whether the authorised training application interprets additional operating-system command input after the normal baseline value.

Use only the designated DVWA/WebGoat command-injection lesson. Apply the **minimum harmless proof necessary**, change only the identified user-controlled parameter, and stop once clear evidence is obtained.

1. On **Kali-Attacker**, open **Burp Suite → Repeater**.

2. Start from the unchanged command/input baseline request created in Task 12.

3. Confirm that the request belongs to the authorised DVWA or WebGoat lesson.

4. Change **only** the identified input parameter.

5. Use one harmless proof-of-concept value, for example:

```text
127.0.0.1; whoami
```

6. If the instructor-designated lesson requires a different supported command separator, the instructor may instead permit:

```text
127.0.0.1 && whoami
```

7. Keep all other request elements unchanged, including:

   - HTTP method;
   - request path;
   - cookies;
   - session values;
   - tokens;
   - unrelated parameters.

8. Send the modified request **once**.

9. Compare the response with the normal baseline from Task 12.

10. Look only for the minimum observable evidence needed to determine whether the second command was interpreted, such as:

   - additional command output;
   - a changed response body;
   - a changed response length;
   - a changed application message;
   - no observable difference.

11. Record the actual result from your environment. Do not assume command execution occurred unless the response provides supporting evidence.

12. Stop testing once sufficient evidence has been obtained.

13. Do **not** use commands that:

   - modify or delete files;
   - create or modify users;
   - change permissions;
   - stop or restart services;
   - alter system configuration;
   - establish reverse/bind shells;
   - download or execute additional payloads;
   - attempt privilege escalation.

### Complete the table

| **Item** | **Observed value** |
|---|---|
| Parameter name | |
| Baseline value | `127.0.0.1` |
| Test value | `127.0.0.1; whoami` or instructor-approved equivalent |
| HTTP method | |
| Request path | |
| Response status | |
| Response length | |
| Additional output observed? | Yes / No |
| Observed application behaviour | |
| Supported interpretation | |

### Evidence to capture

Capture one screenshot showing:

- the authorised target and lesson;
- the modified input parameter;
- the response status;
- the response length;
- the relevant response content that supports your observation.

Redact passwords, session identifiers, authentication tokens, and other sensitive values before submitting evidence.

### Expected result

The student records whether the application appears to interpret the appended harmless operating-system command.

Clear additional output may support the conclusion that the input is influencing command execution in the authorised lesson. However, the student should report only what the evidence demonstrates and should stop once minimal proof has been obtained.

### Task 14 – Compare Baseline and Modified Input

The purpose of this task is to compare the normal command/input baseline with the modified request from Task 13 using objective response evidence.

Use only the authorised DVWA/WebGoat lesson and record what is actually observed. Do not infer command execution solely from a changed response unless the returned content supports that conclusion.

1. On **Kali-Attacker**, open **Burp Suite → Repeater**.

2. Keep the following responses available for comparison:

   - the normal baseline response from Tasks 11/12;
   - the modified response from Task 13.

3. Compare the two responses side by side where possible.

4. Record the following for both responses:

   - HTTP status code;
   - response length;
   - normal application output;
   - any additional command output;
   - whether a username or other command result is returned;
   - any other visible difference.

5. If Burp shows a measurable response-length difference, record the actual values.

6. If additional command output is visible, record only the minimum relevant evidence needed to support the observation.

7. If there is no observable difference, record:

```text
No observable difference
```

8. Do not change the request again in this task. This task is for comparison only.

9. Capture evidence showing the relevant difference between the baseline and modified responses.

### Complete the table

| **Feature** | **Normal input** | **Modified input** |
|---|---|---|
| HTTP status | | |
| Response length | | |
| Normal application output | | |
| Additional command output | | |
| Username returned? | Yes / No | Yes / No |
| Other visible difference | | |

### Supported interpretation

After completing the table, write one short evidence-based statement.

For example:

> The modified request produced additional output that was not present in the baseline response, while the request method, path, cookies, and unrelated parameters remained unchanged.

Or, if nothing changed:

> No observable response difference was identified between the baseline and modified requests.

Do not claim broader system compromise unless you explicitly demonstrate it.

### Evidence requirement

Capture one Burp Repeater screenshot showing:

- the baseline response;
- the modified response;
- the relevant response status and length;
- the response section containing the observed difference.

Redact passwords, session identifiers, authentication tokens, and other sensitive values before submitting evidence.

### Expected result

Compare the baseline and modified requests using objective response evidence, allowing the student to explain whether the modified input caused a reproducible change in application behaviour.

### Task 15 – Confirm Reproducibility

The purpose of this task is to confirm whether the harmless command-handling result observed in Task 13 can be reproduced consistently using the **same request**.

This task is for confirmation only. Do not introduce additional commands, separators, payloads, or escalation once sufficient evidence has already been obtained.

1. On **Kali-Attacker**, open **Burp Suite → Repeater**.

2. Locate the exact harmless modified request used in Task 13.

3. Confirm that the request still belongs to the authorised DVWA or WebGoat lesson.

4. Resend the **same request once more** without changing:

   - the input parameter;
   - HTTP method;
   - request path;
   - cookies;
   - session values;
   - tokens;
   - unrelated parameters.

5. Compare the new response with the modified response observed in Task 13.

6. Check whether the same relevant evidence appears again, such as:

   - the same additional command output;
   - the same username or command result;
   - the same response pattern;
   - a similar response length;
   - the same application behaviour.

7. Record the result as one of the following with example:

```text
Reproducible (same)
```

```text
Not reproducible (different)
```

or:

```text
Inconclusive (unclear)
```

8. If the result is inconsistent, record it as **inconclusive**. Do not escalate the test by introducing additional commands.

9. Stop once sufficient confirmation evidence has been collected.

### Complete the table

| **Item** | **Observed value** |
|---|---|
| Affected parameter | |
| Test value used | |
| First observed result | |
| Repeated result | |
| Same additional output observed? | Yes / No |
| Response status | |
| Response length | |
| Reproducibility outcome | Reproducible / Not reproducible / Inconclusive |

### Confirmed observation

Write one short evidence-based statement.

For example:

> Repeating the same harmless request produced the same additional output as Task 13, indicating that the observed behaviour is reproducible.

Or, if the result differs:

> Repeating the same harmless request did not produce the same output, so the result is recorded as inconclusive.

### Evidence requirement

Capture one Burp Repeater screenshot showing:

- the repeated request;
- the affected parameter;
- the relevant response output;
- the response status and length.

Redact passwords, session identifiers, authentication tokens, and other sensitive values before submitting evidence.

### Expected result

The student determines whether the observed command-handling behaviour is reproducible using the same controlled request.

Record the result as **reproducible**, **not reproducible**, or **inconclusive**, without escalating beyond the minimum harmless proof already used.

## Part E – Understand the Impact

### Task 16 – Separate Evidence from Assumption

The purpose of this task is to distinguish **what was directly observed** from **what the evidence reasonably supports** and from **claims that would go beyond the available evidence**.

No new probing is required. Use only the screenshots, Burp requests/responses, and notes already collected during the SQL Injection and command/input tasks.

1. Use the **Kali-Attacker evidence folder** or your report workstation.

2. Review the evidence collected in the earlier tasks, including:

   - baseline requests and responses;
   - SQL metacharacter and Boolean-condition comparisons;
   - identified SQL parameter evidence;
   - command/input baseline evidence;
   - harmless command-handling test results;
   - reproducibility evidence.

3. For each important observation, record three separate statements:

   - **Observed evidence** – what you directly saw in the request, response, browser, or Burp Suite;
   - **Supported interpretation** – what the evidence reasonably indicates;
   - **Unsupported assumption** – a stronger claim that the current evidence does not prove.

4. Keep each statement concise and evidence-based.

5. Do not convert a behavioural difference into a broader compromise claim unless that outcome was explicitly demonstrated.

6. If useful, create a note file:

```bash
nano ~/lab-evidence/week4/evidence-vs-assumption.txt
```

7. Save the completed notes or table in the Week 4 evidence folder.

### Complete the table

| **Observed evidence** | **Supported interpretation** | **Unsupported assumption** |
|---|---|---|
| True and false SQL conditions produce different responses | The tested input may influence server-side query behaviour | The entire database is compromised |
| `whoami` output appears in the response | The tested input may reach operating-system command execution | Root or administrator access has been obtained |
| A database-related error message appears | The input reaches database-related processing | Every SQL injection technique will succeed |
| Repeating the same harmless command test produces the same output | The observed command-handling behaviour is reproducible | Persistent system compromise has been achieved |
| Your example | | |

### Guidance

A strong entry should follow this pattern:

```text
Observed evidence → Supported interpretation → Unsupported assumption
```

For example:

```text
Response length changed between true and false conditions
→ The application handled the two inputs differently
→ The database contents can be extracted
```

The first statement is directly observable.

The second is a reasonable interpretation.

The third would require additional authorised testing and is therefore unsupported by the current evidence.

### Evidence requirement

No new probing is required.

Use the evidence already collected in previous tasks and reference the relevant screenshot, Burp Repeater tab, or saved response where appropriate.

### Expected result

The student clearly separates direct observations from supported interpretations and unsupported claims.

The final report should avoid overstating exploitability, access level, or system compromise beyond what the collected evidence actually demonstrates.

### Task 17 – Assess Security Impact

The purpose of this task is to assess the demonstrated SQL Injection and command/input-handling findings against **confidentiality, integrity, availability, and privilege** using only the evidence collected in the lab.

No additional attack request is required. Do not claim an impact that was not demonstrated. Where an impact is plausible but untested, record it as **Requires further verification**.

1. Use the **Kali-Attacker evidence folder** or your report workstation.

2. Review the evidence already collected for:

   - SQL Injection testing;
   - Boolean-condition comparison;
   - identified SQL injection point;
   - command/input baseline;
   - harmless command-handling test;
   - reproducibility evidence.

3. For each finding, assess **Confidentiality**.

   Ask:

   - Was information actually exposed?
   - What information was visible?
   - Was only application behaviour observed?
   - Would broader data exposure require further verification?

4. Assess **Integrity**.

   Ask:

   - Was application data actually modified?
   - Was command behaviour altered?
   - Was only command interpretation demonstrated?
   - Would data modification require further verification?

5. Assess **Availability**.

   Ask:

   - Was the application or service disrupted?
   - Did the test affect normal availability?
   - Is service disruption only a theoretical possibility?

6. Assess **Privilege**.

   Ask:

   - What application, database, or operating-system account appears to be involved?
   - Was the account identity actually observed?
   - Was the privilege level demonstrated?
   - Would elevated privilege require further verification?

7. Complete the impact table using only evidence from the authorised lab.

8. Use one of the following where appropriate:

```text
Observed
```

```text
Not observed
```

```text
Requires further verification
```

9. Do not convert a potential impact into a confirmed finding unless the lab evidence directly supports it.

### Complete the table

| **Impact area** | **SQL Injection** | **Command/Input Handling** |
|---|---|---|
| Confidentiality | | |
| Integrity | | |
| Availability | | |
| Privilege considerations | | |

### Guidance for each impact area

#### Confidentiality

Record whether the demonstrated weakness exposed information.

Example:

> Different SQL responses were observed, but unauthorised data disclosure was not demonstrated. Broader confidentiality impact requires further verification.

#### Integrity

Record whether the test actually changed data or command behaviour.

Example:

> The command/input test demonstrated altered command handling, but modification of files or application data was not performed.

#### Availability

Record whether the service was disrupted.

Example:

> No service interruption was observed during testing. Availability impact was not demonstrated.

#### Privilege

Record only the account or privilege information actually observed.

Example:

> The `whoami` response identified the account under which the command executed. Elevated privileges were not demonstrated.

If no account identity was observed, record:

```text
Requires further verification
```

### Evidence requirement

No new probing is required.

Base each impact statement on the evidence already collected and, where useful, reference the relevant:

- screenshot;
- Burp Repeater tab;
- response body;
- response status;
- response length;
- command output.

### Expected result

The student produces impact statements that are directly tied to observed evidence and clearly distinguishes confirmed impact from potential impact that would require further verification.

Do not claim confidentiality loss, data modification, service disruption, or elevated privilege unless those outcomes were actually demonstrated in the authorised lab.

## Part F – Remediation

### Task 18 – Understand SQL Injection Remediation

The purpose of this task is to explain **why parameterised queries / prepared statements reduce SQL injection risk** by separating SQL code from user-supplied data.

No new attack request is required. This is a remediation and secure-coding review task.

1. On **Kali-Attacker**, open the authorised SQL Injection lesson in the browser.

2. If using **DVWA**, open the lesson's **View Source** option where available.

3. If using **WebGoat**, review the lesson explanation, solution, or remediation guidance provided for the SQL Injection exercise.

4. Identify the unsafe coding pattern in which user-controlled input is combined directly with an SQL statement.

   Conceptually, an unsafe pattern may look like:

```text
query = "SELECT * FROM users WHERE id = '" + user_input + "'"
```

5. Explain why this is unsafe.

   When user input is concatenated directly into the SQL statement, specially crafted input may alter the intended SQL syntax rather than being treated only as data.

6. Compare this with a parameterised query.

   A safer SQL structure is:

```sql
SELECT * FROM users WHERE id = ?
```

7. Explain that the placeholder represents data that is supplied separately from the SQL statement.

8. Where the application language or lesson supports it, identify the equivalent prepared-statement pattern, for example:

```text
prepare(...)
bind parameter(s)
execute(...)
```

9. Record the relevant remediation concept shown by the lesson.

10. Do **not** edit the DVWA/WebGoat container or application source unless the instructor explicitly asks you to do so.

### Complete the table

| **Item** | **Observation / explanation** |
|---|---|
| Vulnerable lesson reviewed | |
| Unsafe input-handling pattern | |
| Why the pattern is unsafe | |
| Safer SQL structure | |
| Main remediation control | Parameterised queries / prepared statements |
| Additional secure-coding control observed, if any | |

### Complete the statement

> Parameterised queries reduce SQL injection risk because ________________________________.

A strong answer should explain that **the SQL statement structure is defined separately from user-supplied values, so the input is treated as data rather than executable SQL syntax**.

### Knowledge Check

1. What is the security problem with concatenating user input directly into an SQL query?
2. What is the purpose of the `?` placeholder in a parameterised query?
3. Why is parameter binding safer than building a query through string concatenation?
4. Does parameterisation remove the need for all other input validation? Explain briefly.

### Expected result

The student can explain, using the authorised lesson as context, why parameterised queries / prepared statements are a primary control against SQL injection.

The student should distinguish between:

```text
SQL code + concatenated user input
```

and:

```text
SQL statement structure + separately bound data
```

No modification of the target application is required for this task.

### Task 19 – Identify Command/Input Remediation

The purpose of this task is to identify remediation controls for the command/input-handling weakness observed in the authorised lab and to relate those controls to the specific behaviour demonstrated in earlier tasks.

No additional probing is required. Use the lesson source/explanation and the evidence already collected. Ubuntu configuration changes are not required unless the instructor explicitly asks you to make them.

1. Review the authorised command/input-handling lesson and the evidence collected in Tasks 11–15.

2. Identify the **expected format** of the user input.

   For example, if the field is intended to accept an IP address, the application should validate that the submitted value matches a valid IP-address format.

3. Recommend **strict server-side allow-list validation** so that only the expected data type and format are accepted.

4. Identify unexpected characters or separators that should not be accepted when they are not required for the field's intended purpose.

5. Explain why directly constructing a shell command from user-controlled input is unsafe.

   User input that is inserted into a shell command may be interpreted as part of the command syntax rather than as ordinary data.

6. Recommend avoiding shell invocation where possible.

   Prefer a safer application/library API that performs the required function directly without passing user input through a command shell.

7. Recommend appropriate input constraints, such as:

   - expected data type;
   - expected format;
   - allow-listed characters;
   - sensible length limits;
   - rejection of unexpected separators/metacharacters.

8. Recommend **least-privilege execution** for the application or service account.

   The application should run only with the permissions required for its intended function.

9. Complete the remediation table using recommendations that are specific to the weaknesses demonstrated in the lab.

### Complete the table

| **Weakness** | **Recommended control** |
|---|---|
| SQL injection | Parameterised queries / prepared statements |
| Unsafe shell command construction | Avoid building shell commands from user input; use a safer application/API function where possible |
| Weak input validation | Strict server-side allow-list validation, expected format/type checks, and sensible length limits |
| Excessive application privileges | Run the application/service using a least-privilege account |

### Additional remediation considerations

| **Control area** | **Recommended approach** |
|---|---|
| Input format | Accept only the format required by the field |
| Unexpected metacharacters | Reject characters that are not valid for the intended input |
| Shell invocation | Avoid invoking a shell when a direct API/library call is available |
| Error handling | Return controlled application errors without exposing unnecessary internal details |
| Privilege | Grant only the minimum permissions required by the service |

### Supported explanation

A strong remediation statement should connect the control directly to the observed weakness.

For example:

> The command/input weakness can be reduced by validating the expected input format on the server and avoiding the construction of shell commands from user-controlled data. Where possible, the application should use a dedicated API or library function instead of a shell and should run under a least-privilege service account.

### Knowledge Check

1. Why is server-side validation more important than relying only on browser-side validation?
2. Why is allow-list validation generally preferable when the expected input format is well defined?
3. Why is direct shell-command construction from user input dangerous?
4. How does using a safer application/API function reduce command-injection risk?
5. Why does least privilege reduce the potential impact of a command-injection weakness?

### Expected result

The student identifies remediation controls that are directly related to the observed command/input-handling weakness rather than providing only generic security recommendations.

The final recommendations should address:

- safe handling of SQL input;
- safe handling of command/input data;
- strict server-side validation;
- avoidance of unnecessary shell execution;
- least-privilege application/service accounts.

## Part G – Retest the Control

### Task 20 – Retest After Remediation

The purpose of this task is to determine whether the **same baseline and modified requests behave differently after a stronger or remediated control is applied**.

The comparison must be fair: keep the test case the same and change only the relevant security control or remediated application stage.

1. On **Kali-Attacker**, use the browser and **Burp Suite → Repeater**.

2. Keep copies of the original requests used earlier in the lab, including:

   - the normal baseline request;
   - the SQL modified request, where applicable;
   - the command/input modified request, where applicable.

3. Do **not** change the test payload, request method, path, cookies, or unrelated parameters unless the remediated lesson itself requires a different request structure.

4. If using **DVWA**:

   - change only the DVWA security level to the instructor-designated stronger level;
   - confirm the new security level is active before retesting.

5. If using **WebGoat**:

   - open the instructor-designated remediated/fixed stage or lesson;
   - confirm you are testing the intended remediated implementation.

6. Resend the **same normal baseline request**.

7. Confirm whether the legitimate input still works as intended.

8. Resend the **same modified request** used before remediation.

9. Compare the before/after responses, including:

   - HTTP status code;
   - response length;
   - normal application behaviour;
   - whether the modified input is accepted;
   - whether SQL-related behaviour still changes;
   - whether command output still appears;
   - whether the application rejects or sanitises the modified input;
   - any new validation/error message.

10. Record the **specific control or behavioural difference** demonstrated by the retest.

11. If the response changes, describe exactly how it changed.

12. If no meaningful difference is observed, record:

```text
No observable remediation effect
```

13. Do not conclude that a higher security level is secure merely because the label is **High**.

### Complete the table

| **Behaviour** | **Before remediation** | **After remediation** |
|---|---|---|
| Normal input works | | |
| Modified input accepted | | |
| SQL behaviour changes | | |
| Command output appears | | |
| Input safely rejected | | |
| Response status | | |
| Response length | | |
| Validation/error message | | |

### Interpretation

Write one short evidence-based statement describing the control demonstrated by the retest.

For example:

> The same modified input that changed application behaviour at the lower security level was rejected after the stronger control was applied, while the normal input continued to work.

Or:

> The application accepted the normal value after remediation, but the modified input no longer produced the previous SQL/command-handling behaviour.

If no difference is observed:

> The same modified request produced no meaningful behavioural difference after the security control was changed, so the remediation effect was not demonstrated by this test.

Do **not** simply write:

```text
High is secure.
```

Instead describe the specific observed control, for example:

> The modified input was rejected because the application applied stricter server-side validation.

### Evidence requirement

Capture screenshots showing:

- the stronger/remediated security setting or lesson;
- the unchanged baseline request;
- the unchanged modified request;
- the relevant before/after response difference;
- response status and response length where available.

Redact passwords, session identifiers, authentication tokens, and other sensitive values before submitting evidence.

### Expected result

The student determines whether the same previously tested request behaves differently after a stronger or remediated control is applied.

The conclusion must identify the **specific observed control or behavioural change**, rather than relying only on the name of the security level.

## Part H – Compare DVWA Security Levels

### Task 21 – Compare the Same Injection Test

The purpose of this task is to compare how the **same DVWA injection test behaves at Low, Medium, and High security levels**.

To make the comparison valid, keep the function, parameter, test input, request method, and unrelated request elements the same. Change only the DVWA security level.

> **Scope:** This task applies to **DVWA only**.

1. On **Kali-Attacker**, use the browser and **Burp Suite → Repeater**.

2. Choose one authorised DVWA injection function and one controlled test input to use for the entire comparison.

3. Confirm the parameter to be tested and keep it the same at every level.

4. Set DVWA to:

```text
Low
```

5. Capture or reuse the chosen request in Burp Repeater.

6. Send the same normal baseline request and the same controlled modified request.

7. Record the observed response behaviour.

8. Keep the Low-security request in a separate Repeater tab.

9. Change DVWA to:

```text
Medium
```

10. Repeat the **same function, same parameter, and same test input**.

11. Record the response and keep the Medium-security request in a separate Repeater tab.

12. Change DVWA to:

```text
High
```

13. Repeat the exact same workflow again.

14. Keep separate Repeater tabs for:

   - Low;
   - Medium;
   - High.

15. Compare only **like-for-like requests**.

16. Record:

   - whether the same parameter was tested;
   - whether the normal request still works;
   - how the single-quote input is handled;
   - whether the Boolean-condition test changes output;
   - whether input validation is observed;
   - whether error behaviour changes;
   - whether response status or length changes;
   - any other observable control.

17. Use:

```text
Not observed
```

where no supporting evidence is available.

18. Do not infer that a control exists simply because the security level is called **High**.

### Complete the table

| **Feature** | **Low** | **Medium** | **High** |
|---|---|---|---|
| Same parameter tested | | | |
| Normal request works | | | |
| Quote accepted | | | |
| Boolean test changes output | | | |
| Input validation observed | | | |
| Error behaviour | | | |
| Response status | | | |
| Response length | | | |
| Other observable control | | | |

### Evidence to capture

Capture comparable evidence for each security level showing:

- the DVWA security level;
- the same request path and parameter;
- the same controlled test input;
- the relevant response status and length;
- the response content or error behaviour.

Redact passwords, session identifiers, authentication tokens, and other sensitive values before submitting evidence.

### Interpretation

Write one short comparison statement based only on observed evidence.

For example:

> The same SQL test produced different response behaviour across Low, Medium, and High security levels. The higher level introduced stricter input handling, while the baseline request continued to function normally.

If no meaningful difference is observed:

> No clear technical difference was demonstrated by this test across the three security levels.

Do not simply write:

```text
High is secure.
```

Instead identify the exact control or behaviour that changed.

### Expected result

The student identifies specific technical differences in how DVWA handles the same injection test at Low, Medium, and High security levels.

The comparison should be based on **like-for-like requests and observed evidence**, not on assumptions derived from the security-level names.

## Part I – Evidence Collection

### Task 22 – Save Required Evidence

The purpose of this task is to organise, verify, and finalise the Week 4 evidence collected during Tasks 1–21.

Your evidence set should be complete, readable, clearly named, and free of sensitive information before submission.

1. On **Kali-Attacker**, open the Week 4 evidence folder.

2. Review the evidence required by Tasks 1–21.

3. Save each screenshot or text file using a clear filename that maps to the relevant task.

4. Before saving or submitting evidence, confirm that it does **not** expose:

   - passwords;
   - `PHPSESSID` values;
   - WebGoat tokens;
   - authentication tokens;
   - session identifiers;
   - other sensitive values.

5. List the contents of the evidence directory:

```bash
ls -lh ~/lab-evidence/week4
```

6. Confirm that all expected files are present.

7. Open or preview each saved file to confirm that:

   - the file is not corrupted;
   - the screenshot is readable;
   - the relevant request/response detail is visible;
   - sensitive values are redacted;
   - the filename matches the task.

8. Save the completed findings table and summary in the same folder.

9. If useful, verify the file types with:

```bash
file ~/lab-evidence/week4/*
```

10. Do not delete original evidence until you have confirmed that the final submission set is complete.

### Recommended evidence

1. Authorised application running.
2. Designated injection lesson.
3. Baseline request.
4. SQL quote test.
5. SQL Boolean comparison.
6. Burp Repeater SQL evidence.
7. Command/input baseline.
8. Controlled command-handling evidence.
9. Remediation/retest evidence.
10. Completed findings table and summary.

### Recommended filenames

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

### Final evidence checklist

| **Check** | **Complete?** |
|---|---|
| All required files are present | Yes / No |
| Filenames clearly map to tasks | Yes / No |
| Screenshots are readable | Yes / No |
| Requests/responses are visible where required | Yes / No |
| Passwords are redacted | Yes / No |
| Session IDs/tokens are redacted | Yes / No |
| Findings table is included | Yes / No |
| Findings summary is included | Yes / No |
| Every saved file opens successfully | Yes / No |

### Expected result

A complete, readable, and clearly organised Week 4 evidence set is available for submission.

The evidence should allow the assessor to trace each screenshot or text file back to the relevant task while protecting passwords, session identifiers, tokens, and other sensitive values.

## Part J – Findings Table

### Task 23 – Produce Evidence-Based Findings

The purpose of this task is to produce a concise, evidence-based summary of the two injection categories tested in the lab:

- SQL Injection;
- Command/Input Handling.

No new probing is required. Use only the screenshots, Burp requests/responses, notes, remediation observations, and retest evidence already collected in Tasks 1–22.

1. Use **Kali-Attacker** or your report workstation.

2. If useful, create or open the findings summary file:

```bash
nano ~/lab-evidence/week4/10-findings-summary.txt
```

3. Review the evidence already collected for **SQL Injection**.

4. Record the following:

   - authorised target;
   - affected parameter;
   - baseline behaviour;
   - modified input used;
   - observed response;
   - supported interpretation;
   - potential impact;
   - recommended remediation;
   - retest result.

5. Repeat the same process for **Command/Input Handling**.

6. Ensure every important statement can be traced back to evidence such as:

   - a screenshot;
   - Burp Proxy HTTP history;
   - Burp Repeater request/response;
   - response status;
   - response length;
   - visible application output;
   - remediation/retest evidence.

7. Distinguish carefully between:

   - what was directly observed;
   - what the evidence reasonably supports;
   - what would require further verification.

8. If an impact was not demonstrated, write:

```text
Requires further verification
```

or:

```text
Not observed
```

as appropriate.

9. Do not add claims such as database compromise, privilege escalation, persistent access, or service disruption unless those outcomes were actually demonstrated in the authorised lab.

### Complete the table

| **Area** | **SQL Injection** | **Command/Input Handling** |
|---|---|---|
| Target | | |
| Parameter | | |
| Baseline behaviour | | |
| Modified input | | |
| Observed response | | |
| Supported interpretation | | |
| Potential impact | | |
| Remediation | | |
| Retest result | | |
| Evidence reference | | |

### Guidance for each field

#### Target

Record the authorised application and target host used in the lab.

Example:

```text
DVWA on authorised Ubuntu-Server target
```

#### Parameter

Record the exact user-controlled parameter identified in Burp.

Example:

```text
id
```

or the actual command/input parameter observed in your environment.

#### Baseline behaviour

Describe the normal response before the input was modified.

#### Modified input

Record only the controlled input used in the authorised task.

#### Observed response

Describe what actually changed, such as:

- response content;
- response length;
- response status;
- record count;
- application message;
- additional command output.

#### Supported interpretation

State only what the evidence supports.

Example:

> The tested parameter appears to influence server-side query behaviour because the true and false conditions produced consistently different responses.

#### Potential impact

Use the impact analysis from Task 17.

If the impact was not directly demonstrated, label it clearly as requiring further verification.

#### Remediation

Use the specific control identified in Tasks 18–19.

Examples include:

- parameterised queries / prepared statements;
- strict server-side allow-list validation;
- avoiding shell-command construction from user input;
- safer APIs;
- least-privilege service accounts.

#### Retest result

Record whether the same test behaved differently after the stronger or remediated control was applied.

#### Evidence reference

Reference the relevant screenshot filename, Repeater tab, or saved evidence file.

### Example evidence-based finding

> The `id` parameter produced different responses when tested with true and false Boolean conditions while the request method, path, cookies, and unrelated parameters remained unchanged. This supports the conclusion that the parameter may influence server-side SQL query behaviour. Broader database access was not tested and requires further verification. The recommended control is parameterised queries / prepared statements.

### Expected result

The student produces a concise findings table for both SQL Injection and Command/Input Handling in which every important statement is traceable to collected evidence.

The final findings should clearly separate:

```text
Observed evidence
→ Supported interpretation
→ Potential impact
→ Remediation
→ Retest result
```

Unsupported claims must not be included.

## Part K – Findings Summary

### Task 24 – Write a 400–500 Word Findings Summary

Kali-Attacker evidence folder or report workstation.

Write 400-500 words that connect the evidence into a concise professional finding summary. Reference the baseline, Burp comparison, demonstrated impact, remediation, retest and limitations.

1. Use your Week 4 evidence and findings table.
2. Create or open the summary file:

```bash
nano ~/lab-evidence/week4/10-findings-summary.txt
```

3. Write approximately **400–500 words**.
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

## Part L – Injection Analysis (Advanced Lab, Optional)

### Task 25 – Compare the Same SQL Injection Request Across Security Levels (Advanced Lab, Optional)

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

### Task 26 – Perform Controlled Boolean-Based Response Analysis (Advanced Lab, Optional)

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

### Task 27 – Examine Encoded Input Handling (Advanced Lab, Optional)

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

### Task 28 – Compare Harmless Command Separators (Advanced Lab, Optional)

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

### Task 29 – Correlate Burp Evidence with Server Logs (Advanced Lab, Optional)

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
