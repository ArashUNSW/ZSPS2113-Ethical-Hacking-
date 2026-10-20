# Week 8 Lab – Modern Application Testing with OWASP Juice Shop

**Authorised target:** Instructor-provided OWASP Juice Shop on Ubuntu-Server  
**Testing workstation:** Kali-Attacker  
**Tools:** Firefox Developer Tools and **either** Burp Suite Community Edition **or** OWASP ZAP  

## Learning objectives

By completing the lab, students will be able to: 
- Document SPA browser-to-API requests
- Investigate authentication and logout 
- Map actual endpoints
- Compare authorised and unauthenticated access
- Assess client-side routing and one benign business rule
- Report only impacts demonstrated by evidence

## Important
Test only the assigned Juice Shop instance and instructor-approved accounts. Do not use public demo servers, production systems, another student's records, or guessed administrative paths. Avoid brute force, credential stuffing, destructive actions, automated crawling, payment/checkout, and high-volume testing. Use low-volume manual requests and fictitious account information. Where a response unexpectedly reveals another user's data, do not explore further; redact, preserve minimal evidence and report it to the instructor.

> **Accuracy reminder:** Juice Shop versions and interfaces differ. Endpoint names, login responses, authentication storage, roles, and error statuses must be **observed**, not assumed. No particular vulnerability is guaranteed to appear in a configured instance.

## Part A – Prepare the Week 8 Environment

### Task 1 – Create the Week 8 Evidence Folder

1. Start Kali-Attacker and Ubuntu-Server using the authorised lab environment.
2. Use the actual Ubuntu-Server IP and mapped port. An **illustrative** URL is `http://192.168.1.100:3000/`; it is not a required address.
3. Select **one interception tool** for the lab: **Burp Suite → Proxy → HTTP history / Repeater**, or **ZAP → History / Requester (Manual Request Editor)**. The Firefox **Network**, **Storage**, **Inspector**, and **Debugger/Sources** panels are also used.
4. Create a Week 8 evidence folder:

```bash
mkdir -p ~/lab-evidence/week8
```

3. Change into the folder:
```bash
cd ~/lab-evidence/week8
```

4. Confirm the current path:
```bash
pwd
```

### Evidence to capture

Take one screenshot showing the Week 8 evidence folder.

### Expected result

```text
/home/<your-user>/lab-evidence/week8
```

---
# Part A – Authorised Environment and Browser Baselines

## Task 1 – Verify the authorised Juice Shop target

**Objective:** Confirm the service, isolation and initial HTTP behaviour before any testing.

### Step 1 – Confirm the target VM

1. On **Ubuntu-Server**, run `ip -br addr` and record the IP of the assigned lab interface.
2. Run `sudo docker ps` to identify the running Juice Shop container, container image, status and published port. If missing/unhealthy, follow the earlier lab setup and ask the instructor; do not substitute a public Juice Shop instance.
3. On Kali-Attacker, confirm its assigned lab IP using `ip -br addr`. Record how the machines are connected and whether the lab is isolated.

### Step 2 – Load the application

1. Open Firefox on Kali-Attacker and visit the **authorised** Juice Shop address (example only: `http://192.168.1.100:3000/`).
2. Check the home page displays and record the final browser URL. Do not log in yet.
3. Press **F12**, select **Network**, enable **Persist Logs**, and reload once.
4. Select the document request and record method, path, status, redirect `Location` (if any) and final page. Observe that later SPA navigation may not reload the document.

### Step 3 – Select your proxy

1. Start either Burp Suite or ZAP and configure a lab browser to use it. Confirm the proxy history records your request to the authorised Juice Shop host.
2. Do not enable active scanners or automated spidering. Record which proxy will be used for all subsequent tasks.

### Results table

| Item | Actual observation |
|---|---|
| Kali-Attacker IP / lab interface | |
| Ubuntu-Server IP / lab interface | |
| Juice Shop URL and published port | |
| Docker image / container status | |
| Initial request method/path/status | |
| Redirect, if any | |
| Proxy selected | |
| Lab isolation confirmation | |

### Interpretation questions

1. What evidence confirms you reached the **assigned** container?
2. Was an initial redirect seen? If not, record “Not observed.”

