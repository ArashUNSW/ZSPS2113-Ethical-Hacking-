# Week 10 Lab – Mini Penetration Test (Consolidated Target)

**Target:** Instructor-defined subset of services on the authorised consolidated lab target  
**Suggested tools:** Student-selected **approved** tools (for example, Nmap, curl, Firefox Developer Tools, Burp Suite, Wireshark, and approved vulnerability scanners)  

## Estimated time

Approximately **2 hours for the core practical assessment**, plus additional time for evidence review and the professional report. Complete only the instructor-approved scope; optional tasks require additional time.

---

## Learning Objectives

By the end of this lab, students should be able to:

- Interpret and document a written penetration-testing scope and rules of engagement.
- Select suitable approved tools for a bounded assessment and explain why each is needed.
- Identify accessible hosts, ports, services, application paths, and relevant trust boundaries.
- Establish repeatable baselines before making controlled changes.
- Independently investigate a small number of potential weaknesses without expanding the agreed scope.
- Validate findings with reproducible evidence and distinguish results from scanner suggestions or assumptions.
- Assess demonstrated security impact and assign a justified risk rating.
- Recommend proportionate remediation and define measurable retest criteria.
- Clearly distinguish **implemented remediation** from a **proposed retest**.
- Produce a short professional penetration-testing report including scope, methodology, evidence, risk, remediation, limitations, and retest status.

---

## Scenario

You are acting as a junior penetration tester conducting a **mini assessment** for a client. The client has provided an **authorised consolidated lab target**, but only a **defined subset of its services** is in scope. Your task is to perform an independent, evidence-led assessment within the agreed rules, prioritise one or two meaningful findings, and explain what the client should do next.

**Kali-Attacker** is your testing workstation. **Ubuntu-Server** or another instructor-designated VM hosts the consolidated target. The instructor will supply the actual target address, permitted services, accounts, test window, and any prohibited operations. Do **not** assume earlier labs' ports or applications are automatically in scope.

> **Alert:** Test only the instructor-authorised host, ports, applications, accounts, and techniques. Do not scan university infrastructure, public networks, other students' systems, or any excluded service. No denial-of-service, password spraying, brute forcing, persistence, malware, data exfiltration, unrestricted automated exploitation, or destructive modification. Stop and notify the instructor if an operation risks disrupting the lab.

---

# Part A – Prepare the Engagement and Confirm Scope

## Task 1 – Create the Week 10 Evidence Folder

1. Open a terminal on **Kali-Attacker**.
2. Create and enter the evidence folder:

```bash
mkdir -p ~/lab-evidence/week10
cd ~/lab-evidence/week10
pwd
```

3. Organise files under optional `screenshots/`, `requests/`, and `notes/` subfolders if useful.
4. Avoid placing passwords or unredacted authentication tokens in shared evidence.

### Evidence to capture

Screenshot showing the Week 10 directory and its path.

### Expected result

```text
/home/<your-user>/lab-evidence/week10
```

---

## Task 2 – Read and Record the Rules of Engagement

1. Obtain the **instructor-issued scope** before attempting any assessment activity.
2. Identify the authorised IP/hostname, approved services and ports, applications, test accounts, start/end conditions, and expressly excluded actions.
3. Record permission to use each selected tool; if an activity is not explicitly authorised, **do not perform it**.
4. Note the instructor contact and escalation procedure for unexpected outages or sensitive findings.
5. Write `01-scope.md` with the following table:

| Scope item | Instructor-authorised value |
|---|---|
| Target name/address | |
| Permitted IP/host | |
| Included service/port 1 | |
| Included service/port 2 | |
| Included application/path | |
| Testing account / role (no password) | |
| Approved tools | |
| Allowed testing techniques | |
| Explicit exclusions | |
| Stop and escalation conditions | |
| Test date/window | |

### Knowledge Check

**Why does finding an open port not automatically authorise testing that service?**

### Evidence to capture

Completed scope table without secrets.

### Expected result

A written, unambiguous boundary for the mini penetration test exists **before** reconnaissance begins.

---

## Task 3 – Verify the Consolidated Target and Network Isolation

1. On the authorised server, review the network interfaces and running containers/services, where access is provided:

```bash
ip -br addr
sudo docker ps
```

2. On Kali-Attacker, check your lab interface and routes:

```bash
ip -br addr
ip route
```

