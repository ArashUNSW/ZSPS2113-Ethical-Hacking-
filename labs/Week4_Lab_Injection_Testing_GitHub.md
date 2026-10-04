# Week 4 Lab – Injection Testing

**Course:** ZSPS2113 Ethical Hacking and Penetration Testing  
**Tutorial / Stage:** Injection Testing  
**Targets:** DVWA / WebGoat  
**Suggested tools:** Browser, Burp Suite Proxy and Repeater  
**Focus:** SQL injection and command/input handling in designated lessons  
**Evidence / outcome:** Demonstrate the issue in the authorised lab, explain impact, and recommend parameterised queries and input validation.

---

## Learning Objectives

By the end of this lab, students should be able to:

1. Identify user-controlled parameters that may be vulnerable to injection.
2. Establish a normal baseline request before changing input.
3. Use Burp Repeater to safely reproduce and compare requests.
4. Demonstrate SQL injection in an authorised DVWA/WebGoat lesson.
5. Demonstrate unsafe command/input handling using harmless lab-only commands.
6. Distinguish observed evidence from assumptions about exploitability.
7. Explain the security impact of injection vulnerabilities.
8. Recommend appropriate remediation, including parameterised queries and server-side input validation.
9. Record concise, reproducible evidence suitable for a penetration-testing report.

---

## Authorised Scope and Safety Rules

> Perform all activities only against the supplied DVWA/WebGoat lab targets and only within the designated injection lessons.

- Do **not** test external websites, university systems, public IP addresses, or other students' systems.
- Use only harmless proof-of-concept inputs.
- Do not use destructive SQL statements such as `DROP`, `DELETE`, `UPDATE`, or `INSERT`.
- Do not attempt reverse shells, persistence, privilege escalation, or destructive operating-system commands.
- Do not retrieve unrelated sensitive information.
- Stop testing if the application becomes unstable and record what occurred.

---

# Part A – Prepare the Injection-Testing Environment

## Task 1 – Confirm the Authorised Target

Record the application you will test.

| Item | Observation |
|---|---|
| Target application | DVWA / WebGoat |
| Target URL | |
| Authorised lesson/module | |
| Your testing workstation | Kali / supplied lab VM |
| Date/time | |

**Evidence to capture:** Screenshot showing the authorised DVWA/WebGoat page.

---

## Task 2 – Confirm Application Availability

Open the target application in the browser and verify that it loads normally.

Record:

- whether the login page loads;
- whether you can authenticate using the supplied lab credentials;
- whether the designated injection lesson is accessible.

**Expected outcome:** The target is reachable and ready for authorised testing.

---

## Task 3 – Start Burp Suite

Launch Burp Suite:

```bash
burpsuite
```

Confirm that the browser is configured to send traffic through Burp.

**Evidence to capture:** Burp Proxy or HTTP history showing a request to the authorised target.

---

## Task 4 – Capture a Baseline Request

Before attempting any injection, submit a normal value in the selected lesson.

Examples:

- a normal user ID in a SQL injection lesson;
- a normal IP address or hostname in a command/input-handling lesson.

In Burp, record:

| Item | Observation |
|---|---|
| HTTP method | |
| Request path | |
| Parameter name | |
| Normal parameter value | |
| Response status | |
| Normal application response | |

**Key point:** Always establish what normal behaviour looks like before modifying input.

---

# Part B – SQL Injection

## Task 5 – Locate the SQL Injection Lesson

Open the designated SQL injection lesson in DVWA or WebGoat.

For DVWA, use only the supplied **SQL Injection** module.  
For WebGoat, use only the designated SQL injection lesson provided by the course.

Record the user-controlled parameter that is sent to the server.

---

## Task 6 – Test Normal Input

Submit a normal value such as:

```text
1
```

Record the result.

| Test | Input | Result |
|---|---|---|
| Baseline | `1` | |

**Evidence to capture:** Normal application response.

---

## Task 7 – Test a SQL Metacharacter

Submit a single quote:

```text
'
```

Observe whether the response changes.

Look for:

- an SQL/database error;
- a different page response;
- missing or additional results;
- a generic server error;
- no visible change.

| Test | Input | Observation |
|---|---|---|
| Quote test | `'` | |

**Interpretation:** A changed response may be a lead for further testing, but it is not yet sufficient to claim SQL injection.

---

## Task 8 – Send the Request to Burp Repeater

In **Proxy → HTTP history**:

