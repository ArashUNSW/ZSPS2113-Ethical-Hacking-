# Week 5 Lab – Cross-Site Scripting (XSS) Testing

**Tutorial / Stage:** Cross-site scripting  
**Targets:** DVWA / WebGoat  
**Suggested tools:** Browser Developer Tools, Burp Suite Proxy and Repeater  

## Estimated time ## 
Approximately 2 hours for core activities, with additional time for optional advanced activities.

---

## Learning Objectives

By the end of this lab, students should be able to:

- Prepare and verify an authorised DVWA/WebGoat environment for XSS testing.
- Capture normal HTTP requests and establish a reproducible baseline before modifying input.
- Identify user-controlled input and determine where that input appears in the resulting HTML/DOM.
- Perform controlled tests for reflected, stored, and DOM-oriented XSS/input-handling weaknesses.
- Use Browser DevTools and Burp Suite to trace data from input source to output context.
- Distinguish observed browser behaviour from unsupported assumptions about exploitability.
- Assess the demonstrated impact of an XSS weakness using collected evidence.
- Recommend appropriate controls including context-aware output encoding, input validation, safe DOM APIs, and Content Security Policy (CSP).
- Retest the same input after a stronger/remediated control is applied.
- Produce an evidence-based XSS finding that identifies the affected input/output context and appropriate mitigation.

---

## Scenario

You are continuing an authorised penetration-testing exercise.

This week focuses on Cross-Site Scripting and unsafe browser-side input handling, particularly:

- reflected XSS;
- stored XSS;
- DOM-oriented input handling;
- identifying user-controlled input;
- identifying the corresponding HTML/DOM output context;
- assessing demonstrated impact;
- recommending suitable remediation.

**Kali-Attacker** is the testing workstation.

**Ubuntu-Server** hosts the authorised DVWA/WebGoat training applications.

Use **Browser Developer Tools** to inspect the DOM and browser behaviour and use **Burp Suite Proxy/Repeater** to capture and reproduce HTTP requests.

> **Alert:** Test only the instructor-authorised DVWA/WebGoat environment. Do not test university systems, public websites, other students' systems, or any application that has not been explicitly authorised.

---

# Part A – Prepare the Week 5 Environment

## Task 1 – Create the Week 5 Evidence Folder

On **Kali-Attacker**:

```bash
mkdir -p ~/lab-evidence/week5
cd ~/lab-evidence/week5
pwd
```

### Evidence to capture

Take one screenshot showing the Week 5 evidence folder.

### Expected result

```text
/home/<your-user>/lab-evidence/week5
```

---

## Task 2 – Verify the Authorised Application

On **Ubuntu-Server**:

```bash
sudo docker ps
```

**Note:** If DVWA, Burp Suite, WebGoat, or Juice Shop is not available or is showing as unhealthy, refer back to Labs 3 and 4 for instructions on downloading, configuring, and running the required applications.

From Kali, confirm connectivity.

DVWA example:

```bash
curl -I http://<UBUNTU_IP>/
```

WebGoat example:

```bash
curl -I http://<UBUNTU_IP>:8081/WebGoat
```

### Complete the table below:

| Item | Observed value |
|---|---|
| Application | DVWA / WebGoat |
| Target IP | |
| Published port | |
| HTTP status | |
| Application page loads? | Yes / No |

### Evidence to capture

Capture one screenshot showing the authorised application loaded in the browser.

### Expected result

The authorised DVWA/WebGoat target is reachable and ready for testing.

---

## Task 3 – Locate the XSS Lessons

### DVWA

Identify the instructor-designated lessons such as:

- **XSS (Reflected)**
- **XSS (Stored)**
- **XSS (DOM)**

Record the active DVWA security level.

### WebGoat

Locate the instructor-designated Cross-Site Scripting lesson. Lesson names may vary between WebGoat versions.

### Complete the table below:

| Item | Observation |
|---|---|
| Application | |
| XSS lesson/type | Reflected / Stored / DOM-oriented |
| URL/path | |
| Input field/parameter | |
| Security level/stage, if applicable | |

### Evidence to capture

Capture the lesson page before modifying any input.

### Expected result