**Evidence to capture:** Docker status, browser URL, initial HTTP headers and completed environment table.

**Expected outcome:** The authorised target and scope are explicitly verified.

---

## Task 2 – Establish a public browsing and API baseline

**Objective:** Distinguish ordinary SPA navigation, static assets and client-visible API requests.

### Step 1 – Browse without authentication

1. Open a new private browser session; confirm you are signed out.
2. Browse **home/products**, one **product detail** page and the **login page**, using only visible links. Do not add items to someone else’s basket or checkout.
3. In Firefox Network filter **Fetch/XHR**, then look separately at **Doc**, **JS**, and **CSS** traffic to distinguish API calls from static resources.

### Step 2 – Inspect two baseline requests

1. Select at least two read-only requests generated by normal browsing (such as a product list or product detail).
2. Record the **actual** method, endpoint, parameter names, status, response media type and a short summary of response data.
3. Locate the same requests in Burp Proxy HTTP history or ZAP History. Save one unchanged read-only request and, if authorised, submit it once in Repeater/Requester.
4. Record whether the repeated response is consistent; differences in dynamic data are not automatically vulnerabilities.

### Results table

| UI action | Request method | Endpoint | Parameter names | HTTP status | Response type/content | Public? |
|---|---|---|---|---|---|---|
| Product list | | | | | | |
| Product detail | | | | | | |
| Login page | | | | | | |

### Interpretation questions

1. Which actions generated HTTP API requests, rather than only client-side routing?
2. Why does endpoint visibility alone not demonstrate a security weakness?

**Evidence to capture:** Public browser pages, two redacted request/response captures and completed table.

**Expected outcome:** A reproducible, unauthenticated API/navigation baseline.

---


# Part B – Authentication and Session Behaviour


## Task 3 – Compare unsuccessful and successful authentication

**Objective:** Document login behaviour and the observable session mechanism using one authorised account.

### Step 1 – Prepare the login baseline

1. Ask the instructor for the approved test account or register only if expressly permitted.
2. Open the visible login page and keep Firefox **Network** and your proxy history available.
3. Identify the actual login request endpoint and method; avoid assuming a path or response format.

### Step 2 – Perform one incorrect login

1. Enter the approved account identifier with **one** deliberately incorrect fictitious password.
2. Record the request method/path, HTTP status, error message and whether protected account content becomes accessible.
3. Do not repeat failed login attempts or use password lists.

### Step 3 – Perform one successful login

1. Submit the correct credentials for the same approved account.
2. Capture the request/response and the resulting page state. Do not include the password or returned secret values in screenshots.
3. Open Firefox **Storage** to determine whether relevant cookies, local storage or session storage entries are present. Record **key names and storage type only**.
4. Navigate between two authorised pages and observe whether credentials are resent or existing authentication material is reused.

### Results table

| Observation | Incorrect login | Correct login |
|---|---|---|
| Method / endpoint | | |
| Status / UI message | | |
| Protected page accessible? | | |
| Storage mechanism observed | | |
| Follow-up request behaviour | | |

### Interpretation questions

1. Does the presence of a token in browser storage alone prove the server accepted it?
2. What evidence distinguishes an authentication failure from a success?

**Evidence to capture:** One unsuccessful and one successful login capture, redacted storage panel and comparison table.

**Expected outcome:** Observed login/session behaviour without unsupported claims about server implementation.

---


## Task 4 – Evaluate logout and unauthenticated replay

**Objective:** Determine whether the server serves a protected *read-only* resource without authentication.

### Step 1 – Capture your own protected baseline

1. While signed in, choose a normal **read-only** account-specific UI page. Avoid account changes or purchases.
2. Capture the corresponding HTTP request, response status and safe indication of your own account context.
3. Keep a copy of the request as an unchanged baseline in the proxy.

### Step 2 – Perform normal logout

1. Use the application’s Logout action. Observe any logout request and browser storage change.
2. Revisit the protected page using the UI and record whether the content is served from a new response or browser cache.

### Step 3 – Compare absent authentication

