# Week 9 Lab – Guided Advanced Web Application Security Lessons

**Course:** ZSPS2113 – Ethical Hacking and Penetration Testing  
**Tutorial / Stage:** Guided advanced lessons  
**Authorised targets:** OWASP WebGoat and WebWolf on the designated Ubuntu-Server  
**Tools:** WebGoat, WebWolf, Firefox Developer Tools, Burp Suite  
**Primary deliverables:** Completed lesson evidence, HTTP comparison records, mitigation notes, and a 400–500-word Findings Summary

> **Safety and scope:** Work only in the instructor-provided WebGoat and WebWolf training environment. Complete only the assigned lessons using training accounts, synthetic data, and the lesson's designated endpoints. Do not scan other hosts, use real credentials, attempt persistence, or use destructive payloads. Some WebGoat versions have different lesson names and steps; record the actual lesson title and follow its on-screen instructions. **Do not claim a lesson is completed unless WebGoat displays its completion indicator or another instructor-approved outcome.**

## Learning objectives

By the end of Week 9, students should be able to:

1. Verify the authorised WebGoat and WebWolf environment and establish HTTP baselines.
2. Complete selected guided lessons involving access control, injection, token/session handling, or server-side parsing.
3. Use WebWolf to observe training callbacks or messages **only when a selected lesson explicitly requires it**.
4. Use Burp Suite to compare an unchanged baseline with one controlled change at a time.
5. Separate observable application behaviour from assumptions about exploitability or impact.
6. Recommend vulnerability-specific controls and produce reproducible, evidence-linked mitigation notes.

## Before you begin

- Use the **actual ports shown by `sudo docker ps`**. Common course deployments use WebGoat on `8081` and WebWolf on a separately published port, but mappings vary.
- Some lesson categories below are alternatives. Your instructor should designate **at least one access-control lesson and two additional lessons** from the available categories; complete the relevant tasks and mark unavailable categories *Not assigned*.
- Avoid submitting real account passwords, bearer tokens, session cookies, API keys, or WebWolf secrets in reports. Redact values while retaining header names, HTTP methods, endpoint paths, status codes, and non-sensitive outputs.
- Save evidence under `~/lab-evidence/week9/` on Kali-Attacker.

---

# Part A – Environment Verification and Baseline

## Task 1 – Confirm the authorised WebGoat and WebWolf services

**Objective:** Verify that both applications are reachable and correctly identify their training roles.

### Procedure

1. Start **Ubuntu-Server** and **Kali-Attacker** in the authorised lab environment.
2. On Ubuntu-Server, run:

   ```bash
   ip -br addr
   sudo docker ps
   ```

3. Record the Ubuntu-Server IP and **actual host-to-container port mappings** for WebGoat and WebWolf.
4. On Kali-Attacker, open each application in Firefox using the mapped ports. For example, use `http://192.168.1.100:8081/WebGoat/` only if that address and port match your environment. Obtain the WebWolf URL from the container mapping or course instructions.
5. Verify that WebGoat shows its login or lesson interface and WebWolf shows its expected training interface.
6. Do not guess credentials or alter other containers. If a service is unavailable, consult the earlier Docker lab instructions and report the issue.

| Item | Observed value |
|---|---|
| Kali-Attacker IP | |
| Authorised Ubuntu-Server IP | |
| Lab network / isolation | |
| WebGoat image/container and published port | |
| WebGoat URL | |
| WebWolf image/container and published port | |
| WebWolf URL | |
| Browser accessibility / redirect | |
| Service health or troubleshooting notes | |

**Evidence to capture:** Container status, published ports, WebGoat and WebWolf landing pages, and the completed table.  
**Expected outcome:** The two authorised services are correctly identified and accessible, or a documented setup limitation explains why testing cannot continue.

## Task 2 – Prepare evidence and capture a normal HTTP baseline

**Objective:** Establish a reliable reference request before guided testing.

### Procedure

1. On Kali-Attacker, create the evidence folder:

   ```bash
   mkdir -p ~/lab-evidence/week9
   ```