1. locate the SQL injection request;
2. right-click it;
3. select **Send to Repeater**;
4. open **Repeater**.

Repeat the normal request first and confirm that the response is consistent.

---

## Task 9 – Perform a Controlled Boolean Test

In the authorised lab lesson, test a basic Boolean condition such as:

```text
' OR '1'='1
```

If the lesson requires a slightly different syntax, use the syntax demonstrated by the lesson.

Record:

| Item | Observation |
|---|---|
| Original parameter | |
| Modified parameter | |
| HTTP status | |
| Response length | |
| Visible response difference | |

**Evidence to capture:** Repeater request and response showing the changed application behaviour.

---

## Task 10 – Compare True and False Conditions

Where supported by the lesson, compare two controlled conditions.

Example:

```text
' OR '1'='1
```

and:

```text
' OR '1'='2
```

Record whether the responses differ.

| Test | Condition | Response behaviour |
|---|---|---|
| A | True condition | |
| B | False condition | |

**Interpretation:** Consistent differences between true and false conditions provide stronger evidence that the parameter is being interpreted by a database query.

---

## Task 11 – Identify the Injection Point

Based on your evidence, identify:

- the vulnerable parameter;
- where the parameter appears in the HTTP request;
- whether the request uses GET or POST;
- what application behaviour changed.

Write one concise observation:

> **Observation:** The parameter `__________` produced different server responses when SQL control characters/conditions were supplied.

---

## Task 12 – Record SQL Injection Evidence

Complete the evidence table.

| Evidence item | Result |
|---|---|
| Target lesson | |
| HTTP method | |
| Vulnerable parameter | |
| Baseline input | |
| Controlled test input | |
| Response difference | |
| Screenshot/reference | |

---

## Task 13 – Explain the Potential Impact

Based only on what you observed, identify potential impact.

Examples may include:

- bypassing intended query logic;
- viewing records not intended for the user;
- altering authentication decisions;
- exposing application/database information.

Do **not** claim an impact that you did not demonstrate.

Write:

> **Confirmed observation:**  
> **Possible security impact:**  
> **Further verification required:**

---

# Part C – Command / Input Handling

## Task 14 – Locate the Designated Command/Input Lesson

Open the authorised command injection or input-handling lesson in DVWA/WebGoat.

For DVWA, this may be the supplied **Command Injection** module.  
For WebGoat, use only the designated lesson assigned by the instructor.

Record the input field and its intended purpose.

---

## Task 15 – Establish Normal Behaviour

Enter a normal value expected by the application.

Example:

```text
127.0.0.1
```

Record the response.

| Item | Observation |
|---|---|
| Parameter | |
| Normal value | |
| Intended function | |
| Normal response | |

---

## Task 16 – Capture the Request in Burp

Locate the request in **Proxy → HTTP history** and send it to **Repeater**.

Identify the parameter that contains the user-supplied value.

---

## Task 17 – Test a Harmless Command Separator

Only in the authorised command-injection lesson, use a harmless proof-of-concept command such as:

```text
127.0.0.1; whoami
```

or, where appropriate:

```text
127.0.0.1 && whoami
```

Use only the syntax supported by the supplied lab environment.

**Do not use commands that modify files, users, services, permissions, or system configuration.**

---

## Task 18 – Observe the Response

Look for evidence that the second command was interpreted.

Examples:

- an operating-system username;
- additional command output;
- a changed response compared with the baseline.

| Test | Input | Response |
|---|---|---|
| Baseline | Normal value | |
| Controlled separator test | Lab-safe input | |

**Evidence to capture:** Burp Repeater request/response or browser output.

---

## Task 19 – Confirm the Input-Handling Weakness

Repeat the request once to confirm that the observed behaviour is reproducible.

Do not continue escalating the test after you have sufficient evidence.

Write:

> **Confirmed observation:**  
> **Input parameter:**  
> **Evidence of command interpretation:**

---

## Task 20 – Consider Input Validation

Review the expected format of the field.

For example, if the application expects an IPv4 address, valid input might contain only:

```text
0-9 and .
```

Discuss:

- what characters should be permitted;
- what characters should be rejected;
- whether allow-list validation would be appropriate;
- why client-side validation alone is insufficient.

---

# Part D – Remediation

## Task 21 – SQL Injection Remediation

Explain why building SQL queries through string concatenation is unsafe.

Unsafe concept:

```text
query = "SELECT ... WHERE id = '" + user_input + "'"
```

