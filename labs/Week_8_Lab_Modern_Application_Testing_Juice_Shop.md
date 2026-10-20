# Week 8 Lab – Modern Application Testing with OWASP Juice Shop

**Course:** ZSPS2113 – Ethical Hacking and Penetration Testing  
**Tutorial / Stage:** Modern application testing  
**Authorised target:** OWASP Juice Shop hosted on Ubuntu-Server  
**Testing workstation:** Kali-Attacker  
**Tools:** Firefox Browser Developer Tools, Burp Suite Community Edition **or** OWASP ZAP  
**Primary deliverables:** Evidence-linked endpoint map, access-control findings table, 400–500-word Findings Summary

> **Scope and safety:** Work only on the instructor-authorised Juice Shop instance and accounts. Use fictitious test information, do not test other hosts, and do not access, change, or extract another student's information. Use only low-volume, manually initiated requests. Do not perform credential stuffing, automated brute force, denial-of-service, destructive changes, or payment transactions. Treat any unexpected access to another account's data as a finding; record only the minimum evidence required and stop that test.

## Learning objectives

By the end of the lab, students should be able to:

1. Establish reproducible browser and proxy baselines for a single-page application (SPA).
2. Identify authentication-related HTTP requests and distinguish browser state from server-side authentication.
3. Map client-visible API endpoints, request methods, parameters, response statuses, and authentication requirements.
4. Compare permitted and unauthenticated requests to assess access-control enforcement.
5. Examine client-side JavaScript, browser storage, and network traffic without assuming that discoverable routes are exploitable.
6. Evaluate one benign business-logic workflow using controlled, low-impact variations.
7. Report validated findings separately from potential risks and untested hypotheses.

## Before you begin

- Use the IP address and port assigned to **your** Ubuntu-Server. Example only: `http://192.168.1.100:3000/`.
- Confirm the instructor's account policy. Where two test accounts are needed, use **two accounts expressly approved for the lab**; otherwise perform only unauthenticated-versus-authenticated comparisons.
- Capture requests using **one proxy** (Burp or ZAP). In Burp use **Proxy → HTTP history** and **Repeater**. In ZAP use **History** and **Requester / Manual Request Editor**, where available.
- Redact bearer tokens, credentials, session cookies, account identifiers, email addresses, and any private content from shared screenshots. Preserve methods, paths, parameter names, response statuses, and sufficient non-sensitive response structure.
- Save evidence under `~/lab-evidence/week8/`. Create the directory with:

```bash
mkdir -p ~/lab-evidence/week8
```

---

# Part A – Environment, Scope and Baselines

## Task 1 – Confirm the authorised Juice Shop target

1. Start Kali-Attacker and Ubuntu-Server and confirm the Ubuntu-Server IP address with `ip -br addr`.
2. On Ubuntu-Server, check the Juice Shop Docker container using `sudo docker ps`. Record its image, container status, and published port (often 3000 in the lab, but verify yours).
3. From Kali, open the authorised Juice Shop URL in Firefox. Do not use a public Juice Shop demo or any non-lab host.
4. With **Firefox Developer Tools → Network** open and **Persist Logs** enabled, refresh the application. Record the initial document request, status, and any redirect; note that subsequent SPA navigation may occur without full page reloads.
5. Record your environment:

| Item | Observed value |
|---|---|
| Kali-Attacker IP | |
| Ubuntu-Server IP | |
| Juice Shop URL and port | |
| Docker container/image/status | |
| Initial HTTP request/status | |
| Redirect (if any) | |
| Proxy selected (Burp or ZAP) | |
| Authorised account(s) available | |

**Evidence:** Target URL, running container, initial Network request and completed table.  
**Expected outcome:** A confirmed, isolated and authorised Juice Shop baseline.

## Task 2 – Establish unauthenticated navigation and API baseline