2. Sign into WebGoat using only the supplied lab account or create the training account if directed by the instructor.
3. Open Firefox Developer Tools (**F12 → Network**) and enable persistent request logging.
4. Open Burp Suite **Proxy → HTTP history**. Use a browser configured to proxy through Burp; confirm a WebGoat request appears.
5. Select an assigned lesson and observe its introductory instructions **without modifying inputs**.
6. Capture a normal request associated with opening or using the lesson. Record method, path, parameter names, status, response and any redirect.
7. Send the captured request to **Repeater**, retain an unchanged baseline tab, and resend only if the request has no unintended state change. For state-changing requests, use an instructor-approved fresh training state instead.
8. Record any dynamic elements (for example, changing tokens) that make exact response lengths differ between otherwise equivalent requests.

| Lesson / page | Method | Path | Parameters (names only) | HTTP status | Normal response / redirect | Evidence filename |
|---|---|---|---|---|---|---|
| | | | | | | |

**Evidence to capture:** Evidence directory, Browser Network entry, baseline Burp request/response, and table.  
**Expected outcome:** A reproducible or appropriately qualified baseline and a safe evidence collection workflow.

---

# Part B – Guided Access-Control Testing

## Task 3 – Identify identities, resources, and intended permissions

**Objective:** Map the legitimate authorisation boundary of an assigned WebGoat access-control lesson.

### Procedure

1. Open an instructor-designated lesson in the **Access Control** category (for example, a lesson on missing function-level access control or insecure direct object references if available).
2. Read the lesson objectives and record the **exact lesson name and step number**.
3. Identify the training identity or role used, the permitted resource or function, and the resource or function that should require different permission.
4. Perform the **normal permitted action** through the lesson UI. Capture its corresponding request in Browser Developer Tools or Burp.
5. Record any object identifier, user identifier, path segment, query value, or JSON field that determines the requested resource. Redact session tokens and identifiers that are sensitive outside the lesson.
6. Do not test non-lesson records or real accounts.

| Lesson / step | Training identity / role | Intended permitted action | Protected resource / function | Relevant request field | Expected access rule |
|---|---|---|---|---|---|
| | | | | | |

**Evidence to capture:** Lesson title/instructions, normal request and response, and permission map.  
**Expected outcome:** A clear distinction between permitted behaviour and the authorisation rule the guided lesson is examining.

## Task 4 – Compare an authorised request with one controlled access variation

**Objective:** Determine whether the assigned lesson enforces the intended access-control rule.

### Procedure

1. Preserve the valid request captured in Task 3 in Burp Repeater.
2. Follow the **specific variation prescribed by the guided lesson** (for example, one training object identifier or a permitted/denied lesson role). Change **one authorisation-relevant field** at a time.
3. Keep other request fields unchanged where practical. If switching training identities is required, capture a fresh valid request and record the identity change rather than copying another user's session token.
4. Send the authorised controlled request and inspect the response content, HTTP status, redirect and any lesson feedback.
5. Compare the result with the baseline. Remember that `200 OK` does not prove permission was granted and `403` alone does not prove all access controls are correct.
6. Check whether the protected action or data was actually made available. Complete the lesson only within its provided workflow.

| Scenario | Training role / object | HTTP status | Response / resource observed | Expected policy | Policy enforced? | Evidence |
|---|---|---|---|---|---|---|
| Legitimate baseline | | | | | | |
| Controlled variation | | | | | | |

**Questions:** Did the server check authorisation, or merely hide a control in the UI? Is the result evidence of a validated access-control issue, inconclusive, or correct enforcement? What further lesson-authorised check would clarify ambiguity?

**Evidence to capture:** Two labelled request/response pairs, any WebGoat success indicator, completed comparison table.  
**Expected outcome:** An evidence-based access-control finding or explicit non-validation.

---

# Part C – Guided Injection and Server-Side Parsing

## Task 5 – Establish an injection lesson baseline

**Objective:** Understand how a designated input is processed before introducing lesson-provided modified values.

### Procedure

1. Choose an instructor-designated WebGoat injection lesson (such as an SQL injection lesson if available).
2. Record the lesson name, step, expected normal input format and the specific training parameter or field.
3. Submit the lesson's **ordinary valid input**, as directed on screen.
4. Capture the request and response using Burp or Browser Developer Tools. Identify request method, path, parameter/value, HTTP status and normal output.
5. Reproduce the baseline in Repeater only where this is safe and does not unexpectedly alter training state.
6. Record any dynamic fields or server-generated values that may affect comparisons.

| Item | Baseline observation |
|---|---|
| Assigned lesson / step | |
| Request method and path | |
| User-controlled parameter | |
| Normal input | |
| Status and response length (specify measurement) | |
| Expected normal content | |
| Evidence | |