3. Compare the network details with the instructor-issued scope. Network addresses and routes alone do **not** establish isolation; confirm host-only or otherwise approved segmentation from the lab configuration or instructor. Do not scan a broader subnet merely to locate the target.
4. If the instructor has permitted a connectivity check, send a **single bounded** request to an in-scope HTTP service. Substitute the authorised address, port and scheme before running the example:

```bash
curl -I --max-time 10 http://<AUTHORISED_IP>:<APPROVED_PORT>/
```

5. Record redirects and failures as observed; `302 Found`, `401 Unauthorized`, or `403 Forbidden` can be normal for a working service. Do **not** follow redirects automatically to an unapproved host/path. If only HTTPS is authorised, use `https://` rather than `http://`.

| Item | Observed value |
|---|---|
| Kali interface/address | |
| Consolidated target address | |
| Confirmed in-scope service | |
| Tested URL (if applicable) | |
| HTTP status / connection outcome | |
| Redirect (if present) | |
| Isolation/scope verified? | Yes / No |

### Evidence to capture

Target confirmation, relevant networking and one authorised connectivity result.

### Expected result

The student verifies the **specific** consolidated target and allowed service without performing broad discovery.

---

# Part B – Independent Reconnaissance and Service Inventory

## Task 4 – Develop a Small Test Plan

1. Select **up to two or three in-scope service features** from the instructor's scope (for example, an HTTP landing page, a designated API route, or an authorised service banner). If only one feature is approved, assess that one and state the limitation.
2. Identify the appropriate approved tool for each check.
3. State what normal behaviour you expect and what evidence will establish it.
4. Identify a safe stopping point and any testing risks.

| In-scope component | Assessment question | Approved tool | Safe test | Evidence expected |
|---|---|---|---|---|
| | | | | |
| | | | | |
| | | | | |

### Knowledge Check

**Why is a narrow, hypothesis-led test plan preferable to running every available tool against the target?**

### Expected result

A small, reproducible plan aligned with the defined subset of services.

---

## Task 5 – Identify Permitted Ports and Service Versions

1. Consult the scope table; form a comma-separated list containing **only explicitly permitted ports**.
2. **Only if the instructor expressly permits active service/version probing**, perform one limited scan against the authorised IP and permitted ports. Service detection sends additional probes, so it must not be treated as harmless or automatically approved:

```bash
nmap -sT -sV --version-light -Pn -p <APPROVED_PORTS> <AUTHORISED_IP> -oN ~/lab-evidence/week10/02-service-inventory.txt
```

3. Replace `<APPROVED_PORTS>` with the explicitly authorised comma-separated TCP ports and `<AUTHORISED_IP>` with the designated IP; do not enter literal angle-bracket placeholders. If service/version probing is not authorised, use the instructor-provided service inventory or normal approved browser observations instead. The `-Pn` flag does not grant permission to probe unlisted services.
4. Record each open, closed, filtered, or unconfirmed service. Do not infer a vulnerability merely from a version string.

| Host | Port/protocol | Observed service | Version/banner | State | Evidence reference |
|---|---|---|---|---|---|
| | | | | | |
| | | | | | |

### Evidence to capture

Scope-compliant inventory output or equivalent authorised observation.

### Expected result

A concise inventory of **in-scope services only**, including uncertainty where identification was inconclusive.

---

## Task 6 – Inspect Service Responses and Headers

1. Select an approved web endpoint from Task 5.
2. Capture an unchanged normal request using the browser or a bounded `curl` request. Substitute the approved address, port, scheme and **exact authorised path**:

```bash
curl -i --max-time 10 http://<AUTHORISED_IP>:<APPROVED_PORT>/<APPROVED_PATH>
```

3. Record status, request path, redirects, response headers, visible page content, and relevant technology disclosures.
4. Inspect only pages or routes identified in the approved scope; do not use broad directory brute forcing or crawling. Do not automatically follow any redirect outside the authorised scope.
5. Distinguish expected diagnostic headers from demonstrable security problems.

| Property | Observation |
|---|---|
| HTTP method/path | |
| Response status | |
| Location/redirect | |
| Server/content-type headers | |
| Cookie names only (if present) | |
| Visible output | |
| Security significance / uncertainty | |

### Evidence to capture

One raw request/response and one browser view, as applicable.

### Expected result

A documented baseline for a chosen authorised service.

---

## Task 7 – Map the Permitted Attack Surface

