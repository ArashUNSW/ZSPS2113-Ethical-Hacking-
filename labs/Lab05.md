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

1. On **Kali-Attacker**, open a terminal.
2. Create the Week 5 evidence folder:

```bash
mkdir -p ~/lab-evidence/week5
```

3. Change into the folder:

```bash
cd ~/lab-evidence/week5
```

4. Confirm the current path:

```bash
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

On **Kali-Attacker**,

Log in to the DVWA (admin, password) and identify the instructor-designated lessons such as:

- **XSS (Reflected)**
- **XSS (Stored)**
- **XSS (DOM)**

Record the active DVWA security level.

### WebGoat

Locate the instructor-designated Cross-Site Scripting lessons such as:

- **Cross Site Scripting**
- **Cross Site Scripting (Stored)**
- **Cross Site Scripting (Mitigation)**

Lesson names may vary between WebGoat versions.

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

### DVWA

On **Kali-Attacker**,

Before testing XSS, establish the application's normal behaviour.

Use a harmless normal value, as you did in Lab 4 for SQL Injection. Enter a normal User ID value and submit the form to observe the application's expected behaviour.

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

1. On **Kali-Attacker**, start Burp Suite:

```bash
burpsuite
```

2. Use either:
   - the Burp built-in browser, or
   - the instructor-configured browser that is already using the Burp proxy.

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

You can reliably reproduce the baseline request in Burp Repeater.

---

# Part C – Reflected XSS Testing

## Task 6 – Identify the Reflected Input

On **Kali-Attacker**,

Open the authorised DVWA **Reflected XSS** lesson and submit:

```text
Week5Test
```

Use Burp Suite's response to see whether the marker appears in the returned HTML, or you can also use Browser DevTools, the Elements panel/page source, Ctrl + F to search for Week5Test.

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

In Burp Suite or Developer Tools → Elements, locate the reflected marker and record the surrounding HTML.

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

### Note

Reflection does not automatically mean confirmed XSS.

### Expected result

Students identify the exact output context in which the user-controlled value appears.

---

## Task 8 – Perform a Harmless Reflected XSS Test

Use only the instructor-authorised DVWA XSS lesson.

Start with a minimal browser-visible proof. Open the authorised Reflected XSS lesson and submit:

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

Compare the baseline request using normal input with the modified request used for XSS testing. Record any differences in the HTTP response, reflected input, HTML context, browser behaviour, and output encoding. Use your Burp Suite observations to determine how the application handles normal versus modified input.

### Complete the table below:

| Feature | Baseline | Modified |
|---|---|---|
| HTTP status | | |
| Response length | | |
| Input reflected | | |
| HTML context | | |
| Browser execution | No | |
| Encoding observed | | |

### Sample results

See the following sample comparison; you can complete **Task 9** in a similar way:

| Feature               | Baseline                          | Modified                                           |
| --------------------- | --------------------------------- | -------------------------------------------------- |
| **HTTP status**       | `200 OK`                          | `200 OK`                                           |
| **Response length**   | `4364 bytes`                      | `4384 bytes`                                       |
| **Input reflected**   | Yes — `Week5Test`                 | Yes — supplied `<script>...` value                 |
| **HTML context**      | Text content in the returned page | Script/HTML markup inserted into the returned page |
| **Browser execution** | No                                | **Yes, if the alert box appeared in the browser**  |
| **Encoding observed** | Normal text returned              | **No effective output encoding observed**          |

### Interpretation
The baseline value `Week5Test` was reflected as normal text, while the modified `<script>` value was returned in the page without effective output encoding. If the browser displayed the JavaScript alert, this confirms that the supplied markup was interpreted as executable content.

One important point: in the request, characters such as < and > appearing as %3C and %3E are URL encoding, not XSS protection. What matters is whether the server safely HTML-encodes the value in the response. In this case, the modified value appears to be returned without effective output encoding.

### Expected result

The student explains the observed difference between normal and modified input without overstating the security impact.

---

# Part D – Stored XSS Testing

## Task 10 – Establish the Stored-Input Baseline

Open the authorised **Stored XSS** lesson and enter a normal value such as:

```text
Week5Test
```

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

```text
Week5Test
```

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

Open the instructor-designated **XSS (DOM) / DOM-oriented** lesson and use Browser DevTools to inspect the URL, query string, fragment/hash, form value, JavaScript, and DOM changes.

Use a harmless marker:

```text
DOMTest123
```

On the DVWA **DOM XSS** page, there is no free-text box. The input source is the **URL query parameter** shown in the address bar:

```text
?vulnerability...
?default=English
```

To enter the harmless marker DOMTest123, replace English in the URL with your marker.

Change (example):
http://192.168.1.100/vulnerabilities/xss_d/?default=English

to (example):
http://192.168.1.100/vulnerabilities/xss_d/?default=DOMTest123

Then press Enter.
After the page reloads, check whether DOMTest123 appears in the dropdown or elsewhere on the page.

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

Use **Browser DevTools** to trace how the DOM-XSS input moves through the page.

The goal is to identify the flow:

**Source → JavaScript processing → DOM sink**

For this DVWA DOM-XSS exercise, begin by opening:

**DevTools → Sources**

Then:

1. Open the page source for the DOM-XSS lesson.
2. Search for terms such as:
   - `default`
   - `location`
   - `document.location`
   - `document.write`
   - `innerHTML`
3. Identify where the page reads the user-controlled value from the URL.
4. Follow how JavaScript processes that value.
5. Identify the DOM operation that inserts the value into the page.
6. Confirm the resulting DOM in **DevTools → Elements**.

For example, if the page URL contains:

```text
?default=DOMTest123
```

and JavaScript reads the `default` value from the URL and inserts it into the language dropdown, record the exact behaviour you observe.

### Observe the table below:

| Stage | Observation |
|---|---|
| **Source** | URL query parameter, for example `default=DOMTest123` |
| **JavaScript processing** | Record how JavaScript reads or extracts the value, such as through `document.location` or another URL-processing operation |
| **Sink/output** | Record the DOM-writing operation actually observed, such as `document.write(...)` or `innerHTML` |
| **Encoding/sanitisation observed** | Record whether the value is encoded, filtered, sanitised, or inserted without visible protection |
| **Resulting DOM** | Record where the supplied value appears in the DOM, for example inside an `<option>` element in the language dropdown |

### Evidence to capture

Capture DevTools evidence showing:

- the browser-controlled input source;
- the JavaScript code that processes the value;
- the DOM sink or output operation; and
- the resulting DOM element containing the marker value.

### Note

Do **not** assume a particular sink such as `document.write` or `innerHTML`. Record only the operation you actually observe in DevTools.

DOM-oriented weaknesses may occur largely in the browser, so the vulnerable value may not appear in the original server-generated response.

### Expected result

Students document the observed source-to-sink flow and explain how JavaScript reads a browser-controlled value and inserts it into the DOM.

---

## Task 15 – Perform a Harmless DOM Test

Using only the designated **DOM XSS** lesson, test whether a controlled browser-supplied value is inserted into the DOM as plain text or interpreted as active markup.

Use only the **instructor-approved harmless test value**. Do not use payloads that access cookies, session data, credentials, external systems, or other users.

### Procedure

1. Open the designated DOM XSS page.
2. Identify the browser-controlled input source from **Task 13**, for example the `default` URL parameter.
3. Record the relevant DOM element in **DevTools → Elements** before changing the input.
4. Replace the normal input value with the instructor-approved harmless test value.
5. Reload or revisit the page.
6. Inspect the same DOM element again.
7. Determine whether the supplied value:
   - remains plain text,
   - creates new HTML markup, or
   - results in harmless JavaScript execution.
8. Record only what you actually observe.

### Complete the table below:

| Item | Observation |
|---|---|
| **Input supplied** | |
| **DOM before** | |
| **DOM after** | |
| **Markup created?** | Yes / No |
| **Script execution observed?** | Yes / No |
| **Supported interpretation** | |

### Example observations

For **DOM before**, you might record something such as:

```html
<option value="English">English</option>
```

For **DOM after**, record the resulting structure shown in DevTools after supplying the test value.

If the browser creates additional HTML elements from the supplied value, record:

```text
Markup created: Yes
```

If a harmless alert or equivalent approved proof executes, record:

```text
Script execution observed: Yes
```

Do not mark either result as **Yes** unless it is directly observed.

### Evidence to capture

Capture DevTools evidence showing:

- the browser-controlled input source;
- the relevant DOM element before the test;
- the resulting DOM structure after the test; and
- any harmless execution evidence, if observed.

### Expected result

Students determine whether controlled browser input is treated as **plain text** or interpreted as **active DOM content**, and support the conclusion with DevTools evidence.

---

# Part F – Compare the Three XSS Types

## Task 16 – Build an XSS Comparison Table

Use the evidence collected in the **Reflected XSS**, **Stored XSS**, and **DOM-oriented XSS** tasks to compare how each type handles user-controlled input.

Focus on the **data flow**, **where the value is processed**, **whether it is stored**, and **what causes execution**. Record only what you observed in the lab.

### Complete the table below:

| Characteristic | Reflected XSS | Stored XSS | DOM-oriented XSS |
|---|---|---|---|
| **User input source** | | | |
| **Input stored?** | | | |
| **Returned by server?** | | | |
| **Processed mainly by** | Server response / browser | Server + storage + browser | Browser-side JavaScript |
| **DOM modified?** | | | |
| **Script execution observed?** | | | |
| **Typical trigger condition observed** | | | |
| **Evidence reference** | | | |

### Guidance

Use your earlier evidence to answer the table. For example:

- **Reflected XSS:** the supplied value is sent in a request and returned in the same response.
- **Stored XSS:** the supplied value is saved by the application and rendered again later.
- **DOM-oriented XSS:** browser-side JavaScript reads a controllable source and writes it into the DOM.

Do not assume that every field on the application behaves the same way.

### Knowledge Check

**What is the most important technical difference between reflected, stored, and DOM-oriented XSS?**

Write a short answer based on the observed **source-to-sink/data-flow behaviour**.

### Evidence to capture

Reference the screenshots or observations you collected in the earlier tasks, including:

- the reflected request/response;
- the stored value after reload or revisit; and
- the DOM source, JavaScript processing, and resulting DOM change.

### Expected result

Students distinguish the three XSS categories using observed **input flow**, **storage behaviour**, **server involvement**, **DOM manipulation**, and **execution conditions**.

---

# Part G – Separate Evidence from Assumption

## Task 17 – Evidence-Based Interpretation

For each observation, distinguish between:

**Observed evidence → Supported interpretation → Unsupported assumption**

The goal is to make conclusions that are supported by the lab evidence without overstating the security impact.

### Complete the table below:

| Observed evidence | Supported interpretation | Unsupported assumption |
|---|---|---|
| Input appears unencoded in returned HTML | Output handling may be unsafe in this context | Every input on the site is vulnerable |
| Browser executes the instructor-approved harmless script | | |
| Stored value executes after reload or revisit | | |
| DOM changes based on controlled URL input | | |
| Response status remains `200 OK` after modified input | | |
| **Your example** | | |

### Short interpretation exercise

Choose **one** result from your lab and write:

1. **What you directly observed**
2. **What conclusion is supported by that observation**
3. **What claim would go beyond the evidence**

### Example

> **Observed:** The modified value was returned in the page and the browser displayed the harmless alert.  
> **Supported interpretation:** The supplied value was interpreted as executable content in this context.  
> **Unsupported assumption:** The vulnerability allows complete compromise of all users.

### Expected result

Students clearly separate:

- **direct observations**;
- **reasonable technical conclusions** supported by those observations; and
- **claims that are not demonstrated by the available evidence**.


# Part H – Assess the Security Impact

## Task 18 – Assess Demonstrated Impact

Use only the following terms when recording impact:

```text
Observed
Not observed
Requires further verification
```

Base each entry on evidence collected during the authorised lab. Do not infer impact that was not directly demonstrated.

### Complete the table below:

| Impact area | Reflected XSS | Stored XSS | DOM-oriented XSS |
|---|---|---|---|
| **Browser script execution** | | | |
| **Persistent behaviour** | | | |
| **Sensitive information exposed** | | | |
| **User interaction required** | | | |
| **Session compromise demonstrated** | | | |
| **Further verification required** | | | |

### Guidance

- Mark **Observed** only when the behaviour was directly demonstrated.
- Mark **Not observed** when the behaviour was specifically checked and did not occur.
- Mark **Requires further verification** when the available evidence is insufficient to reach a conclusion.
- Do **not** claim session theft, credential theft, account compromise, or broader user impact merely because XSS could theoretically enable those outcomes.

### Short interpretation

Write one or two sentences identifying the **highest impact actually demonstrated** by your evidence.

### Expected result

Students assess only the impact supported by the collected evidence and clearly distinguish demonstrated behaviour from theoretical risk.

---

# Part I – Understand XSS Remediation

## Task 19 – Match the Weakness to the Correct Mitigation

Review the vulnerable lesson, available source code, remediation guidance, or safer implementation provided in the authorised training environment.

Match each output context or weakness to the most appropriate mitigation.

### Complete the table below:

| Weakness / context | Appropriate control |
|---|---|
| **HTML text output** | Context-aware HTML output encoding |
| **HTML attribute output** | |
| **JavaScript context** | |
| **DOM manipulation** | |
| **Untrusted rich HTML content** | |
| **Additional browser defence** | |
| **Unexpected or unsafe input** | |

### Important concept

> **Input validation alone is not a complete defence against XSS.**

Validation can reduce unexpected input, but XSS prevention also requires **context-appropriate output handling** and safe browser-side APIs.

### Knowledge Check

Why is the correct mitigation dependent on the **output context**?

### Expected result

Students connect each observed weakness to a context-appropriate mitigation and explain why validation alone is insufficient.

---

## Task 20 – Inspect the Vulnerable and Safer Pattern

Review the following conceptual examples.

### Potentially unsafe pattern

```javascript
element.innerHTML = userInput;
```

### Safer approach when plain text is intended

```javascript
element.textContent = userInput;
```

### Complete the table below:

| Item | Observation / explanation |
|---|---|
| **Vulnerable pattern reviewed** | |
| **Unsafe DOM/API use observed** | |
| **Why the pattern is unsafe** | |
| **Safer handling approach** | |
| **Why the safer approach helps** | |

### Guidance

Your explanation should focus on **browser interpretation**.

For example, `innerHTML` can cause supplied characters to be interpreted as HTML markup, whereas `textContent` inserts the value as text rather than parsing it as HTML.

Do not state that `textContent` is appropriate for every situation. It is suitable where the application intends to display **plain text**.

### Expected result

Students explain how safer output handling changes the browser's interpretation of untrusted data.

---

# Part J – Retest the Control

## Task 21 – Retest the Same XSS Input

If the DVWA security level, WebGoat remediation stage, or equivalent training control supports it, repeat the same test after changing **only the relevant security control or remediation stage**.

Retain the original:

- baseline input;
- controlled XSS test value;
- target page or lesson; and
- evidence method.

This allows a valid before-and-after comparison.

### Complete the table below:

| Behaviour | Before remediation | After remediation |
|---|---|---|
| **Normal input displays correctly** | | |
| **Special characters accepted** | | |
| **Markup interpreted** | | |
| **Script executes** | | |
| **Output encoded** | | |
| **DOM altered unsafely** | | |
| **Validation or security message shown** | | |

### Interpretation

Write one short evidence-based statement identifying the **specific observed control or behavioural difference**.

Example structure:

> Before remediation, **[observed behaviour]**. After the control was applied, **[observed difference]**, indicating that **[specific control effect]**.

### Evidence to capture

Capture comparable before-and-after evidence showing:

- the same input or test value;
- the relevant request/response or DOM state;
- the changed security/remediation stage; and
- the resulting difference in browser behaviour.

### Expected result

Students identify the **specific observed control or behavioural difference** and avoid attributing improvements to controls that were not actually verified.

---

# Part K – Evidence Collection

## Task 22 – Save Required Week 5 Evidence

Save the evidence collected during the core Week 5 activities using clear filenames that map directly to the relevant tasks.

### Suggested filenames

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

Before submission, review every screenshot and file to ensure that it does **not** expose:

- passwords;
- `PHPSESSID` values;
- WebGoat authentication tokens;
- unrelated session identifiers;
- API keys or credentials; or
- other sensitive values.

Redact sensitive values while keeping the technical evidence needed for assessment visible.

### Complete the table below:

| Check | Complete? |
|---|---|
| **All required evidence files are present** | Yes / No |
| **Filenames clearly map to tasks** | Yes / No |
| **Screenshots are readable** | Yes / No |
| **Request/response details are visible where required** | Yes / No |
| **Passwords are redacted** | Yes / No |
| **Session IDs/tokens are redacted** | Yes / No |
| **Findings table is included** | Yes / No |
| **Findings summary is included** | Yes / No |
| **Every saved file opens successfully** | Yes / No |

### Expected result

A complete, readable, clearly organised, and appropriately redacted Week 5 evidence set is ready for submission.

---

# Part L – XSS Findings Table

## Task 23 – Produce Evidence-Based XSS Findings

Use only the evidence already collected from:

- screenshots;
- Burp Suite requests and responses;
- Browser DevTools observations;
- remediation/source notes; and
- retest evidence.

Do not introduce claims that were not demonstrated during the authorised lab.

### Complete the table below:

| Area | Reflected XSS | Stored XSS | DOM-oriented XSS |
|---|---|---|---|
| **Target** | | | |
| **Input source / parameter** | | | |
| **Baseline behaviour** | | | |
| **Modified input** | | | |
| **Output / DOM context** | | | |
| **Observed behaviour** | | | |
| **Script execution demonstrated?** | | | |
| **Supported interpretation** | | | |
| **Demonstrated / potential impact** | | | |
| **Recommended remediation** | | | |
| **Retest result** | | | |
| **Evidence reference** | | | |

### Core reporting sequence

Use the following sequence for each finding:

**Observed evidence → Output/DOM context → Supported interpretation → Impact → Mitigation → Retest**

### Reporting guidance

- Keep **observed behaviour** separate from **potential impact**.
- Use **demonstrated impact** where the lab directly proved a consequence.
- Use **potential impact** only where clearly labelled as theoretical.
- Refer to specific evidence filenames, screenshots, or task numbers where possible.
- Do not claim broader compromise unless it was directly demonstrated.

### Expected result

Students produce concise, technically accurate, evidence-based findings for reflected, stored, and DOM-oriented XSS/input-handling weaknesses.

---

# Part M – Findings Summary

## Task 24 – Write a 400–500 Word Findings Summary

Use evidence from the **core Tasks 1–23**. Optional advanced activities do not need to be included.

Write approximately **400–500 words** in a concise professional style.

### Include the following

- authorised target/application;
- XSS types tested: **reflected, stored and DOM-oriented**;
- key baseline and testing evidence;
- affected parameter or input source;
- whether script execution was actually demonstrated;
- main Browser DevTools or Burp Suite evidence;
- security impact;
- recommended mitigation;
- testing limitations.

### Suggested structure

> XSS testing was conducted against the authorised **DVWA/WebGoat training application** using Browser Developer Tools and Burp Suite. Baseline testing was first performed to establish how the application normally handled user-controlled input before any modified values were introduced.
>
> Reflected and stored XSS testing focused on the relevant **input fields or parameters**. The evidence showed **[briefly describe the observed behaviour]**. The supplied value was **[reflected/stored]** in the application, and the controlled test **[did/did not]** result in browser script execution.
>
> DOM-oriented testing examined how user-controlled input was processed in the browser. Developer Tools showed **[briefly describe the DOM/source-to-sink behaviour]**, and the controlled test **[did/did not]** create or execute active content.
>
> The demonstrated impact was **[briefly state the impact]**. Recommended remediation includes **context-aware output encoding, safe DOM manipulation, server-side input validation, and sanitisation where required**.
>
> Testing was limited to the authorised training applications and designated inputs. Based on the collected evidence, the tested XSS weaknesses were classified as **[confirmed/potential/not validated]**, and no broader impact should be assumed beyond what was directly demonstrated.

### Writing Guidance

Your summary should:

- focus on the **most important evidence** rather than describing every task;
- distinguish **demonstrated impact** from potential impact;
- avoid listing every screenshot or request;
- avoid claims such as session theft or credential compromise unless directly demonstrated.

### Expected Result

A concise professional summary that explains:

**what was tested → what evidence was observed → what was validated → what the impact is → how it should be mitigated.**

# Part N – Advanced XSS Analysis (Optional)

## Task 25 – Compare Reflected XSS Across DVWA Security Levels  
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

# Part O – Additional Advanced XSS Analysis (Optional)

## Task 26 – Analyse XSS Output Contexts  
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

## Task 27 – Trace a DOM Source-to-Sink Flow  
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

## Task 28 – Inspect Content Security Policy as a Defence-in-Depth Control  
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

## Task 29 – Compare Server Response with the Browser DOM  
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