1. Open Juice Shop in a fresh private browser window, without logging in.
2. Browse the home page, product list, product detail view, and login page. Avoid submitting orders or changing account information.
3. In Network, filter **Fetch/XHR** (or equivalent); distinguish document/static-asset requests from API calls.
4. Choose **two ordinary, read-only requests** observed during normal browsing. Capture each in Burp/ZAP and record method, path, query parameters, response status and a brief response description.
5. Repeat one unchanged **read-only** request in Repeater/Requester, with appropriate permission. Record whether the result is consistent.

| Page/action | Request method | Path / endpoint | Parameters | Status | Response description | Login required? |
|---|---|---|---|---|---|---|
| Home/products | | | | | | |
| Product details | | | | | | |
| Login page/load | | | | | | |

**Evidence:** Browser pages, two read-only request/response captures and baseline table.  
**Interpretation:** A discovered endpoint is not automatically a vulnerability.

---

# Part B – Authentication and Session Behaviour

## Task 3 – Observe normal and unsuccessful authentication

1. Use only a lab-approved account. In Firefox Network and Burp/ZAP, identify the login request **as actually observed**; do not assume its endpoint or request format.
2. Perform one deliberately incorrect login with a fictitious password for your own test account. Record the status, generic error/message and whether an authenticated session was established.
3. Log in successfully using the supplied account credentials. Record the request method/path, status, resulting navigation, and any authentication state indicators.
4. Inspect Firefox **Storage** (cookies, local/session storage) and the relevant subsequent requests. Record **storage mechanism and token/cookie names only**; never copy secret values into evidence.
5. Navigate between two permitted pages and determine whether credentials are retransmitted or whether authentication state is reused.

| Observation | Unsuccessful login | Successful login |
|---|---|---|
| Request method/path | | |
| HTTP status | | |
| Response message | | |
| Authentication state established? | | |
| Subsequent protected-page behaviour | | |

**Evidence:** Redacted requests/responses, storage panel showing names (not values), completed comparison.  
**Analysis:** Distinguish observed tokens/browser storage from proven server-side session validation.

## Task 4 – Assess logout and expired/absent authentication state

1. While authenticated, select one ordinary **read-only account-specific request** initiated by the UI (for example, viewing your own profile). Do not select another user's identifier.
2. Capture the request, redact secrets, and record the response.
3. Log out using the application's normal interface; observe any logout request and browser storage change.
4. Attempt to revisit the protected UI page. If needed, repeat the same **read-only** request without its authentication header/cookie in a controlled Repeater copy; do not change account identifiers.
5. Note whether the server rejects the request, returns public data, or behaves in another way. If the UI appears cached, verify whether a fresh HTTP response actually grants access.

| Test | Request/status | Observed response | Supported conclusion |
|---|---|---|---|
| Authenticated own-account baseline | | | |
| After logout | | | |
| Without auth material (read-only) | | | |

**Evidence:** Login/logout state comparison and protected-request responses.  
**Expected outcome:** An evidence-based assessment of whether unauthenticated requests can retrieve protected content, without assuming logout necessarily destroys every server-side token.

---

# Part C – API Endpoint Discovery and Access Control

## Task 5 – Build a client-visible endpoint inventory

1. While navigating normal Juice Shop screens, inspect Fetch/XHR traffic and locate **at least six distinct requests**, where available, across product browsing, search, authentication, and user-specific functions.
2. For each request, record the actual endpoint, method, parameter **names** (not secrets), status and purpose.
3. Mark whether the request appears public, authenticated or uncertain based on **observed behaviour**.
4. Exclude third-party requests and do not enumerate hidden or administrative routes using guessing or wordlists.

| ID | UI action | HTTP method | Endpoint/path | Parameter names | Response status | Public/authenticated/uncertain | Evidence |
|---|---|---|---|---|---|---|---|
| E1 | | | | | | | |
| E2 | | | | | | | |
| E3 | | | | | | | |
| E4 | | | | | | | |
| E5 | | | | | | | |
| E6 | | | | | | | |

**Evidence:** Labelled Network/proxy history and endpoint inventory.  
**Expected outcome:** A client-visible API map based on requests actually generated by the application.

## Task 6 – Compare public and authenticated endpoint behaviour