The student identifies the authorised XSS lesson, its path, input field, and applicable security stage.

---

# Part B – Establish a Normal Baseline

## Task 4 – Submit Normal Input

Before testing XSS, establish normal application behaviour.

Use a harmless normal value such as:

```text
Week5Test
```

Record the HTTP method, request path, input/parameter name, normal value, response status, response length, and where the value appears on the page.

### Complete the table below:

| Property | Observed value |
|---|---|
| Parameter/input | |
| HTTP method | GET / POST |
| Normal value | |
| Response status | |
| Response length | |
| Visible output | |

### Evidence to capture

Capture one screenshot showing the normal input and resulting application behaviour.

### Knowledge Check

**Why should normal application behaviour be recorded before attempting XSS testing?**

### Expected result

A reproducible baseline exists for comparison with later modified requests.

---

## Task 5 – Capture the Baseline in Burp Suite

Open **Burp Suite → Proxy → HTTP history** and submit the same normal value.

Identify the authorised Host/IP, request method, request path, parameter, value, cookies, response status, and response length. Send the request to **Repeater**, resend it unchanged, and confirm the response matches the browser baseline.

### Complete the table below:

| Item | Observed value |
|---|---|
| Target host/IP | |
| Request method | |
| Request path | |
| Baseline parameter | |
| Baseline value | |
| Response status | |
| Response length | |
| Baseline reproduced in Repeater? | Yes / No |

### Evidence to capture

Capture one screenshot showing the authorised target, baseline request, method/path, normal input, response status, and response length.

Redact passwords, session identifiers, authentication tokens, and other sensitive values.

### Expected result

The baseline request can be reproduced reliably in Burp Repeater.

---

# Part C – Reflected XSS Testing

## Task 6 – Identify the Reflected Input

Open the authorised **Reflected XSS** lesson and submit:

```text
Week5Test
```

Use Browser DevTools, the Elements panel/page source, and the Burp response to determine whether the marker appears in returned HTML.

### Complete the table below:

| Item | Observation |
|---|---|
| Input parameter | |
| Request method | |
| Request path | |
| Test marker | Week5Test |
| Reflected in response? | Yes / No |
| HTML location | |
| Output context | Text / attribute / other |

### Evidence to capture

Capture evidence showing the input parameter and where the marker appears in the response or DOM.

### Expected result

Students identify the relationship:

**user input → HTTP request → server response → browser output**

---

## Task 7 – Identify the Output Context

In **Developer Tools → Elements**, locate the reflected marker and record the surrounding HTML.

Conceptual examples:

```html
Hello Week5Test
```

or:

```html
<input value="Week5Test">
```

Do not assume all reflection is XSS.

### Complete the table below:

| Context property | Observation |
|---|---|
| User-controlled input | |
| HTML element | |
| Text or attribute context | |
| Browser interprets as text? | Yes / No |
| Encoding observed? | Yes / No |

### Teaching point

**Reflection does not automatically mean confirmed XSS.**

### Expected result

Students identify the exact output context in which the user-controlled value appears.

---

## Task 8 – Perform a Harmless Reflected XSS Test

Use only the instructor-authorised XSS lesson.

Start with a minimal browser-visible proof such as:

```html
<script>alert('XSS')</script>
```

If the designated lesson filters that form, an instructor may permit another harmless demonstration appropriate to the lesson, such as:

```html
<img src=x onerror=alert('XSS')>
```

Do not use cookie exfiltration, external callback servers, credential harvesting, redirect payloads, persistent malicious scripts, or destructive actions.

### Complete the table below:

| Item | Observation |
|---|---|
| Parameter | |
| Test input | |
| Reflected in HTML? | Yes / No |
| Browser interpreted markup? | Yes / No |
| Script executed? | Yes / No |
| Response status | |
| Supported interpretation | |

### Evidence to capture

Capture one screenshot showing the authorised lesson, test input, relevant response/DOM context, and browser behaviour.

### Stop condition

Once a harmless visual proof is demonstrated, **stop testing**.

### Expected result

The student determines whether the input is merely reflected or actually interpreted as executable browser content.

---

## Task 9 – Compare Baseline and Modified Response