1. Combine Tasks 5–6 into a simple **attack-surface map**.
2. Show the Kali testing workstation, consolidated target, allowed network boundary, in-scope ports, approved paths, and any login or API boundary.
3. Mark excluded services as **out of scope** without interacting with them.
4. Avoid inventing unseen components or trust relationships.

```text
Kali-Attacker (approved tools)
          |
          v
Authorised consolidated target
          |
    +-----+-----------------+
    |                       |
In-scope service A     In-scope service B
(port/path)             (port/path)
    |                       |
Observed request       Observed response

[All other services: NOT IN SCOPE]
```

### Evidence to capture

One labelled map, with evidence references for included components.

### Expected result

A defensible, bounded representation of the authorised test surface.

---

# Part C – Establish Baselines and Perform Controlled Checks

## Task 8 – Capture a Reproducible Baseline in Burp or an Approved Tool

1. Choose one in-scope request from Task 6.
2. In Burp **Proxy → HTTP history**, locate the request or use another approved request-capture method.
3. If Burp is approved and the captured request is **read-only/idempotent**, send it to **Repeater** and resend **unchanged** once. Otherwise use an approved passive capture or a fresh instructor-authorised training state; do not replay login or state-changing actions merely for baseline reproduction.
4. Record request method, path, permitted parameters, response status, body length, and content.
5. Redact session IDs and credentials in evidence.

| Baseline property | First observation | Unchanged repeat |
|---|---|---|
| Method and path | | |
| Relevant parameter(s) | | |
| Response status | | |
| Body length (same metric) | | |
| Visible result | | |
| Consistent? | | |

### Evidence to capture

The original request and unchanged repeat response.

### Expected result

A stable, safe comparison point exists before any authorised input variation. Any dynamic differences or inability to replay are recorded honestly.

---

## Task 9 – Identify One User-Controlled Input or Access Boundary

1. In an instructor-approved page or API route, identify a normal **non-sensitive** parameter, form input, resource identifier, or authentication boundary.
2. State the application's intended behaviour for this feature.
3. Record input type, normal value, and any browser-side constraints.
4. Classify the test hypothesis (input validation, access control, error handling, or configuration) **without claiming a vulnerability**.

| Item | Observation |
|---|---|
| Endpoint | |
| Input/access boundary | |
| Normal permitted value | |
| Intended behaviour | |
| Proposed benign variation | |
| Reason variation is in scope | |

### Expected result

One narrow, evidence-driven test hypothesis linked to an authorised service.

---

## Task 10 – Test One Benign Input Variation

1. Preserve the unchanged request from Task 8.
2. Change only **one benign variable** explicitly permitted in the lab, such as an instructor-provided non-existent training identifier or harmless marker:

```text
Week10Test
```

3. Submit **once** using the approved browser/Burp interface, **only if the request is safe to repeat**. Do not inject commands, enumerate real accounts, request sensitive files, or alter state without explicit approval.
4. Compare the response to the baseline. A status code alone does not prove success or failure.
5. If the application has no authorised modifiable input, record **Not applicable** and choose an approved non-invasive configuration comparison instead.

| Feature | Baseline | Benign variation |
|---|---|---|
| Method/path | | |
| Input/value | | |
| HTTP status | | |
| Body length | | |
| Visible content/error | | |
| Supported interpretation | | |

### Evidence to capture

Matched before/after requests and responses showing the single changed variable.

### Expected result

Students independently conduct a bounded test and explain the observed difference accurately.

---

## Task 11 – Inspect Authentication and Session Boundaries (Only If In Scope)

1. If the scope provides a training account, inspect normal **logged-out** and **logged-in** behaviour at one permitted page.
2. Use the supplied account only; never guess passwords or attempt other users' accounts.
3. Record HTTP responses, redirect behaviour, and relevant **cookie/token/header names only**. Keep secret values out of shared screenshots and reports (retain valid authentication material in the actual request when needed to test the approved session).
4. Log out normally and attempt the same permitted page again, using a fresh server request. Distinguish a cached page from new protected content returned by the server. Logout does not, by itself, prove token revocation.
5. If no account or authentication feature is authorised, mark this task **Not applicable** and explain why.

| State | Requested resource | Status/redirect | Content observed | Supported conclusion |
|---|---|---|---|---|
| Logged out | | | | |
| Logged in | | | | |
| After logout | | | | |

### Evidence to capture

Redacted before/after request evidence using only the assigned account.