**Evidence to capture:** Lesson screen, baseline Browser/Burp request and response, table.  
**Expected outcome:** A documented baseline suitable for a controlled injection comparison.

## Task 6 – Perform the lesson-provided controlled injection comparison

**Objective:** Assess whether the assigned injection input changes server-side interpretation.

### Procedure

1. Read the lesson's authorised example or guided input instructions. Use **only that lesson's benign training values**.
2. In Repeater or the lesson UI, apply one controlled variation and capture its result.
3. Where the selected lesson requires a true/false comparison, submit the two **lesson-provided** alternatives separately, preserving the rest of the request.
4. Record HTTP status, response length, returned records or messages, and any error output. A syntax error alone is not proof of the full exploitability or underlying code implementation.
5. Identify whether the lesson's expected behaviour was demonstrated. Record its success indicator if applicable.
6. Avoid database extraction beyond lesson samples, destructive statements, production endpoints, and unapproved payload variations.

| Scenario | Input label (baseline/modified) | HTTP status | Response content | Interpretation | Evidence |
|---|---|---|---|---|---|
| Baseline | | | | | |
| Guided variation A | | | | | |
| Guided variation B, if assigned | | | | | |

**Questions:** What was actually demonstrated? Is input reaching a server-side interpreter? Does the evidence validate the lesson vulnerability, or is the result inconclusive? What prepared statement or safe API design would prevent this class of problem?

**Evidence to capture:** Controlled Burp comparison and lesson completion proof if achieved.  
**Expected outcome:** Accurate interpretation of the guided injection behaviour and its limitations.

## Task 7 – Examine a server-side parsing lesson, if assigned

**Objective:** Investigate how a designated WebGoat lesson parses structured user-supplied data.

### Procedure

1. Open an assigned **server-side parsing** lesson, such as a lesson dealing with XML, JSON, or another parser, **if present in the installed version**.
2. Record the exact lesson and parser/input type. If no relevant lesson is assigned, mark this task **Not assigned**, not failed.
3. Submit the lesson's normal sample input, capture the request, and note its declared `Content-Type` and body structure.
4. Using only the lesson's supplied harmless malformed or alternative example, modify one structural element at a time.
5. Compare responses, including parse failures, validation messages and unexpected information disclosures.
6. Do **not** retrieve local files, use external entities to access network resources, or test unapproved destinations.

| Item | Normal input | Guided modified input |
|---|---|---|
| Content type / parser | | |
| Request path | | |
| HTTP status | | |
| Visible content or error | | |
| Server details disclosed? | | |

**Questions:** Does an error confirm a security vulnerability? What parser configuration, schema validation, data-binding approach, or safe error handling is appropriate to the observed behaviour?

**Evidence to capture:** Normal and modified requests/responses, completed table, assigned lesson status.  
**Expected outcome:** Evidence-based parsing observations and relevant mitigation ideas.

---

# Part D – Token/Session Handling and WebWolf

## Task 8 – Map the training authentication or token flow

**Objective:** Identify where the chosen lesson creates, sends and checks authentication/session data.

### Procedure

1. Open an instructor-selected **session or token** lesson if available.
2. Identify the training login/session boundary and the request that transitions the application into an authenticated state.
3. In Browser Developer Tools and Burp, identify **cookie/header names only** and any relevant token-bearing request fields; do not record secret values.
4. Compare a request before authentication with a request after authentication, using the lesson UI and account provided.
5. Observe how the application handles logout, expiry or a deliberately invalid training token **only if directed by the lesson**.
6. Record whether session persistence, rejection or other expected behaviour is observable. Do not claim token forgery or bypass unless the controlled lesson validates it.

| State / action | Endpoint | Authentication-related field name | HTTP status | Observable outcome | Evidence |
|---|---|---|---|---|---|
| Before authentication | | | | | |
| After authentication | | | | | |
| Logout / expired / invalid (if assigned) | | | | | |

**Evidence to capture:** Redacted header/name comparison, state-change responses, lesson outcome.  
**Expected outcome:** An authentication/session flow with appropriate privacy and evidence handling.

## Task 9 – Use WebWolf for an assigned training callback or message

**Objective:** Observe a WebGoat-to-WebWolf interaction where the lesson explicitly requires an auxiliary test service.

### Procedure