### Complete the table below:

| Feature | Baseline | Modified |
|---|---|---|
| HTTP status | | |
| Response length | | |
| Input reflected | | |
| HTML context | | |
| Browser execution | No | |
| Encoding observed | | |

### Supported interpretation

Write one evidence-based statement.

Example:

> The modified value was returned inside the page without effective output encoding and the browser interpreted the supplied markup as executable content.

### Expected result

The student explains the observed difference between normal and modified input without overstating the security impact.

---

# Part D – Stored XSS Testing

## Task 10 – Establish the Stored-Input Baseline

Open the authorised **Stored XSS** lesson and enter a normal value such as:

```text
Week5 Stored Test
```

Submit it, reload or revisit the page, and determine whether the value persists.

### Complete the table below:

| Item | Observation |
|---|---|
| Input field | |
| Normal value | |
| Value retained after reload? | Yes / No |
| Display location | |
| HTTP method | |
| Request path | |

### Evidence to capture

Capture one screenshot showing the normal stored input after it is rendered.

### Teaching point

**Submit → Store → Retrieve → Render**

### Expected result

The student establishes normal persistent-input behaviour before introducing modified content.

---

## Task 11 – Capture the Stored-Input Request in Burp

Use Burp Proxy history to locate the submission and the later request that retrieves or displays the stored value.

### Complete the table below:

| Item | Submission request | Retrieval/display request |
|---|---|---|
| HTTP method | | |
| Request path | | |
| Parameter | | |
| Response status | | |
| Response length | | |
| Stored value visible? | | |

### Evidence to capture

Capture both the submission request and the page displaying the stored value.

### Expected result

Students identify how a submitted value is stored and later returned to the browser.

---

## Task 12 – Perform a Controlled Stored-XSS Test

Use only the instructor-designated field and the minimum harmless proof necessary, for example:

```html
<script>alert('Stored XSS')</script>
```

Submit once, reload/revisit the lesson, and record whether the script executes when stored content is rendered.

Do **not** attempt session theft, cookie extraction, phishing forms, keylogging, or external network callbacks.

### Complete the table below:

| Feature | Observation |
|---|---|
| Input accepted | Yes / No |
| Input stored | Yes / No |
| Input rendered later | Yes / No |
| Browser interprets markup | Yes / No |
| Script executes | Yes / No |
| Execution persists after reload | Yes / No |
| Supported interpretation | |

### Evidence to capture

Capture the stored input and the later rendering behaviour.

### Expected result

Students distinguish reflected behaviour from stored behaviour using direct evidence.

---

# Part E – DOM-Oriented Input Handling

## Task 13 – Identify a DOM Input Source

Open the instructor-designated **DOM XSS/DOM-oriented** lesson and use Browser DevTools to inspect the URL, query string, fragment/hash, form value, JavaScript, and DOM changes.

Use a harmless marker:

```text
DOMTest123
```

### Complete the table below:

| Item | Observation |
|---|---|
| Input source | URL / hash / field / other |
| Marker | DOMTest123 |
| Sent to server? | Yes / No / unclear |
| JavaScript processes value? | Yes / No |
| DOM element changed | |
| Output context | |

### Evidence to capture

Capture DevTools evidence showing the input source and affected DOM element.

### Expected result

The student identifies a browser-controlled input source and its relationship to the page DOM.

---

## Task 14 – Trace Source to Sink

Using DevTools, identify as much of the flow as possible:

**Source → JavaScript processing → DOM sink**

Potential operations may include `innerHTML`, `document.write`, or `location`, but students should record what they **actually observe**.

### Complete the table below:

| Stage | Observation |
|---|---|
| Source | |
| JavaScript processing | |
| Sink/output | |
| Encoding/sanitisation observed | |
| Resulting DOM | |

### Teaching point

DOM-oriented weaknesses may occur largely in the browser; the vulnerable value may not appear in the server-generated response.

### Expected result

Students document the observed source-to-sink flow.

---

## Task 15 – Perform a Harmless DOM Test

Using only the designated lesson, determine whether controlled input changes the DOM in an unsafe way. Use the instructor-approved minimum harmless proof.