1. For the **same read-only** request, make a controlled proxy copy and remove only the Authorization header or session cookie as applicable. Do **not** change object IDs.
2. Send once, then record status and whether actual protected data was returned. A public error page with HTTP 200 is **not** a validated bypass.
3. If unexpected private data appears, stop and retain only a minimal redacted record.

### Results table

| Test | HTTP status | Content returned | Supported conclusion |
|---|---|---|---|
| Signed-in baseline | | | |
| UI after logout | | | |
| Same read-only request without auth | | | |

### Interpretation questions

1. Was protected content delivered by a new server response or merely displayed from cache?
2. Does logout demonstrate token revocation, or only a UI/storage change?

**Evidence to capture:** Logout request/state, before/after protected response and comparison table.

**Expected outcome:** Evidence-based server-side access observation, with remaining uncertainty documented.

---


# Part C – API Mapping and Access-Control Testing


## Task 5 – Construct a client-visible API inventory

**Objective:** Build an endpoint map from actual interactions, not guessed URLs.

### Step 1 – Observe distinct application functions

1. Use normal UI navigation through home, catalogue, search, product details, login and your own account where authorised.
2. Filter Firefox Network to **Fetch/XHR** and locate at least six distinct requests where available.
3. Exclude third-party traffic and routine duplicate requests unless they carry different useful actions.

### Step 2 – Record request structure

1. For each endpoint capture method, actual path, query/body **parameter names**, status and a brief non-sensitive response description.
2. Classify public/authenticated/uncertain using your observations. Do not infer authorisation from route names.
3. Cross-reference each with its Burp/ZAP history entry and a labelled evidence filename.

### Results table

| ID | UI action | Method | Observed endpoint | Parameter names | Status | Auth context | Evidence |
|---|---|---|---|---|---|---|---|
| E1 | | | | | | | |
| E2 | | | | | | | |
| E3 | | | | | | | |
| E4 | | | | | | | |
| E5 | | | | | | | |
| E6 | | | | | | | |

### Interpretation questions

1. Which endpoints are demonstrably public?
2. Which endpoints require further testing before their access requirements can be classified?

**Evidence to capture:** Endpoint inventory and labelled Network/proxy records.

**Expected outcome:** An endpoint list grounded in the SPA’s observed activity.

---


## Task 6 – Compare access with and without authentication

**Objective:** Check whether the server enforces authentication for two selected read-only endpoints.

### Step 1 – Choose requests

1. From Task 5 choose one apparently public read-only endpoint and one read-only endpoint involving your own account, if available.
2. Capture their normal requests and save unchanged copies.

### Step 2 – Make one-variable comparisons

1. In Repeater or ZAP Requester, duplicate a request and remove only applicable authentication material. Keep path, method, safe parameters and account context unchanged.
2. Send each modified **read-only** request once; record status, data shape and whether account-specific information remains accessible.
3. Compare the server responses rather than relying only on status codes or visible UI behaviour.

### Step 3 – Classify cautiously

1. If no protected data is returned, state “No issue observed for this test,” not “all access control is secure.”
2. If your own previously restricted data is returned without the required authentication, stop repeat testing and record minimally as a possible validated issue subject to intended permissions.

### Results table

| Endpoint | Normal response | Without-auth response | Data visibility | Interpretation |
|---|---|---|---|---|
| Public read-only | | | | |
| Own-account read-only | | | | |

### Interpretation questions

1. What does `401`, `403`, a redirect or `200` mean **in this specific response**?
2. Which server-provided field indicates whether restricted data was exposed?

**Evidence to capture:** Paired redacted request/response captures and comparison table.

**Expected outcome:** A limited, justified assessment of request-level authentication enforcement.

---


## Task 7 – Perform approved object-level authorisation comparison (conditional)

**Objective:** Assess ownership checks without accessing uninvolved users or enumerating IDs.

### Step 1 – Confirm prerequisite authorisation

1. Proceed **only** if the instructor expressly provides two isolated test accounts (A and B) and specifically approves cross-account object comparisons.
2. If not supplied, record **“Not performed – second authorised account or permission unavailable”** and continue to Task 8. There is no penalty for this safety condition in the core task.

### Step 2 – Capture each owner baseline