1. Select **two read-only endpoints** from Task 5: ideally one publicly accessible and one associated with your account.
2. Capture each endpoint in its normal state (authenticated where needed).
3. Create a copy in Repeater/Requester and remove **only** the authentication material (e.g., an Authorization header or session cookie) while preserving the endpoint, method and other parameters.
4. Submit once, record response status and whether the response contains restricted information.
5. Compare with the normal response. If an account-specific response is returned without authentication, stop and document a minimal, redacted example.

| Endpoint | Normal status/content | Without auth status/content | Access-control interpretation | Evidence |
|---|---|---|---|---|
| Public endpoint | | | | |
| Account-specific endpoint | | | | |

**Interpretation:** `200 OK` alone does not prove improper access; inspect the content and its intended visibility. `401` and `403` are common rejections, but report actual results.

## Task 7 – Test object-level access control using approved accounts (conditional)

**Perform only if the instructor supplies two isolated test accounts and expressly authorises cross-account access-control comparisons. Otherwise record “Not performed – second authorised test account unavailable.”**

1. Using account A, capture a **read-only** request displaying an object owned by account A (e.g., a profile or test record).
2. Using account B, capture the corresponding request for an object owned by account B, and identify the relevant object identifier if present.
3. As account B, test **one** controlled read-only request referencing account A's specifically approved test object. Change only that object identifier; do not guess or enumerate identifiers.
4. Record whether the server rejects access or returns the approved object's data. Stop immediately if any unapproved information appears.
5. Do not attempt editing, deletion, checkout, role changes or access to third-party accounts.

| Account context | Object ownership | Request/status | Returned information (redacted) | Authorised access? |
|---|---|---|---|---|
| A | Own test object | | | |
| B | Own test object | | | |
| B | A's instructor-approved test object | | | |

**Evidence:** Minimal redacted comparison and permission/scope confirmation.  
**Interpretation:** An insecure direct object reference / broken object-level authorisation is **validated only** if the server returns an object that the requesting test account is not authorised to access.

---

# Part D – Client-Side Discovery and Business Logic

## Task 8 – Map client-side routes and exposed application structure

1. Inspect the **Elements/Inspector**, **Sources/Debugger**, and **Network** panels while using the visible UI.
2. Record three client-side route changes or navigation actions. Note which cause an actual HTTP request and which are handled within the SPA.
3. Inspect loaded JavaScript filenames and public application configuration visible to the browser, without extracting secrets or scanning for hidden administrative endpoints.
4. Identify UI-provided links/buttons and the endpoint requests they trigger; distinguish a hidden/disabled UI control from a demonstrated server-side authorisation control.
5. Produce a simple diagram connecting screens → client-side action → observed endpoint → response.

```text
Juice Shop UI
  ├── Product list ───> [observed endpoint] ───> [response]
  ├── Search ─────────> [observed endpoint] ───> [response]
  └── Own account ────> [observed endpoint] ───> [response]
```

**Evidence:** Client-side route observations, at least three screen-to-endpoint mappings and diagram.  
**Expected outcome:** An understanding of the difference between client-side discoverability and server-side security enforcement.

## Task 9 – Establish a benign business-logic baseline