Preferred approach:

```text
SELECT ... WHERE id = ?
```

with the user-supplied value passed separately as a bound parameter.

**Key remediation:** Use **parameterised queries / prepared statements**.

---

## Task 22 – Explain Parameterised Queries

Complete the statement:

> Parameterised queries reduce SQL injection risk because ____________________________________________.

Your answer should distinguish **SQL code** from **user-supplied data**.

---

## Task 23 – Input-Validation Remediation

For command/input handling, recommend controls such as:

- server-side allow-list validation;
- strict data type, length and format checks;
- rejecting unexpected separators/metacharacters;
- avoiding direct construction of operating-system commands;
- using safer application APIs instead of invoking a shell;
- least-privilege execution accounts.

---

## Task 24 – Match Vulnerability to Remediation

| Finding | Primary remediation |
|---|---|
| SQL injection | |
| Command injection | |
| Weak input validation | |
| Excessive application privileges | |

---

## Task 25 – Retest After Remediation

If the lab lesson provides a remediated or higher-security version, repeat the same controlled request.

Compare:

| Test | Before remediation | After remediation |
|---|---|---|
| Normal input works | | |
| SQL/command metacharacters accepted | | |
| Unexpected behaviour occurs | | |
| Server returns safe handling/error | | |

**Expected outcome:** Normal functionality remains available, while maliciously structured input is no longer interpreted as executable SQL or an operating-system command.

---

# Part E – Analysis and Reporting

## Task 26 – Separate Observation from Conclusion

For each finding, write three statements.

### Finding 1 – SQL Injection

**Confirmed observation:**  
**Possible concern:**  
**Further verification required:**

### Finding 2 – Command/Input Handling

**Confirmed observation:**  
**Possible concern:**  
**Further verification required:**

---

## Task 27 – Assess Impact

For each issue, consider:

- **Confidentiality:** Could unauthorised information be exposed?
- **Integrity:** Could information or commands be altered?
- **Availability:** Could the weakness disrupt the application?
- **Privilege:** What permissions does the affected application/service have?

Do not claim an impact that was not demonstrated in the lab.

---

## Task 28 – Write a Short Finding

Use the following structure:

### Finding: Injection Weakness

**Affected target:**  
**Affected parameter:**  
**Evidence:**  
**Observed behaviour:**  
**Potential impact:**  
**Recommended remediation:**  
**Retest required:** Yes / No

---

## Task 29 – Findings Summary

Complete the summary.

| Item | Result |
|---|---|
| Target application | |
| SQL injection lesson tested | |
| SQL parameter tested | |
| SQL injection demonstrated? | |
| Command/input lesson tested | |
| Input parameter tested | |
| Unsafe command handling demonstrated? | |
| Main SQL remediation | Parameterised query / prepared statement |
| Main input remediation | Server-side allow-list validation / safer API |
| Evidence files/screenshots recorded | |

---

## Task 30 – Final Validation and Cleanup

Before finishing:

- confirm that testing remained within DVWA/WebGoat;
- remove any sensitive values from screenshots;
- ensure Burp project/history does not contain unrelated credentials;
- stop any temporary lab services if instructed;
- save required evidence;
- confirm that no destructive actions were performed.

---

# Optional Challenge – Compare DVWA Security Levels

If instructed, repeat the **same designated injection workflow** at different DVWA security levels.

| Security level | Same input accepted? | Behaviour observed | Control identified |
|---|---|---|---|
| Low | | | |
| Medium | | | |
| High | | | |

**Important:** Do not infer that a control exists only because the security level is labelled “Medium” or “High”. Record only what you can observe.

---

# Suggested Evidence Checklist

Students should retain evidence of:

1. authorised target and lesson;
2. normal baseline request;
3. SQL injection request and response;
4. true/false comparison where applicable;
5. command/input-handling request and response;
6. Burp Repeater evidence;
7. remediation explanation;
8. before/after comparison where available;
9. final findings summary.

---

# Key Takeaways

- Injection testing starts with a **normal baseline**.
- Burp Repeater helps students reproduce and compare requests safely.
- A changed response is **evidence to investigate**, not automatic proof of full compromise.
- SQL injection is primarily addressed through **parameterised queries / prepared statements**.
- Command injection is reduced by **avoiding shell execution**, using safer APIs and applying strict **server-side input validation**.
- Testing must remain within the **authorised DVWA/WebGoat lesson and scope**.