1. Sign in as A and observe one approved read-only object belonging to A. Capture its exact identifier from the ordinary UI request.
2. Sign in as B and capture the equivalent approved read-only object belonging to B. Ensure no real student data is used.

### Step 3 – Check a single controlled access

1. With B authenticated, duplicate B’s read-only request, replacing only the object identifier with A’s **specifically approved** object ID.
2. Submit once, record status and the minimum redacted response shape. Stop if any unauthorised content is disclosed.
3. Do not guess IDs, modify objects, attempt role changes or delete records.

### Results table

| Requesting account | Object owner | Response/status | Authorised for account? | Evidence |
|---|---|---|---|---|
| A | A | | | |
| B | B | | | |
| B | A (expressly approved test object) | | | |

### Interpretation questions

1. Was an ownership check demonstrably enforced by the server?
2. Why is merely seeing an object ID in a URL insufficient to validate a broken-object-level-authorisation issue?

**Evidence to capture:** Instructor permission note and three minimally redacted comparisons, or a “Not performed” statement.

**Expected outcome:** A tightly scoped object-level access finding or explicitly recorded limitation.

---


# Part D – Client-Side Discovery and Business-Logic Assessment


## Task 8 – Map client-side routes to observed API requests

**Objective:** Explain how visible SPA routes, scripts and actual backend calls relate.

### Step 1 – Observe routes and scripts

1. In Firefox open **Inspector**, **Debugger/Sources**, and **Network**.
2. Navigate through at least three visible screens (for example product list, search, own account). Record any changed browser route and whether a document reload occurred.
3. Note loaded JavaScript filenames and publicly exposed application configuration **without searching for secrets** or guessing hidden administrative routes.

### Step 2 – Link actions to endpoints

1. For each screen, identify an ordinary UI action and an actual request it generated in Fetch/XHR.
2. Record the request method/status and whether authentication was present.
3. Distinguish a hidden or disabled button from an independently verified server-side authorisation rule.

### Step 3 – Draw a simple map

Use a Markdown code block or diagram editor to produce a screen → action → observed endpoint → response diagram. Example:

```text
Product list --(normal browsing)--> [captured endpoint] --> [status]
Search ------(search term)-------> [captured endpoint] --> [status]
Own account -(read-only view)----> [captured endpoint] --> [status]
```

### Results table

| Screen/route | UI action | Full page reload? | Captured endpoint/method | Status | Auth context |
|---|---|---|---|---|---|
| | | | | | |
| | | | | | |
| | | | | | |

### Interpretation questions

1. What is the difference between a visible client route and a server-authorised resource?
2. Does bundled JavaScript mentioning an endpoint mean the endpoint is vulnerable?

**Evidence to capture:** At least three observed mappings, route evidence and diagram.

**Expected outcome:** A grounded client-side-to-API relationship map.

---


## Task 9 – Document one harmless business-logic workflow

**Objective:** Establish expected behaviour of a visible application rule using only benign UI actions.

### Step 1 – Select an allowed workflow

1. Choose one safe workflow: product search, benign category/filter selection or another **read-only** display option.
2. Do not submit purchases, redeem coupons, alter another user’s basket, or make transactions.
3. State the expected rule, such as “searching for a valid product term returns the matching catalogue items.”

### Step 2 – Record the baseline

1. Perform one ordinary UI input and record the resulting view.
2. Capture the actual HTTP request and response, where present, including method, parameter name, status and safe result summary.

### Step 3 – Make one benign UI variation

1. Change a single normal value (such as a different ordinary search term).
2. Record resulting UI and request/response. Keep other conditions unchanged.
3. Explain whether the behaviour follows the apparent UI rule and what server-side checks remain untested.

### Results table

| Workflow/rule | Baseline input/result | Benign variation/result | Endpoint | What was observed? |
|---|---|---|---|---|
| | | | | |

### Interpretation questions

1. Was the rule enforced by the server, the browser, or is this uncertain?
2. Why does a surprising search result not automatically constitute a business-logic exploit?

**Evidence to capture:** Two labelled workflow states, proxy evidence if an API call occurred, and completed table.