### Complete the table below:

| Item | Observation |
|---|---|
| Input supplied | |
| DOM before | |
| DOM after | |
| Markup created? | Yes / No |
| Script execution observed? | Yes / No |
| Supported interpretation | |

### Evidence to capture

Capture DevTools showing the source/input, affected DOM element, and resulting DOM structure.

### Expected result

Students determine whether the controlled input is handled as text or interpreted as active DOM content.

---

# Part F – Compare the Three XSS Types

## Task 16 – Build an XSS Comparison Table

### Complete the table below:

| Characteristic | Reflected | Stored | DOM-oriented |
|---|---|---|---|
| User input source | | | |
| Input stored? | | | |
| Returned by server? | | | |
| DOM modified? | | | |
| Script execution observed? | | | |
| Trigger condition | | | |
| Evidence reference | | | |

### Knowledge Check

**What is the most important technical difference between reflected, stored, and DOM-oriented XSS?**

### Expected result

Students distinguish the three XSS categories using observed data flow and execution behaviour.

---

# Part G – Separate Evidence from Assumption

## Task 17 – Evidence-Based Interpretation

Distinguish:

**Observed evidence → Supported interpretation → Unsupported assumption**

### Complete the table below:

| Observed evidence | Supported interpretation | Unsupported assumption |
|---|---|---|
| Input appears unencoded in HTML | Output handling may be unsafe | Every input on the site is vulnerable |
| Browser executes harmless script | XSS was demonstrated in this context | Full account compromise occurred |
| Stored value executes after reload | Stored XSS behaviour is demonstrated | Every user can be attacked |
| DOM changes based on URL input | Client-side code processes the input | Server-side XSS exists |
| Your example | | |

### Expected result

Students clearly distinguish direct observations from supported conclusions and unsupported claims.

---

# Part H – Assess the Security Impact

## Task 18 – Assess Demonstrated Impact

Use:

```text
Observed
Not observed
Requires further verification
```

### Complete the table below:

| Impact area | Reflected | Stored | DOM-oriented |
|---|---|---|---|
| Browser script execution | | | |
| Persistent behaviour | | | |
| Sensitive information exposed | | | |
| User interaction required | | | |
| Session compromise demonstrated | | | |
| Further verification required | | | |

Do not claim session theft merely because XSS could theoretically be used for it.

### Expected result

Students assess only the impact demonstrated by the evidence.

---

# Part I – Understand XSS Remediation

## Task 19 – Identify the Correct Mitigation

Review the vulnerable lesson/source or remediation guidance and match the control to the output context.

### Complete the table below:

| Weakness / context | Appropriate control |
|---|---|
| HTML text output | Context-aware HTML output encoding |
| HTML attribute output | Attribute-safe encoding and controlled attributes |
| JavaScript context | JavaScript-safe encoding / avoid dynamic code construction |
| DOM manipulation | Use safe DOM APIs such as `textContent` where appropriate |
| Untrusted rich content | Proven HTML sanitisation |
| Additional browser defence | Content Security Policy |
| Unsafe input | Server-side validation appropriate to expected data |

### Important concept

**Input validation alone is not a complete XSS defence.**

### Expected result

Students connect each observed weakness to a context-appropriate mitigation.

---

## Task 20 – Inspect the Vulnerable and Safer Pattern

Conceptual unsafe pattern:

```javascript
element.innerHTML = userInput;
```

Safer approach for plain text:

```javascript
element.textContent = userInput;
```

### Complete the table below:

| Item | Observation / explanation |
|---|---|
| Vulnerable pattern reviewed | |
| Unsafe DOM/API use observed | |
| Why the pattern is unsafe | |
| Safer handling approach | |
| Why the safer approach helps | |

### Expected result

Students explain why safer output handling changes browser interpretation of untrusted data.

---

# Part J – Retest the Control

## Task 21 – Retest the Same XSS Input

If the DVWA security level or WebGoat remediation stage supports it, retain the original baseline and controlled XSS input, change only the relevant control/stage, and repeat the test.

### Complete the table below:

| Behaviour | Before remediation | After remediation |
|---|---|---|
| Normal input displays correctly | | |
| Special characters accepted | | |
| Markup interpreted | | |
| Script executes | | |
| Output encoded | | |
| DOM altered unsafely | | |
| Validation message | | |

### Interpretation

Write one short evidence-based statement identifying the specific observed control or behavioural difference.

### Evidence to capture

Capture the stronger/remediated stage and comparable before/after evidence.

### Expected result

Students identify the **specific observed control or behavioural difference** after remediation.

---

# Part K – Evidence Collection

## Task 22 – Save Required Week 5 Evidence

Suggested filenames:

```text
01-target-running.png
02-xss-lessons.png
03-baseline-request.png
04-reflected-context.png
05-reflected-xss-test.png
06-stored-baseline.png
07-stored-xss-test.png
08-dom-source-sink.png
09-dom-test.png
10-remediation-retest.png
11-xss-findings-summary.txt
```

Before submission, confirm evidence does not expose passwords, PHPSESSID values, WebGoat authentication tokens, unrelated session identifiers, or other sensitive values.

### Complete the table below:

| Check | Complete? |
|---|---|
| All required evidence files are present | Yes / No |
| Filenames clearly map to tasks | Yes / No |
| Screenshots are readable | Yes / No |
| Request/response details are visible where required | Yes / No |
| Passwords are redacted | Yes / No |
| Session IDs/tokens are redacted | Yes / No |
| Findings table is included | Yes / No |
| Findings summary is included | Yes / No |
| Every saved file opens successfully | Yes / No |

### Expected result

A complete, readable, clearly organised, and appropriately redacted Week 5 evidence set is ready for submission.

---

# Part L – XSS Findings Table

## Task 23 – Produce Evidence-Based XSS Findings

Use only the screenshots, requests/responses, DevTools observations, remediation notes, and retest evidence already collected.

### Complete the table below:

| Area | Reflected XSS | Stored XSS | DOM-oriented |
|---|---|---|---|
| Target | | | |
| Input source/parameter | | | |
| Baseline behaviour | | | |
| Modified input | | | |
| Output context | | | |
| Observed behaviour | | | |
| Script execution demonstrated? | | | |
| Supported interpretation | | | |
| Potential impact | | | |
| Remediation | | | |
| Retest result | | | |
| Evidence reference | | | |

### Core reporting sequence

**Observed evidence → Output context → Supported interpretation → Impact → Mitigation → Retest**

### Expected result

Students produce concise, evidence-based findings for reflected, stored, and DOM-oriented XSS/input-handling weaknesses.

---

# Part M – Advanced XSS Analysis (Optional)

## Task 24 – Compare Reflected XSS Across DVWA Security Levels  
**Advanced Lab, Optional**

**Tools:** Kali-Attacker browser, Browser DevTools, Burp Suite Repeater  
**Target:** DVWA only

### Purpose

Compare how the **same reflected-XSS input** is handled at different DVWA security levels while keeping the request and test input as consistent as possible.

### Steps

1. Open the authorised **DVWA XSS (Reflected)** lesson.
2. Set the DVWA security level to **Low**.
3. Submit the baseline marker `Week5Test`.
4. Capture the request in Burp and send it to Repeater.
5. Record method, path, parameter, response status, response length, and output context.
6. Use the same instructor-approved harmless XSS test from the earlier task.
7. Record whether the input is reflected, encoded, interpreted as markup, and executed.
8. Repeat at **Medium** security.
9. Repeat at **High** security.
10. Keep the same logical test wherever the lesson permits.
11. Do not assume that a higher security-level label means the vulnerability has been eliminated.
12. Describe the actual technical difference observed.

### Complete the table below:

| Feature | Low | Medium | High |
|---|---|---|---|
| Parameter tested | | | |
| Normal input works | | | |
| Input reflected | | | |
| Output context | | | |
| Special characters encoded | | | |
| Markup interpreted | | | |
| Script execution observed | | | |
| Response status | | | |
| Response length | | | |
| Security control observed | | | |

### Evidence to capture

Capture comparable evidence showing the security level, same lesson/parameter, input, resulting output, browser behaviour, and relevant Burp request/response.

### Expected result