1. Confirm WebWolf is accessible using the authorised address recorded in Task 1.
2. Read the assigned WebGoat lesson carefully and identify **whether WebWolf is actually required**. If not, record **Not required for selected lesson** and do not fabricate a callback.
3. In WebWolf, identify the relevant instructor-approved feature (such as a lesson-specific mailbox or callback viewer).
4. Follow the WebGoat lesson's built-in procedure using **only training addresses, values and destinations** provided by the course/lesson.
5. In WebWolf, observe whether the expected training event, message or callback appears; record a non-sensitive event label, time and correlation with the action initiated in WebGoat.
6. Check the lesson's completion indicator. Do not direct callbacks to outside services or include real personal data.

| Assigned lesson | WebGoat action | WebWolf feature / event | Observed? | Lesson outcome | Evidence |
|---|---|---|---|---|---|
| | | | | | |

**Evidence to capture:** WebGoat instruction/trigger and WebWolf resulting observation, with secrets redacted.  
**Expected outcome:** A correctly correlated authorised training event, or a documented reason WebWolf was not used.

## Task 10 – Compare token/session and WebWolf observations

**Objective:** Explain the trust boundaries revealed by the assigned lesson and auxiliary evidence.

### Procedure

1. Select one request/response pair from Task 8 or one correlated WebGoat–WebWolf event from Task 9.
2. Draw a simple data-flow diagram showing: **Browser → WebGoat → validation/action → WebWolf (only if used) → response/outcome**.
3. Mark where untrusted input enters, where authorisation or token validation should happen, and where secrets could be exposed if controls fail.
4. Write approximately **100–150 words** linking the diagram to observed evidence.
5. State explicitly whether a vulnerability was actually demonstrated or only a normal workflow was observed.

**Evidence to capture:** Annotated diagram and short interpretation.  
**Expected outcome:** A clear, evidence-linked explanation of the lesson's trust boundary and mitigation needs.

---

# Part E – Findings, Mitigation and Reporting

## Task 11 – Create a guided-lesson evidence matrix

**Objective:** Summarise completed and incomplete lessons without implying unperformed testing.

### Procedure

1. Create `~/lab-evidence/week9/09-lesson-evidence-table.md`:

   ```bash
   nano ~/lab-evidence/week9/09-lesson-evidence-table.md
   ```

2. Enter the assigned access-control lesson and each additional assigned lesson.
3. Reference the normal baseline, controlled variation, observed output and lesson-completion state.
4. Use **Completed / Attempted / Not assigned / Blocked** for the lesson status and **Validated / Not validated / Inconclusive** for the security conclusion. These are different concepts.

| Category | Exact WebGoat lesson / step | Endpoint / input | Baseline | Guided modified test | Observed outcome | Lesson status | Security conclusion | Evidence |
|---|---|---|---|---|---|---|---|---|
| Access control | | | | | | | | |
| Injection | | | | | | | | |
| Token/session or parsing | | | | | | | | |
| WebWolf interaction (if required) | | | | | | | | |

**Evidence to capture:** Completed Markdown table with valid references.  
**Expected outcome:** An auditable summary of what the student actually tested and completed.

## Task 12 – Write mitigation notes for each selected lesson

**Objective:** Translate the observed control problem into practical secure-design recommendations.

### Procedure

1. For every assigned lesson, identify the **observed problem or validation gap**, without assuming that each completed lesson corresponds to a production vulnerability.
2. Propose one primary mitigation and at least one supplementary check where relevant:
   - **Access control:** server-side authorisation for every object/function, deny-by-default policy, ownership checks.
   - **Injection:** parameterised queries/prepared statements, safe APIs, server-side validation, least privilege.
   - **Sessions/tokens:** server-side token validation, expiration and revocation, secure cookie attributes when applicable.
   - **Parsing:** safe parser configuration, schema/format validation, disallow unnecessary features, generic error responses.
3. Explain which layer enforces the proposed control and what result would demonstrate it works.
4. Label recommendations **Evidence-based remediation** or **Preventive best practice**.

| Lesson / finding | Evidence reference | Demonstrated issue or gap | Primary mitigation | How to verify | Recommendation type |
|---|---|---|---|---|---|
| | | | | | |

**Evidence to capture:** Completed mitigation notes table.  
**Expected outcome:** Actionable, correctly scoped mitigation advice linked to lesson evidence.

## Task 13 – Design a remediation verification comparison

**Objective:** Distinguish repeatability, security-level differences and a genuine after-fix retest.

### Procedure