### Expected result

Students describe observed authentication/session behaviour without inferring account compromise.

---

## Task 12 – Check a Designated Access-Control Rule (Only If In Scope)

1. Identify one instructor-approved rule, for example: a training resource accessible to a supplied account but not to an unauthenticated browser.
2. Capture the expected request and response under the permitted state.
3. Repeat only the **authorised read-only comparison** (for example, after logout), using the same endpoint. Inspect actual protected response content—not status alone—and avoid state-changing replays.
4. Record whether the server enforces the rule; merely hiding a navigation link is not sufficient evidence.
5. Do not change resource IDs to other students' objects or attempt privilege escalation outside an instructor-defined scenario.

| Resource/rule | Allowed state result | Disallowed state result | Rule demonstrated? | Evidence |
|---|---|---|---|---|
| | | | Yes / No / Inconclusive | |

### Knowledge Check

**What additional evidence is needed before claiming broken access control?**

### Expected result

A defensible access-control observation—or an explicit statement that the feature was out of scope.

---

## Task 13 – Compare a Second Approved Service or Feature

1. Select a second in-scope service or feature identified in Task 4 **if the instructor approved more than one**. Otherwise record **Not applicable — only one feature authorised** and do not expand scope.
2. Establish an unchanged baseline and, where permitted, one benign comparison.
3. Record what is similar to or different from the first feature.
4. Avoid assuming that one endpoint's behaviour applies to every service.

| Feature | Service/endpoint A | Service/endpoint B |
|---|---|---|
| In-scope purpose | | |
| Baseline status | | |
| Tested variation | | |
| Observed response | | |
| Evidence reference | | |

### Evidence to capture

Labelled observations for the two approved services or features.

### Expected result

Students independently assess a **defined subset**, not an unrestricted network.

---

# Part D – Evidence-Based Interpretation and Risk

## Task 14 – Separate Observation, Interpretation and Assumption

Use evidence from Tasks 5–13. Complete at least **three** rows:

| Observed evidence | Supported interpretation | Unsupported assumption |
|---|---|---|
| An approved service responds on a listed port | The service is reachable from Kali | The service is exploitable |
| An invalid benign value returns an error | The application handles that input differently | Arbitrary file read is possible |
| Logged-out access is redirected to login | The tested route requires authentication in that state | All routes enforce correct authorisation |
| Your observation | | |

### Knowledge Check

**Why does a tool's vulnerability label or a server banner require independent validation?**

### Expected result

Claims are anchored in reproducible evidence rather than assumptions or automated-tool labels.

---

## Task 15 – Assess Demonstrated Impact and Likelihood

1. For each relevant finding, record the affected asset/service and actual observed behaviour.
2. Mark impact as **Observed**, **Not observed**, or **Requires further verification**.
3. Consider confidentiality, integrity and availability **only as far as supported** by evidence.
4. Explain exploit preconditions and exposure without inventing a CVSS score.

| Finding | Confidentiality | Integrity | Availability | Preconditions | Evidence |
|---|---|---|---|---|---|
| | | | | | |

### Expected result

Students explain what the lab actually demonstrated and what remains only a potential impact.

---

## Task 16 – Prioritise Findings with a Justified Risk Rating

1. Select up to **two** evidence-supported findings or validation gaps.
2. Consider exposure, prerequisites, demonstrated impact, and any mitigating control.
3. Assign a qualitative rating (**Low**, **Medium**, **High**) only when evidence and context support risk; justify it in 2–3 sentences. Use **Informational** for observations with no demonstrated exploitable weakness, and label unvalidated concerns **Inconclusive** rather than giving them an unwarranted confirmed severity.
4. A confirmed vulnerability requires direct evidence; a suspected issue should be classified **Inconclusive** or **Requires further verification**.

| Finding | Validated / inconclusive / informational | Exposure/prerequisites | Demonstrated impact | Risk rating | Justification |
|---|---|---|---|---|---|
| | | | | | |

### Expected result

A small prioritised findings list with transparent reasoning—not severity inflation.

---

# Part E – Remediation and Retesting

## Task 17 – Recommend Controls Matched to the Findings

1. Identify the underlying control relevant to each observation (for example, server-side authorisation, safe input handling, secure headers, least privilege, or unnecessary service exposure).
2. State a practical mitigation and whether it is **evidence-based remediation** or a **preventive best practice**.
3. Describe what legitimate functionality the mitigation must preserve.