**Expected outcome:** A limited business-rule baseline supported by direct evidence.

---


## Task 10 – Compare one client-side control with server responses

**Objective:** Use a single harmless read-only parameter modification to test an observable business rule.

### Step 1 – Choose a suitable request

1. From Task 9 choose an ordinary read-only request that includes a non-sensitive, user-controlled input (for example a search term).
2. Send an unchanged copy to Repeater/ZAP Requester and capture baseline response.

### Step 2 – Make one controlled change

1. Alter only the benign parameter to a different ordinary UI-allowed value. Do not introduce SQL/XSS payloads, monetary fields, privilege flags or unauthorised IDs.
2. Submit once and record response status, content type and the change in returned content.

### Step 3 – Compare and interpret

1. Compare server results to the browser UI rule.
2. State exactly what the single test shows; note any limitation.
3. Return to the normal UI value; do not leave altered records or settings.

### Results table

| Property | Unchanged baseline | One benign variation |
|---|---|---|
| Method and endpoint | | |
| Parameter/value (non-sensitive) | | |
| HTTP status | | |
| Result summary | | |
| Rule supported? | | |

### Interpretation questions

1. Does the response demonstrate server-side validation, or only data retrieval?
2. What additional permission would be needed to examine a state-changing rule?

**Evidence to capture:** Baseline/modified request pair and short interpretation.

**Expected outcome:** A controlled, low-impact server response comparison with realistic limitations.

---


# Part E – Reporting, Evidence Review and Restoration


## Task 11 – Create the endpoint and access-control map

**Objective:** Consolidate the application endpoints and evidence into a reviewable Markdown deliverable.

### Step 1 – Create the file

On Kali, execute:

```bash
nano ~/lab-evidence/week8/09-endpoint-map.md
```

### Step 2 – Consolidate direct observations

1. Use Task 5’s actual endpoint inventory and Task 8’s UI→API mappings. Aim for six distinct requests **where available**; justify fewer without inventing endpoints.
2. For each request record method, path, safe parameter names, authentication context, response status, access comparison and evidence filename.
3. Reference account-specific and optional cross-account results only if tested under the stated restrictions.

### Step 3 – Classify findings

1. Use **Validated weakness**, **Inconclusive**, **No issue observed in tested case**, or **Not tested**.
2. An endpoint being discoverable, a UI hiding a button or `200 OK` alone does not validate broken access control.

### Results table

| ID | UI action / route | Endpoint / method | Input parameters | Auth context | Baseline status | Controlled comparison | Finding classification | Evidence |
|---|---|---|---|---|---|---|---|---|
| E1 | | | | | | | | |
| E2 | | | | | | | | |
| E3 | | | | | | | | |
| E4 | | | | | | | | |
| E5 | | | | | | | | |
| E6 | | | | | | | | |

### Interpretation questions

1. Are findings tied to the actual response content?
2. Are any access claims based solely on an endpoint name or route?

**Evidence to capture:** Completed `09-endpoint-map.md` and linked redacted captures.

**Expected outcome:** An evidence-based endpoint/access-control map.

---


## Task 12 – Assess security impact and recommend remediation

**Objective:** Separate demonstrated results, potential risks and proposed controls.

### Step 1 – Identify supported findings

1. Review Tasks 3–10 and list only demonstrated behaviour.
2. Identify whether it affected access to **your** test-account content, session state, public data or a benign workflow.
3. Record potential impacts separately; avoid treating unperformed cross-account tests as confirmed IDOR/BOLA.

### Step 2 – Select proportionate controls

1. For authentication/session concerns consider server-side checks, appropriate token expiry/invalidation and secure cookie handling.
2. For access controls consider per-request authentication, per-object ownership/authorisation and deny-by-default design.
3. For SPA discovery consider keeping secrets out of client bundles; for business logic, server-side rule validation. Include rate limits or generic errors only where relevant.
4. Mark each control **Evidence-based remediation** or **Preventive best practice**.

### Step 3 – Define verification

1. Choose one relevant read-only baseline that can be compared before and after a hypothetical fix.
2. Describe the same account context, endpoint, auth state, expected denial and legitimate positive regression test.
3. If no code change was performed, explicitly write **“Proposed retest only — no code change performed.”**