1. Select one evidence-supported lesson finding.
2. Record its baseline and guided modified request, relevant method/path, output and success criteria.
3. Define the proposed application change and the **expected** result if the fix were implemented.
4. Write a before/after procedure that repeats the same training request and verifies both the rejection of invalid access/input **and** continued legitimate functionality.
5. If source code or configuration cannot be changed, explicitly state **“Proposed retest only — no code change performed.”** Do not invent an after-fix result.

| Test element | Before fix (observed) | After fix (expected or observed; label clearly) |
|---|---|---|
| Training lesson / endpoint | | |
| Baseline input | | |
| Controlled variation | | |
| Response/status | | |
| Authorisation or validation result | | |
| Evidence / retest status | | |

**Evidence to capture:** Before/after design, plus actual retest evidence only if implemented.  
**Expected outcome:** A reproducible plan for checking the recommended control.

## Task 14 – Write the 400–500-word Findings Summary

**Objective:** Produce a professional synthesis of the designated guided lessons.

### Procedure

1. Create:

   ```bash
   nano ~/lab-evidence/week9/10-findings-summary.md
   ```

2. Write **400–500 narrative words**. Exclude headings, tables and figure captions from the count.
3. Include the authorised targets, actual addresses, selected lesson names, HTTP baselines, guided input variations, Burp evidence, any WebWolf observation, demonstrated outcomes, mitigation, retest status and limitations.
4. State separately whether lessons were **completed** and whether a vulnerability was **validated** in each area.
5. Check the file with `wc -w` for an initial count, then verify the narrative count if it includes Markdown tables or headings.

### Suggested five-paragraph structure

> Guided advanced testing was conducted against the authorised **WebGoat** application at **[address]**, with **WebWolf** used **[where required / not required]**, using Firefox Developer Tools and Burp Suite. Baseline requests for the selected lessons showed **[normal behaviour]**.
>
> The designated **access-control lesson [name/step]** tested **[resource/function and intended permission]**. Comparing the authorised request with **[guided controlled variation]** produced **[response]**, supporting **[conclusion about access-control enforcement]**.
>
> The designated **injection / parsing lesson [name/step]** tested **[input]**. Burp comparisons showed **[observed response difference]**. Any additional **token/session or WebWolf** observations showed **[actual result, or state not assigned]**.
>
> The demonstrated impact was **[evidence-supported result]**; **[potential risk]** remains unvalidated. Recommended mitigation includes **[specific authorisation checks, safe queries/parsing, session controls, as applicable]**. Remediation was **[implemented and retested / proposed only]**.
>
> Testing was limited to the authorised training accounts, designated lessons and benign guided inputs. The lesson outcomes were **[completed/attempted]**, and the security findings were **[validated/not validated/inconclusive]**. No broader production-system impact is claimed.

**Evidence to capture:** Summary and narrative word count.  
**Expected outcome:** A concise report accurately distinguishing training completion, vulnerability validation and wider security implications.

## Task 15 – Validate evidence, clean up and submit

**Objective:** Ensure reports are accurate, private and complete.

### Procedure

1. Verify the lesson evidence table, mitigation notes and findings summary exist:

   ```bash
   ls -lh ~/lab-evidence/week9/
   ```

2. Use the checklist below to review all claims and evidence.
3. Delete only temporary artefacts explicitly authorised for removal by the lesson or instructor; do not clear shared WebWolf content or alter other accounts.
4. Log out of lab accounts where appropriate and restore instructor-specified settings.
5. Submit the table, summary and **selected** readable screenshots or instructor-required proof of completion.

### Submission checklist

- [ ] WebGoat and WebWolf addresses and authorised scope are recorded.
- [ ] Assigned lesson names and steps are stated exactly.
- [ ] Normal baseline and controlled variation are captured for each tested lesson.
- [ ] Burp requests/responses are labelled and readable.
- [ ] WebWolf evidence is included if a selected lesson required it.
- [ ] Sensitive credentials, cookies and tokens are redacted.
- [ ] Lesson-completion status is separate from vulnerability-validation status.
- [ ] Impact claims reflect directly observed behaviour.
- [ ] Mitigation notes are linked to each completed lesson.
- [ ] Remediation/retest status and limitations are explicit.
- [ ] Findings Summary contains 400–500 narrative words.
- [ ] Cleanup/restoration actions, or their non-applicability, are recorded.