1. Select **one harmless user workflow** available to your authorised account, such as searching for a product, applying a non-sensitive product filter, or changing quantity in a local UI view **without completing a purchase**.
2. Describe the intended rule (e.g., a quantity selector should remain within the UI's displayed range; a search filter should return relevant items).
3. Capture the normal browser action and resulting API request/response, if any.
4. Perform one harmless variation using ordinary UI inputs and compare the results. Avoid actual orders, payment details, coupon redemption, inventory changes or another user's basket.
5. Separate front-end restrictions from observed server behaviour.

| Workflow | Normal action/result | Benign variation/result | Relevant endpoint | What rule was actually checked? |
|---|---|---|---|---|
| | | | | |

**Evidence:** Two labelled workflow states and corresponding requests.  
**Interpretation:** An unusual response does not, on its own, validate a business-logic vulnerability.

## Task 10 – Assess one client-side control against a server response

1. Choose a harmless, **read-only** request associated with Task 9 (e.g., a product search with a normal query parameter).
2. Preserve the original request in Repeater/Requester.
3. Modify **one benign parameter** within the range of values naturally allowed by the interface (e.g., another ordinary search term). Do not use injection strings, privilege flags, negative prices or unauthorised identifiers.
4. Send once, compare the response with the baseline and record whether server-side behaviour aligns with the intended rule.
5. Explain what would require additional authorised testing; do not claim bypass of server-side controls based solely on UI observations.

**Evidence:** Baseline/modified request comparison and a short interpretation of the observed business rule.

---

# Part E – Findings, Reporting and Cleanup

## Task 11 – Create a consolidated endpoint and access-control map

Create `~/lab-evidence/week8/09-endpoint-map.md` using the inventory from Tasks 5–8. Include at least six captured endpoints where available, plus the following columns:

| ID | UI action / route | Endpoint / method | Input parameters | Auth context | Baseline status | Comparison result | Access-control finding | Evidence |
|---|---|---|---|---|---|---|---|---|
| | | | | | | | | |

Clearly label **Not tested**, **No issue observed**, **Inconclusive**, or **Validated weakness**. Do not infer protection or vulnerability simply from endpoint names or UI visibility.

## Task 12 – Assess demonstrated impact and recommend remediation

1. For each relevant finding, document the direct observation, supported interpretation and untested potential impact.
2. Recommend controls as appropriate: server-side authorisation per request and object; deny-by-default access; object ownership verification; session invalidation/token expiry; careful exposure of client-side configuration; server-side business-rule validation; rate limiting where applicable; and generic error responses.
3. Link each proposed control to an observation or mark it **preventive best practice**.
4. Define a before/after **read-only** verification test for one access-control issue or validation gap. If no code changes are permitted, state **“Proposed retest only — no code change performed.”** Changing UI state or logging out is not proof that code-level remediation was implemented.

| Observation | Demonstrated impact | Potential risk (not validated) | Recommended control | Actual/proposed retest |
|---|---|---|---|---|
| | | | | |

## Task 13 – Write a 400–500-word Findings Summary

Create `~/lab-evidence/week8/10-findings-summary.md`:

```bash
nano ~/lab-evidence/week8/10-findings-summary.md
```

Include the authorised target and address; authentication/login/logout behaviour; session mechanism (without disclosing secrets); discovered endpoints; unauthenticated/authenticated comparisons; any authorised object-level checks; client-side routing observations; a benign business-logic comparison; demonstrated impact; remediation; retest status; limitations; and whether **any access-control or business-logic vulnerability was actually validated**. Count narrative words only, excluding tables/headings/captions.

### Suggested structure

> Modern application testing was conducted against the authorised **OWASP Juice Shop** training application at **[address]** using Firefox Developer Tools and **[Burp Suite/OWASP ZAP]**. Baseline browsing identified **[observed pages and endpoints]**.
>
> Authentication testing showed **[incorrect login result]**, **[successful login result]**, and **[observed session mechanism]**. Logout and unauthenticated replay produced **[directly observed server response]**.
>
> The endpoint map documented **[number]** client-visible API requests. Controlled access comparisons for **[endpoint(s)]** returned **[results]**. Object-level access testing was **[performed using two approved accounts/not performed]** and demonstrated **[result or limitation]**.
>
> Client-side discovery identified **[routes and request mapping]**. A harmless business-logic comparison of **[workflow]** showed **[observed behaviour]**. The demonstrated impact was **[evidence-based impact]**; potential risks not directly tested were **[briefly identified]**.
>
> Recommendations include **server-side request/object authorisation, appropriate session management, and server-side business-rule enforcement**. Remediation retesting was **[performed/not performed]**. Testing was limited to the designated functions and authorised accounts; **[access-control/business-logic]** vulnerabilities were **[validated/not validated/inconclusive]** based only on the recorded evidence.

## Task 14 – Validate evidence and submit

- [ ] Authorised Juice Shop address, test workstation, Docker status and scope recorded.
- [ ] Normal unauthenticated baseline and login/logout behaviour documented.
- [ ] At least six actual client-visible endpoints recorded, or a reason given if fewer.
- [ ] At least one public/authenticated access comparison supported by HTTP evidence.
- [ ] Any cross-account object test used only two explicitly approved accounts.
- [ ] Client-side screen-to-endpoint mapping completed.
- [ ] Harmless business-logic comparison recorded.
- [ ] Evidence screenshots are legible and linked to claims.
- [ ] Cookies, bearer tokens, passwords and private information redacted.
- [ ] Findings classified as validated, inconclusive, not tested, or no issue observed.
- [ ] Remediation, retesting status and limitations clearly stated.
- [ ] Findings Summary contains 400–500 narrative words.
- [ ] Lab cleanup/restoration documented.

Submit `09-endpoint-map.md`, `10-findings-summary.md` and selected labelled evidence according to the instructor's directions. Do not submit raw session secrets or unnecessary personal information.

## Task 15 – Restore the authorised lab environment

1. Log out of Juice Shop and close proxy sessions.
2. Remove test data **only** if explicitly authorised by the instructor; do not delete other users' objects or reset shared services.
3. Restore any modified testing configuration; follow the instructor's container shutdown/restart guidance.
4. Verify no test activity remains running and record actual cleanup performed.

---

# Part F – Advanced Optional Tasks

Complete these only if the specified resources and authorisation are available. They are **not prerequisites** for the core Week 8 report.

## Task 16 – Compare two authorised account roles

With two instructor-provided accounts that have deliberately different permitted roles, capture the same **read-only** endpoint for each. Compare statuses, response fields and intended permissions. Do not modify either role or probe administrative endpoints not explicitly in scope. Document whether server-side role enforcement is visible.

## Task 17 – Analyse session expiry or fresh-login behaviour

Using your own test account, capture a protected read-only baseline, then log out and reauthenticate normally. Compare whether authentication material and protected responses change. If no expiry policy is provided, **do not wait indefinitely or attempt to force token expiration**. Distinguish token changes from a proven server-side expiry policy.

## Task 18 – Create a route-to-API dependency map

Produce a diagram of at least five observed client-side screens/routes connected to their actual HTTP endpoints, including request method, authentication context and status. Flag visible client-side controls that would require separate server-side verification; do not guess unobserved endpoints.

## Task 19 – Design an access-control remediation retest

Choose one confirmed weakness or validation gap, identify the expected permission rule, and define a same-request before/after comparison with a permitted and disallowed authorised account. Include expected status/content, negative test and regression test. If no fix is implemented, state **“Proposed retest only — no code change performed.”**

---

# Suggested Evidence Filenames

| Filename | Purpose |
|---|---|
| `01-target-and-baseline.png` | Authorised host, app and initial HTTP request |
| `02-public-navigation.png` | Public browsing baseline |
| `03-login-comparison.png` | Failed and successful authentication |
| `04-session-storage-redacted.png` | Cookie/token names or storage mechanism (secrets redacted) |
| `05-logout-access-comparison.png` | Protected resource after logout/no authentication |
| `06-endpoint-inventory.png` | Client-visible API history |
| `07-access-control-comparison.png` | Public versus authenticated response |
| `08-object-access-optional.png` | Authorised cross-account comparison, if permitted |
| `09-client-routes.png` | Route and API discovery evidence |
| `10-business-logic-comparison.png` | Harmless workflow variation |
| `11-remediation-retest.png` | Retest evidence only if actually performed |
| `09-endpoint-map.md` | Final endpoint/access-control inventory |
| `10-findings-summary.md` | 400–500-word professional findings summary |

## Expected learning outcome

Students can map client-visible endpoints in a modern single-page web application, analyse authentication and server-side access controls, distinguish client-side routes from server-authorised actions, assess harmless business-logic variations, and produce evidence-based findings with proportionate remediation and accurate limitations.