| Finding / gap | Recommended control | Evidence-linked or preventive? | Expected secure behaviour | Legitimate function preserved |
|---|---|---|---|---|
| | | | | |

### Expected result

Remediation addresses identified behaviour and does not propose unrelated generic changes as if they were findings.

---

## Task 18 – Design a Before-and-After Retest

1. Choose one observation or weakness from Task 16.
2. Preserve the original safe test case, endpoint, account, and comparison metric.
3. Describe the specific **code/configuration change** that would be implemented by an authorised maintainer.
4. Define the expected response after the fix and a check that normal functionality still works. Clearly separate **expected** from **actual observed** after-fix results.
5. Do not claim the issue is fixed merely because the student changed a training security level or tried another endpoint.

| Retest item | Before remediation | Expected after remediation | Actual after remediation, if tested |
|---|---|---|---|
| Endpoint/test input | | | |
| HTTP status | | | |
| Response behaviour | | | |
| Weakness observed? | | | |
| Normal feature still works? | | | |

### Expected result

A measurable verification plan exists for the original finding.

---

## Task 19 – Perform an Authorised Retest or Document a Proposed Retest

1. If the instructor explicitly authorised a fix and a retest, document the actual change, rerun the unchanged benign test and record comparable evidence.
2. Otherwise, write exactly:

```text
Proposed retest only — no code change performed.
```

3. Record any differences and remaining limitations. Do not simulate a successful fix.
4. Never edit or reset the target configuration solely to manufacture a pass result.

| Remediation implemented? | Retest performed? | Actual evidence | Outcome |
|---|---|---|---|
| Yes / No | Yes / No | | Fixed / Not fixed / Not tested / Inconclusive |

### Expected result

An explicit, auditable remediation and retest status is included in the client report.

---

# Part F – Professional Reporting

## Task 20 – Save Required Week 10 Evidence

Use consistent, descriptive filenames in `~/lab-evidence/week10/`. The following is a **suggested deliverable inventory**, not evidence that the files already exist. Create only the files applicable to the approved activities.

```text
01-scope.md
02-service-inventory.txt
03-target-verification.png
04-attack-surface-map.png
05-service-baseline.png
06-burp-baseline.png
07-controlled-comparison.png
08-access-control-check.png
09-risk-and-remediation.md
10-retest-plan.md
11-mini-pentest-report.md
```

For required narrative/table deliverables, create the named Markdown files even when the conclusion is **Not tested** or **No validated finding**. For screenshots or scan outputs, create files only when the activity was actually completed. Remove or redact credentials, tokens, private information and out-of-scope scan output.

**File naming note:** `09-risk-and-remediation.md` is the findings table (Task 21), `10-retest-plan.md` documents the proposed/actual retest (Tasks 18–19), and `11-mini-pentest-report.md` contains the 400–500-word narrative (Task 22).

| Check | Complete? |
|---|---|
| All evidence refers to in-scope systems | Yes / No |
| Requests and responses are readable | Yes / No |
| Sessions/credentials are redacted | Yes / No |
| Each claim has an evidence reference | Yes / No |
| No missing evidence is fabricated | Yes / No |

### Expected result

A clean and traceable evidence set suitable for marking and professional review.

---

## Task 21 – Create the Mini Penetration-Test Findings Table

Create or open the findings table:

```bash
nano ~/lab-evidence/week10/09-risk-and-remediation.md
```

Also create or update the retest record with the before/after table from Tasks 18–19:

```bash
nano ~/lab-evidence/week10/10-retest-plan.md
```

| Finding ID | In-scope asset / endpoint | Baseline | Test / observation | Evidence | Validation status | Risk | Mitigation | Retest status |
|---|---|---|---|---|---|---|---|---|
| F-01 | | | | | | | | |
| F-02 | | | | | | | | |

If **no exploitable weakness** was validated, report appropriate informational findings or a clear negative result rather than inventing vulnerabilities.

### Expected result

Each finding follows the sequence **Scope → Evidence → Supported interpretation → Risk → Mitigation → Retest**.

---

## Task 22 – Draft the Professional Mini Penetration-Test Report

Create:

```bash
nano ~/lab-evidence/week10/11-mini-pentest-report.md
```

Write a **400–500-word narrative**, excluding headings, tables and figure captions. Use the observations from the findings table, not fabricated findings. Include:

1. **Scope and authorisation:** exact target and allowed subset of services.
2. **Methodology:** approved tools, baseline checks, and bounded controlled testing.
3. **Evidence and validation:** key request/response or service observations and whether a vulnerability was confirmed.
4. **Risk:** demonstrated impact, preconditions and justified severity.
5. **Remediation:** controls directly related to findings.
6. **Retest:** whether remediation was actually implemented and retested or only proposed.
7. **Limitations:** excluded services, missing credentials, time, or tests not performed.

### Suggested structure

> A mini penetration test was conducted against the authorised **[consolidated target and address]**, limited to **[approved service/port/path subset]**. Reconnaissance with **[approved tools]** identified **[observed services]**, and baseline requests established **[normal behaviour]**.
>
> The controlled assessment focused on **[tested features]**. Comparison of **[baseline]** with **[benign variation or authorised access state]** showed **[observed result]**. Evidence from **[request captures, output, screenshots]** supports **[finding or negative result]**.
>
> The demonstrated impact was **[evidence-supported impact]**, while **[potential wider impact]** was not verified. The finding was assigned **[risk rating]** because **[exposure, prerequisites and impact justification]**.
>
> Recommended remediation includes **[specific control]**, with legitimate functionality preserved through **[verification check]**. Remediation was **[implemented and retested / not implemented]**, and the retest **[result or proposed method]** was recorded.
>
> Testing was restricted to **[scope and limitations]**. The overall assessment **[validated / did not validate]** an exploitable weakness in the tested subset; no conclusion is drawn about excluded services or the broader environment.

### Writing guidance

Check the narrative count with `wc -w ~/lab-evidence/week10/11-mini-pentest-report.md` as a preliminary check; remove headings/tables/captions from the count when present. Focus on the **most important evidence**, not a chronological diary. Do not report untested weaknesses as confirmed or state that remediation was successful without an actual verified change.

### Expected result

A short professional report with **scope, evidence, risk, remediation and retest**.

---

## Task 23 – Perform a Quality-Assurance Review

Check the report against the evidence and complete:

- [ ] Exact target, approved services and exclusions are stated.
- [ ] Selected tools were instructor-approved.
- [ ] Service inventory contains only the defined scope.
- [ ] All authorised features are documented; at least two baselines where more than one feature was approved, otherwise the one-feature limitation is stated.
- [ ] A controlled comparison and its actual response are included.
- [ ] Evidence is labelled, readable and redacted.
- [ ] Findings distinguish observations from assumptions.
- [ ] Risk ratings have a clear rationale.
- [ ] Remediation maps to observed behaviour.
- [ ] Retest is clearly performed **or** explicitly proposed.
- [ ] Limitations and negative results are reported fairly.
- [ ] Narrative is **400–500 words**.

### Expected result

The report is internally consistent and every substantive security claim has a supporting reference.

---

## Task 24 – Restore the Lab and Submit

1. Stop any approved intercepting or capture sessions.
2. Restore the application or training account to the instructor-specified state, only where authorised.
3. Remove only your own temporary test material if explicitly permitted; do not delete unrelated files, logs or containers.
4. Verify final evidence filenames with:

```bash
ls -lh ~/lab-evidence/week10/
```

5. Submit the professional report, findings table, scope table, attack-surface map, selected screenshots and retest plan/evidence **as required by the instructor**. Document any permitted activity that was unavailable or not applicable.
6. Do not include live credentials, session identifiers, internal secrets or out-of-scope output.

### Expected result

The authorised mini test is concluded and a concise, professional evidence package is ready for marking.

---

# Part G – Advanced Mini Penetration-Test Analysis (Optional)

## Task 25 – Compare Two In-Scope Service Exposures  
**Advanced Lab, Optional**

### Purpose

Evaluate whether two instructor-approved services present different observable security boundaries without broadening the scope.

### Steps

1. Select two services already identified as in scope. If fewer than two are approved, mark this optional task **Not applicable**; do not expand scope.
2. Compare permitted access methods, authentication requirements and information returned.
3. Note differences in exposure and prerequisites.
4. Do not treat a difference in a banner or response status alone as a confirmed vulnerability.

| Feature | Service A | Service B |
|---|---|---|
| Authorised port/path | | |
| Authentication needed | | |
| Observable disclosure | | |
| Access restriction | | |
| Potential risk / caveat | | |

### Evidence to capture

Two matching request/response or service observations.

### Expected result