### Results table

| Observation/evidence | Demonstrated impact | Potential risk (untested) | Recommended control | Type | Actual/proposed retest |
|---|---|---|---|---|---|
| | | | | | |

### Interpretation questions

1. Which impacts are actually demonstrated?
2. How would you show a fix works without accidentally testing outside the lab scope?

**Evidence to capture:** Completed interpretation/remediation table and one proposed or actual retest description.

**Expected outcome:** Actionable, evidence-linked recommendations without overclaiming.

---


## Task 13 – Write a 400–500-word professional Findings Summary

**Objective:** Summarise authentication, API discovery, access controls and benign business logic concisely.

### Step 1 – Create the file

Run:

```bash
nano ~/lab-evidence/week8/10-findings-summary.md
```

Use **400–500 narrative words**, excluding headings, tables and captions.

### Step 2 – Use the five-paragraph template

**Paragraph 1:** Name authorised Juice Shop address, testing tools, baseline public browsing and endpoint map.

**Paragraph 2:** Describe one failed and one successful login, the observed authentication material type (never values), logout and read-only no-auth comparison.

**Paragraph 3:** Summarise mapped endpoints, one public-versus-auth comparison and object-level test **only if authorised/performed**.

**Paragraph 4:** Map client-side routes to actual API calls; report one harmless business-logic comparison, demonstrated impact and fitting remediation.

**Paragraph 5:** State actual versus proposed retesting, limitations and whether an access-control or business-logic vulnerability was validated.

### Step 3 – Check technical accuracy

1. Link every material statement to Task 11’s evidence map or an explicitly named screenshot.
2. Differentiate **status codes** from returned protected content, **client storage** from server-side validation, and **potential** from **demonstrated** impact.
3. If fewer than six endpoints were accessible, disclose the limitation. If Task 7 was not approved, say so rather than inventing cross-account evidence.

### Step 4 – Check word count

Run:

```bash
wc -w ~/lab-evidence/week8/10-findings-summary.md
```

If the file has headings or tables, check the narrative separately; revise to 400–500 words.

### Interpretation questions

1. Does the summary explicitly state whether any vulnerability was validated?
2. Does it distinguish an ordinary unauthenticated response from an authorisation failure?

**Evidence to capture:** Completed Markdown findings summary and checked narrative word count.

**Expected outcome:** A concise professional security findings summary based on actual lab evidence.

---


## Task 14 – Verify report evidence and submit

**Objective:** Quality-check the assessment before upload to Moodle or the instructor-specified location.

### Step 1 – Review your deliverables

1. Confirm `09-endpoint-map.md` and `10-findings-summary.md` exist and open correctly.
2. Check screenshots are labelled, legible, correctly matched to endpoints and contain **no unredacted secret values**.
3. Ensure optional Task 7 is explicitly marked performed/not performed, and all claims have direct supporting evidence.

### Step 2 – Complete the checklist

Mark the checklist below honestly; record any unavailable features as limitations rather than generating artificial evidence.

### Step 3 – Submit

1. Submit the two Markdown files and selected screenshots using the course submission method.
2. Do not upload raw HAR files or proxy projects if they expose tokens or private content; use sanitised extracts instead.

### Results table

| Check | Done? |
|---|---|
| Authorised URL, host, Docker state and scope recorded | [ ] |
| Unauthenticated browsing baseline captured | [ ] |
| Failed/successful login and logout tested | [ ] |
| At least six observed endpoints mapped, or limitation stated | [ ] |
| Public and protected read-only comparisons included | [ ] |
| Cross-account testing authorised or clearly not performed | [ ] |
| Three client-side UI→API mappings included | [ ] |
| One benign business rule compared | [ ] |
| Evidence legible and secret values redacted | [ ] |
| Findings distinguish confirmed / inconclusive / untested | [ ] |
| Remediation and actual/proposed retest status explicit | [ ] |
| Findings Summary 400–500 narrative words | [ ] |
| Lab restoration or cleanup documented | [ ] |

**Evidence to capture:** Completed quality checklist and final file listing.

**Expected outcome:** Submission ready with traceable evidence and careful conclusions.