Students identify the **specific input/output control that changes** between security levels.

---

# Part N – Findings Summary

## Task 29 – Write a 400–500 Word Findings Summary

Use evidence from the **core Tasks 1–23**. Evidence from optional advanced activities may be included where completed, but students should **not be disadvantaged for not completing optional activities**.

Write approximately **400–500 words**.

Include:

- authorised target/application;
- XSS lessons tested;
- baseline behaviour;
- reflected-XSS evidence;
- stored-XSS evidence;
- DOM-oriented evidence;
- affected input/source;
- affected output/DOM context;
- Browser DevTools observations;
- Burp request/response evidence;
- demonstrated security impact;
- mitigation;
- retesting;
- limitations;
- whether each XSS weakness was actually validated.

### Suggested structure

> XSS testing was conducted against the authorised DVWA/WebGoat training application using Browser Developer Tools and Burp Suite. Baseline requests established the normal handling of user-controlled input before any modified values were introduced.
>
> Reflected XSS testing of **[parameter]** showed that **[observed evidence]**. Inspection of the returned HTML identified the input in **[output context]**. The controlled test **[did/did not]** result in browser script execution.
>
> Stored XSS testing demonstrated **[observation]**, with the supplied value **[persisting/not persisting]** and being rendered when **[trigger]** occurred.
>
> DOM-oriented testing identified **[source]** flowing into **[DOM location/sink]**. Developer Tools showed **[observable DOM change]**.
>
> Recommended remediation includes context-aware output encoding, safe DOM manipulation, appropriate server-side input validation, HTML sanitisation where rich content is required, and CSP as an additional defence. Testing was limited to the designated training lessons, and conclusions therefore describe only behaviour directly demonstrated during the authorised assessment.

### Expected result

A concise professional findings summary links evidence, input/output context, impact, remediation, retest results, and limitations.

---

# Part O – Additional Advanced XSS Analysis (Optional)

## Task 25 – Analyse XSS Output Contexts  
**Advanced Lab, Optional**

**Tools:** Browser DevTools, Burp Suite  
**Target:** DVWA / WebGoat

### Purpose

Investigate why the same untrusted value may require different security handling depending on **where it is inserted into the page**.

### Complete the table below:

| Output context | Input/source | Where value appears | Encoding observed | Appropriate control |
|---|---|---|---|---|
| HTML text | | | | |
| HTML attribute | | | | |
| URL context | | | | |
| JavaScript context | | | | |
| DOM context | | | | |

### Knowledge Check

**Why is “HTML encode everything” not a complete description of XSS prevention?**

### Expected result

Students relate an XSS weakness to its exact **source, output context, and mitigation**.

---

## Task 26 – Trace a DOM Source-to-Sink Flow  
**Advanced Lab, Optional**

**Tools:** Browser DevTools  
**Target:** Instructor-designated DOM-oriented lesson

### Purpose

Trace user-controlled data through client-side JavaScript from its **source** to the **DOM sink** where it influences page content.

Use a unique harmless marker such as:

```text
DOMSourceTest2026
```

### Complete the table below:

| Stage | Observation |
|---|---|
| Source | |
| Example input | |
| JavaScript processing | |
| DOM sink/property | |
| DOM element affected | |
| Encoding/sanitisation observed | |
| Browser interpretation | |
| Evidence reference | |

### Source-to-sink model

```text
User-controlled source
        ↓
Client-side JavaScript
        ↓
Transformation / processing
        ↓
DOM sink
        ↓
Browser interpretation
```

### Expected result

Students document a defensible **source → processing → sink → browser behaviour** chain.

---

## Task 27 – Inspect Content Security Policy as a Defence-in-Depth Control  
**Advanced Lab, Optional**

**Tools:** Browser DevTools, Burp Suite  
**Target:** DVWA / WebGoat where applicable

### Purpose

Determine whether the application provides a **Content Security Policy (CSP)** and explain how CSP can reduce the impact of some XSS vulnerabilities.

Inspect **Network → select request → Response Headers** or the response headers in Burp and look for:

```text
Content-Security-Policy
```

Do not attempt to bypass the CSP.

### Complete the table below:

| Item | Observation |
|---|---|
| CSP present? | Yes / No |
| `default-src` | |
| `script-src` | |
| Inline script allowed? | |
| External script restrictions | |
| Other relevant directives | |
| Security implication | |

### Knowledge Check

**Why should CSP not be treated as the primary fix for an XSS vulnerability?**

### Expected result

Students explain that CSP may **reduce exploitability or impact**, while the underlying unsafe handling of untrusted data should still be corrected.

---

## Task 28 – Compare Server Response with the Browser DOM  
**Advanced Lab, Optional**

**Tools:** Burp Suite, Browser DevTools  
**Target:** DVWA / WebGoat

### Purpose

Compare what the **server actually returned** with what the **browser eventually constructed in the DOM**.

### Steps

1. Select one previously tested XSS/input-handling example.
2. In Burp, capture the corresponding server response.
3. Record whether the controlled marker appears in the raw HTTP response.
4. In Browser DevTools, inspect the final DOM after JavaScript has executed.
5. Search for the same marker.
6. Compare the raw response and final DOM.
7. Determine whether the content was already present in the response, subsequently inserted/modified by JavaScript, or absent from one of the two.
8. Record any observable transformation.
9. Classify the issue from the evidence, not only from the lesson name.

### Complete the table below:

| Feature | Raw HTTP response | Final browser DOM |
|---|---|---|
| Marker present? | Yes / No | Yes / No |
| Location | | |
| HTML structure | | |
| Encoding | | |
| JavaScript modification evident? | | |
| Browser interpretation | | |

### Interpretation

Write one short evidence-based statement.

### Expected result

Students distinguish between **server response content** and **browser-generated DOM content**.

---

# Troubleshooting

## Problem – DVWA/WebGoat Is Not Reachable

On Ubuntu:

```bash
sudo docker ps
```

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
- **Proxy → HTTP history**;
- whether the correct browser profile is being used.

## Problem – Test Input Produces No Visible Difference

Check:

- correct XSS lesson/module;
- correct parameter;
- correct HTTP method;
- application security level;
- whether the input is reflected in the raw response;
- whether JavaScript modifies the DOM after load;
- DevTools Console and Elements views;
- response content as well as response status and length.

Record the behaviour actually observed. Do not force results to match an example.

---

# Knowledge Check

1. What is Cross-Site Scripting (XSS)?
2. Why should a normal baseline be recorded before modifying input?
3. What is the difference between reflected and stored XSS?
4. How does DOM-oriented XSS differ from server-side reflected XSS?
5. Why does reflection alone not prove XSS?
6. What is an output context?
7. Why does the output context affect the required encoding?
8. What evidence would demonstrate that the browser interpreted input as executable content?
9. Why should a harmless proof-of-concept be used?
10. Why should testing stop once sufficient evidence is obtained?
11. Why is `textContent` generally safer than `innerHTML` for plain-text output?
12. Why is input validation alone not enough to prevent XSS?
13. What role can HTML sanitisation play?
14. What is Content Security Policy?
15. Why is CSP considered defence in depth rather than the primary XSS fix?
16. Why should raw server responses and the final browser DOM sometimes be compared?
17. Why must observed evidence and potential impact be reported separately?
18. Why should remediation be retested?

---

# Summary

In this lab, students use the following workflow:

**Baseline → Capture → Identify Source → Identify Output Context → Modify → Compare → Validate → Assess Impact → Remediate → Retest → Report**

Students use **DVWA/WebGoat**, **Browser Developer Tools**, and **Burp Suite** to investigate reflected, stored, and DOM-oriented XSS/input-handling weaknesses.

The main remediation outcomes are:

- **Reflected/stored output handling → context-aware output encoding**
- **DOM manipulation → safer DOM APIs**
- **Rich HTML content → appropriate sanitisation**
- **Additional browser protection → Content Security Policy**
- **Input handling → server-side validation appropriate to the expected format**
- **Reporting → evidence first; vulnerability claims only when validated**

Optional advanced activities extend this workflow through:

**Compare Controls → Analyse Context → Trace Source/Sink → Inspect CSP → Compare Response vs DOM**