**Expected outcome:** A complete, traceable Week 9 guided-lesson report with mitigation notes.

---

# Part F – Advanced Optional Tasks

> These are optional and do not replace instructor-designated core lessons. Use only existing training roles, lessons and endpoints.

## Task 16 – Compare function-level and object-level access control

1. If the installed WebGoat offers both lesson types, select one example of each using the provided lab identities.
2. Map whether the requested **function** is allowed and whether the specific **object** belongs to the current training identity.
3. Capture one legitimate baseline and one lesson-guided controlled variation for each type.
4. Compare status, visible content, and demonstrated access—not merely the presence of a button or URL.
5. Recommend server-side function and ownership validation; do not expand beyond lesson-provided training records.

| Control | Authorised action | Guided variation | Observed response | Finding | Evidence |
|---|---|---|---|---|---|
| Function level | | | | | |
| Object level | | | | | |

**Evidence:** Two comparisons and a short **100–150-word** explanation of the difference.  
**Expected outcome:** Ability to distinguish function permission from record ownership checks.

## Task 17 – Examine token lifetime and invalidation in a guided lesson

1. Select an assigned token/session lesson that explicitly supports logout, expiry, or invalid-token checks.
2. Capture normal authenticated behaviour and a guided post-logout/expired/invalid-token request using the training account.
3. Redact token values while retaining token/header names and request metadata.
4. Compare the server's response; distinguish UI caching from renewed server-side authorisation.
5. Identify recommended rotation, expiry, revocation and cookie controls applicable to the observed issue.

**Evidence:** Redacted state-transition comparison and mitigation notes.  
**Expected outcome:** Clear explanation of session validation versus mere token presence.

## Task 18 – Correlate WebWolf events with WebGoat requests

1. Select a designated lesson that uses WebWolf as an auxiliary training service.
2. Record the initial WebGoat request path and the action expected to produce the WebWolf event.
3. Perform the lesson-approved action only once unless the lesson requires repetition.
4. Capture the resulting WebWolf event and record a non-sensitive correlation identifier or timestamp.
5. Draw a flow diagram linking the trigger, destination, event and lesson outcome.
6. Explain what the event proves and what it does **not** prove (for example, an observed callback is not automatically evidence of arbitrary access to other systems).

**Evidence:** Trigger/callback pair, correlation table and diagram.  
**Expected outcome:** Correct interpretation of a training callback within its scope.

## Task 19 – Produce a miniature security regression test plan

1. Choose a validated lesson finding or well-defined validation gap.
2. Define one legitimate request that **must continue to work** and one guided prohibited request that **must be blocked** after a hypothetical fix.
3. Describe expected HTTP and application results, including accepted/rejected content, without hard-coding a required status where the lesson does not specify one.
4. Record what automation could check (request, output, status) and what still needs manual review.
5. Mark results as **proposed** unless the instructor has explicitly provided a patched version and you have tested it.

| Test case | Purpose | Input / training identity | Expected result after fix | Verification evidence |
|---|---|---|---|---|
| Positive | Legitimate access | | | |
| Negative | Block invalid action | | | |

**Evidence:** Regression table and short verification rationale.  
**Expected outcome:** A practical verification plan that preserves legitimate application behaviour.

---

## Suggested evidence filenames

| Filename | Purpose |
|---|---|
| `01-authorised-environment.png` | WebGoat/WebWolf and container/port verification |
| `02-http-baseline.png` | Normal HTTP request and response |
| `03-access-control-baseline.png` | Allowed training action |
| `04-access-control-comparison.png` | Controlled permission comparison |
| `05-injection-baseline.png` | Normal assigned injection input |
| `06-injection-comparison.png` | Guided input variation |
| `07-parsing-comparison.png` | Parsing lesson, if assigned |
| `08-session-flow.png` | Redacted authentication/token observations |
| `09-webwolf-event.png` | Auxiliary event, if required |
| `10-lesson-completion.png` | Assigned WebGoat completion indicator |
| `11-trust-boundary-diagram.png` | Data-flow analysis |
| `09-lesson-evidence-table.md` | Completed lesson evidence matrix |
| `10-findings-summary.md` | 400–500-word professional summary |

**Final lab outcome:** Students complete selected WebGoat guided lessons, document relevant WebWolf observations when required, produce evidence-supported interpretations, and connect those results to practical mitigation and verification strategies. A completed lesson does not, by itself, establish a vulnerability outside the authorised training application.