---


## Task 15 – Restore the authorised lab state

**Objective:** Return application and testing tools to their agreed settings without affecting shared data.

### Step 1 – End your session

1. Use the normal Juice Shop logout function for each approved test account.
2. Stop interception where no longer required and close the lab browser session.

### Step 2 – Restore only what you changed

1. Restore browser proxy configuration and any temporary test settings.
2. Do not delete records, reset shared databases or stop instructor-managed containers without explicit authorisation.
3. Remove test objects only if the instructor specifically approves and you can identify your own objects safely.

### Step 3 – Record actual cleanup

1. Confirm no request replay or automated testing is still running.
2. List your evidence folder using `ls -lh ~/lab-evidence/week8/`.
3. Write a short cleanup note describing exactly what was done and what was not performed.

### Results table

| Cleanup item | Completed / Not applicable | Notes |
|---|---|---|
| Logged out | | |
| Browser proxy restored | | |
| Temporary setting restored | | |
| Authorised test data cleanup | | |
| Lab services left as instructed | | |

**Evidence to capture:** Brief restoration record and evidence-folder listing.

**Expected outcome:** No ongoing tests and accurately documented restoration.

---


# Part F – Advanced Optional Tasks

**These tasks are optional.** Complete them only when the instructor specifically authorises the necessary accounts, objects and testing conditions; they are not prerequisites for the core lab report.


## Task 16 – Compare authorised role-specific access

**Objective:** Observe server-side role enforcement using only instructor-provided accounts and a named, permitted read-only function.

### Step 1 – Confirm permission

1. Obtain two deliberately different instructor-issued roles and a specifically approved, read-only endpoint.
2. If roles or approval are unavailable, record “Not performed – required roles/approval unavailable.”

### Step 2 – Capture role baselines

1. Sign in to role A and capture the approved request/response. Record method, path, status and non-sensitive returned fields.
2. Repeat with role B using its own session; do not copy credentials between accounts.

### Step 3 – Assess differences

1. Compare which response fields or content are accessible.
2. Do not alter roles, guess admin endpoints or attempt privilege escalation.
3. If an unexpected restricted response appears, stop and preserve minimal redacted evidence.

### Results table

| Role | Approved endpoint | HTTP status | Safe response summary | Expected permission | Observation |
|---|---|---|---|---|---|
| A | | | | | |
| B | | | | | |

### Interpretation questions

1. Is the restriction demonstrably server-side?
2. What additional test would require explicit new authorisation?

**Evidence to capture:** Instructor-supplied role scope and two labelled, redacted requests.

**Expected outcome:** A cautious role-comparison finding or “Not performed” outcome.

---


## Task 17 – Compare fresh login, logout and session state

**Objective:** Document whether observable authentication material and server responses change across normal session transitions.

### Step 1 – Record a protected baseline

1. With your own authorised account signed in, capture a single read-only protected request.
2. Record authentication mechanism/key names but redact token and cookie values.

### Step 2 – Normal logout and reauthentication

1. Log out through the UI and attempt ordinary access to the same page.
2. Log back in with the same approved account, then capture the same protected read-only request.
3. Compare whether browser storage keys, redacted token fingerprints (locally only) and server responses differ. Do not share full token values.

### Step 3 – Interpret carefully

1. Distinguish fresh login from validated expiry/revocation: a new token value alone does not prove previous tokens are unusable.
2. If no expiry policy is provided, do not attempt to force expiry or wait indefinitely.

### Results table

| Session state | Request/status | Auth material type | Protected content received? | Interpretation |
|---|---|---|---|---|
| Initial login | | | | |
| After logout | | | | |
| Fresh login | | | | |

### Interpretation questions

1. What demonstrates enforcement at the server rather than only browser storage changes?

**Evidence to capture:** Redacted session-state comparison and limitations.

**Expected outcome:** An accurate session-lifecycle observation.

---


## Task 18 – Build an extended route-to-API dependency diagram

**Objective:** Show at least five observed visible screens/routes and the real requests they trigger.

### Step 1 – Collect observations