A justified comparison of two services within the agreed scope.

---

## Task 26 – Trace One Client-to-Server Request  
**Advanced Lab, Optional**

### Purpose

Trace the complete flow of one safe user action through a selected approved application endpoint.

### Steps

1. Choose a harmless, authorised page action.
2. Use Browser DevTools and Burp to identify the triggering request, response and browser-side result.
3. Identify inputs, session boundary (if relevant), and server response.
4. Create a small **user action → HTTP request → server response → client result** diagram.
5. Avoid asserting any unseen backend component exists.

| Stage | Observed value | Evidence |
|---|---|---|
| Client action | | |
| Request | | |
| Response | | |
| Browser result | | |
| Security control observed | | |

### Evidence to capture

Request trace and diagram.

### Expected result

A grounded, end-to-end model of one assessed application feature.

---

## Task 27 – Investigate a False Positive or Inconclusive Result  
**Advanced Lab, Optional**

### Purpose

Improve professional judgement by examining why a potential weakness could not be validated.

### Steps

1. Select one scanner suggestion, response anomaly, or negative result from the core lab.
2. State the original hypothesis and what evidence would prove it.
3. Review the existing captures and perform **at most one additional authorised harmless comparison** if needed.
4. State whether the result is confirmed, not validated or inconclusive.
5. Explain the evidence gap and avoid inventing an exploit.

| Hypothesis | Supporting evidence | Contradicting/absent evidence | Final classification |
|---|---|---|---|
| | | | |

### Expected result

A professional, evidence-based treatment of uncertainty.

---

## Task 28 – Design a Regression Test for a Finding  
**Advanced Lab, Optional**

### Purpose

Convert one remediation recommendation into a safe, repeatable verification procedure.

### Steps

1. Select a validated issue or a preventive configuration recommendation.
2. Record the baseline request, current response and expected secure response.
3. Include one negative check and one normal-functionality check.
4. State who would implement the fix and how evidence would be captured.
5. If no fix was applied, write **“Proposed retest only — no code change performed.”**

| Test case | Current behaviour | Expected after fix | Evidence needed |
|---|---|---|---|
| Negative/security check | | | |
| Legitimate-function check | | | |

### Expected result

An actionable retest plan that verifies security improvement while preserving legitimate behaviour.

---

# Troubleshooting

## Problem – Target Unavailable

Check the exact scope, host address, allowed port, application status and network route. Use only the permitted connectivity check; **do not scan unrelated addresses or ports**.

## Problem – Burp Does Not Capture a Request

Check the authorised browser proxy configuration, Burp listener and **Proxy → HTTP history**. Do not disable lab security controls just to force traffic through a tool.

## Problem – Normal Requests Produce Redirects or Errors

Confirm the correct permitted path and training account state. A `302`, `401` or `403` may be normal. Compare actual response content and redirect destination rather than assuming the test failed.

## Problem – No Vulnerability Found

Do not invent one. Document the services tested, negative results, controls observed and limitations. A technically accurate **no validated finding** result is acceptable.

---

# Knowledge Check

1. What is the difference between scope and an attack-surface inventory?
2. Why must tool selection comply with the rules of engagement?
3. Why does an open port not prove a vulnerability?
4. Why is it important to capture a normal baseline?
5. Why should one controlled variable be changed at a time?
6. What evidence distinguishes an information disclosure from a suspected risk?
7. What is the difference between authentication and authorisation?
8. Why must a severity rating consider exposure, prerequisites and demonstrated impact?
9. What should a tester do if a service is out of scope but reachable?
10. Why is an absence of a validated finding still worth reporting?
11. What makes a remediation recommendation actionable?
12. Why does a change in training security level not prove a code-level fix was implemented?
13. What is a regression test?
14. Why are scope limitations essential in a professional report?
15. Which evidence should be redacted before submitting assessment screenshots?

---

# Summary

This mini penetration test uses the following workflow:

**Authorisation → Scope → Test Plan → Reconnaissance → Baseline → Controlled Validation → Evidence → Risk → Remediation → Retest → Professional Report**

Students independently examine a **defined subset of services** on the consolidated lab target using only approved tools and techniques. The final deliverable is a concise, evidence-supported report that identifies what was tested, what was actually observed, what risk is justified, what should be fixed, and whether the proposed control was implemented and retested.

Optional activities deepen this work through cross-service comparison, request-flow tracing, false-positive review and regression-test design.