1. Use visible UI navigation to identify at least five routes or screen states where possible.
2. For each, capture a relevant request in Firefox Network and the chosen proxy. Do not use route guessing or bundle-based secret extraction.

### Step 2 – Draw relationships

1. Draw each screen → UI action → actual endpoint/method → response status.
2. Label public/authenticated/uncertain context and explicitly show screens that use only local SPA navigation.
3. Mark client controls needing independent server verification as **Not tested**, not insecure.

### Step 3 – Review evidence

1. Cross-reference diagram nodes with screenshot filenames and Task 5 endpoint IDs.
2. Explain one route that did not generate a new document request.

### Results table

| Screen | Action | Endpoint/method | Status | Auth state | Evidence |
|---|---|---|---|---|---|
| | | | | | |
| | | | | | |
| | | | | | |
| | | | | | |
| | | | | | |

### Interpretation questions

1. Which observed frontend relationship is not enough to prove access control?

**Evidence to capture:** Extended labelled dependency map and linked captures.

**Expected outcome:** A technical map connecting visible SPA behaviour to actual HTTP traffic.

---


## Task 19 – Design a measurable authorisation remediation retest

**Objective:** Specify a before/after plan for one demonstrated weakness or validation gap without claiming fixes not performed.

### Step 1 – Select a suitable finding

1. Choose an authenticated read-only endpoint, a confirmed issue or one inconclusive access-control observation from the lab.
2. Record its baseline request, intended permissions and supported evidence.

### Step 2 – Define a secure control

1. Describe expected server-side authentication, per-object ownership or role checks as relevant.
2. Define a positive test (approved account retains access) and negative test (unapproved or unauthenticated account is denied).
3. Use only authorised test objects and accounts. Never guess other identifiers.

### Step 3 – Record retest status

1. State how the exact read-only request would be repeated after a fix.
2. If no change has been made, write **“Proposed retest only — no code change performed.”**
3. Do not treat UI changes, logging out or swapping test roles as code-level remediation.

### Results table

| Item | Before change | After change / proposed expectation |
|---|---|---|
| Endpoint and method | | |
| Permitted account response | | |
| Disallowed account response | | |
| Restricted content returned? | | |
| Actual code change applied? | | |
| Retest performed? | | |

### Interpretation questions

1. What evidence would prove the fix, while preserving authorised access?
2. Which conclusions remain hypothetical because no fix was implemented?

**Evidence to capture:** Proposed control, before/after plan and explicit actual/proposed status.

**Expected outcome:** A defensible authorisation retest design.

---

# Suggested Evidence Filenames

| Filename | Purpose |
|---|---|
| `01-target-and-baseline.png` | Host, Docker and initial HTTP request |
| `02-public-navigation.png` | Unauthenticated product browsing |
| `03-login-comparison.png` | Failed/successful login comparison |
| `04-auth-storage-redacted.png` | Storage key names with secrets redacted |
| `05-logout-protected-comparison.png` | Access before/after logout |
| `06-api-endpoint-inventory.png` | Captured API requests |
| `07-without-auth-comparison.png` | Read-only authenticated/unauthenticated comparison |
| `08-object-access-optional.png` | Conditional authorised object comparison |
| `09-client-route-map.png` | Three UI-to-API mappings |
| `10-business-logic-comparison.png` | Ordinary workflow variation |
| `11-role-comparison-optional.png` | Approved roles only |
| `12-session-lifecycle-optional.png` | Fresh login/logout observations |
| `13-remediation-retest-optional.png` | Evidence **only if performed** |
| `09-endpoint-map.md` | Completed endpoint/access-control map |
| `10-findings-summary.md` | 400–500-word findings summary |

## Week 8 expected learning outcomes

Students should be able to (1) recognise API calls made by a single-page application, (2) document login, session and logout behaviour, (3) map observable endpoints, (4) evaluate limited request- and object-level access controls within expressly authorised accounts, (5) distinguish client-side routing from backend enforcement, (6) evaluate one benign business-logic rule, and (7) produce professionally structured, evidence-linked findings and remediation recommendations with clearly stated limitations.

**Final reminder:** No particular vulnerable endpoint or access-control flaw is presumed. Assess only what the evidence actually demonstrates.

