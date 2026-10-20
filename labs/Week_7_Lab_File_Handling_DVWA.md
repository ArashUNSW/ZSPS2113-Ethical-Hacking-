# Week 7 Lab – File Handling Security in DVWA

**Tutorial / Stage:** File Handling  
**Suggested tools:** Firefox Developer Tools, Burp Suite Community Edition, text editor  
**Scope:** DVWA **File Upload** and **File Inclusion** modules only

## Estimated time ## 
Approximately 2 hours for core activities, with additional time for optional advanced activities.

> [!IMPORTANT]
> Test only the authorised DVWA instance in the isolated lab. Use harmless, self-created text and image files. **Do not** upload executable web shells, attempt remote-code execution, access host operating-system secrets, or test paths outside the permitted DVWA training directories. Do not run these exercises against public websites.

## Learning objectives

By the end of this lab, students should be able to:

1. Establish an evidence-based baseline for DVWA file handling.
2. Assess how uploaded filenames, file types, sizes, and server responses are handled.
3. Observe local file inclusion and path-validation behaviour using benign training files and controlled paths.
4. Compare relevant HTTP requests and responses using Firefox Developer Tools and Burp Suite.
5. Distinguish **demonstrated weaknesses** from unverified risks and recommend effective remediations.

## Part A – Prepare the Week 7 Environment

### Task 1 – Create the Week 7 Evidence Folder

- Start the authorised Ubuntu-Server and Kali-Attacker VMs and confirm that the DVWA container is healthy.
- Record the **actual DVWA address**, IP, port, DVWA version (if available), and security level.

1. On **Kali-Attacker**, open a terminal.
2. Create a Week 7 evidence folder:

```bash
mkdir -p ~/lab-evidence/week7
```

3. Change into the folder:
```bash
cd ~/lab-evidence/week7
```

4. Confirm the current path:
```bash
pwd
```

### Evidence to capture

Take one screenshot showing the Week 7 evidence folder.

### Expected result

```text
/home/<your-user>/lab-evidence/week7
```

---

## Part B – Authorised Environment and Test-File Preparation

**Objective:** Confirm the authorised DVWA environment, identify its file-handling modules, and prepare harmless files for controlled upload testing.

**Tools:** Kali-Attacker, Ubuntu-Server, Firefox Developer Tools, Burp Suite and GIMP (or another approved image editor).

> **Scope:** Perform all tests only against the authorised DVWA training instance. Use harmless files without scripts or executable content. Redact credentials, `PHPSESSID` values and other session secrets in all shared evidence.

### Task 2 – Confirm DVWA and identify file-handling modules

1. Start **Kali-Attacker** and **Ubuntu-Server**. On Ubuntu-Server, identify the authorised lab IP address:

   ```bash
   ip -br addr
   ```

2. Confirm that the DVWA container is running and that the intended HTTP port is published:

   ```bash
   sudo docker ps
   ```

   Confirm the image is `vulnerables/web-dvwa`. If DVWA is missing or unhealthy, use the troubleshooting instructions from earlier labs before continuing.

3. In Kali Firefox, open the authorised DVWA address, for example `http://192.168.1.100:80/` (replace this example with your actual target IP/port). Sign in using the supplied credentials and confirm that the home page loads.
4. Open **DVWA Security** and select **Low** for initial testing.
5. Locate **File Upload** and **File Inclusion**. Record each page's URL/path and the visible user-controlled controls (file selector, submit button, file/page selector or URL parameter). **Do not modify file paths at this stage.**
6. Open Firefox Developer Tools (**F12 → Network**), enable **Persist Logs**, and reload both module pages. For each initial page request record its method, path, status, redirect if present and relevant *cookie names only*.

**Environment record**

| Item | Observation |
|---|---|
| Testing machine | Kali-Attacker |
| Authorised target and IP | |
| DVWA URL and published port | |
| Docker image and container status | |
| Initial HTTP status / redirect | |
| File Upload module path | |
| File Inclusion module path / visible parameter | |
| Initial security level | Low |

**Evidence to capture:** Ubuntu IP, `docker ps`, DVWA URL, both module pages, the relevant Firefox Network entries and completed environment record.

**Expected outcome:** The authorised application and its file-handling entry points have been verified without submitting modified inputs.

---

### Task 3 – Create and verify harmless upload test files

1. On **Kali-Attacker**, open a terminal and create the plain-text file:

   ```bash
   printf 'Week 7 authorised upload test - plain text only.\n' > week7-note.txt
   ```

2. Using **GIMP** (or another approved image editor), create a genuine **100 × 100 pixel** image, optionally with a light-grey background. Export it as **`week7-image.png`**, into the same directory as `week7-note.txt`. Do **not** rename a text file to `.png`.
3. Verify the file sizes and detected types:

   ```bash
   ls -lh week7-note.txt week7-image.png
   file week7-note.txt week7-image.png
   ```

4. Confirm both files exist, the text file is recognised as text, and the PNG is recognised as PNG image data. Neither file should contain scripts or executable content.

**Evidence to capture:** Terminal output showing both filenames, their sizes and their detected file types.

**Expected outcome:** Two genuine, harmless files are ready for upload comparison.

---

## Part C – File Upload Baseline and Security-Level Comparison

**Objective:** Establish reliable image and text-file upload baselines and compare observable upload validation at Low, Medium and High DVWA security levels.

### Task 4 – Inspect the upload form and compare harmless uploads

#### Step 1 – Inspect the form before submitting

1. Open **DVWA → File Upload** at **Low** security.
2. Use Firefox Developer Tools (**Inspector**) to identify the form's method, encoding (such as `multipart/form-data`), file input field name, and any visible client-side file restrictions.
3. Record these observations. **Client-side restrictions do not establish server-side validation.**

#### Step 2 – Establish the Low-security upload baseline

1. Open Firefox Developer Tools (**Network**, with **Persist Logs** enabled).
2. Upload **`week7-image.png`**. Record the original filename, verified file type and size; acceptance/rejection; HTTP status; application message; and disclosed storage path, if any.
3. Where the application provides an authorised link to a successfully stored image, verify that it can be viewed **as an image only**. Do not attempt execution or access outside DVWA.
4. Upload **`week7-note.txt`** without changing it. Record the same details and compare the results with the PNG baseline.
5. Do not treat a `200 OK` response as proof of upload success: inspect the application message and any permitted evidence that the file was stored.

#### Step 3 – Examine HTTP evidence

1. In Firefox **Network**, locate each upload request and inspect its method, path, parameters, filename, declared MIME type, status and response body.
2. In **Burp Suite → Proxy → HTTP history**, locate the same requests. Inspect the multipart body, especially the `filename` and *part-level* `Content-Type`, and compare the responses.
3. Send a copy of an unchanged baseline request to **Repeater** if useful. Re-send only where authorised; repeating an upload may create a duplicate file. Record such behaviour.
4. Inspect **Storage** only to identify relevant cookie *names*. Do not publish session values.

#### Step 4 – Compare Low, Medium and High

1. Repeat the **same unmodified PNG and text-file tests** at **Medium** and then **High** security.
2. Capture the same measurements at every level. Change only the security level between sets of tests; otherwise keep the file contents unchanged.
3. Complete the comparison table:

| Security level | Test file | Size | Upload accepted/rejected | HTTP status | Application message / disclosed path |
|---|---|---|---|---|---|
| Low | `week7-image.png` | | | | |
| Low | `week7-note.txt` | | | | |
| Medium | `week7-image.png` | | | | |
| Medium | `week7-note.txt` | | | | |
| High | `week7-image.png` | | | | |
| High | `week7-note.txt` | | | | |

#### Step 5 – Interpret the baseline

Briefly explain what the evidence shows about extension checks, declared MIME types, actual file-content checks and any storage-path disclosures. State whether behaviour differed by security level, and distinguish confirmed observations from untested assumptions. Consider the application's intended upload policy before calling acceptance of a `.txt` file a weakness.

**Evidence to capture:** Upload form attributes, file sizes/types, labelled Firefox and Burp upload requests/responses, any authorised image-viewing result and the six-row comparison table.

**Expected outcome:** A documented baseline for harmless PNG/text uploads and a like-for-like security-level comparison.

---

## Part D – Targeted Upload Validation Checks

**Objective:** Investigate distinct aspects of server-side file handling without repeating the full security-level baseline.

### Task 5 – Test handling of the declared MIME type

1. Choose the **successful or most informative image-upload request** from Task 4 as your baseline, and retain an unchanged copy.
2. In Burp Repeater identify the multipart file part's `filename` and `Content-Type` headers. Do not confuse the *multipart part* `Content-Type` with the overall request header's multipart boundary.
3. In a separate authorised request copy, change **only the file part's declared `Content-Type`** to a benign alternative, for example `text/plain`. Keep the PNG bytes, filename, security level and other parameters unchanged.
4. Submit the modified request **once**, compare it with the unchanged baseline, and record acceptance/rejection, HTTP status, application response and any path reported.
5. Restore the original request in your working copy. Do not infer that content inspection has been proven solely because the declared header affected the result: it is client-supplied metadata.

| Property | Original PNG request | Modified MIME-type request |
|---|---|---|
| Filename | | |
| File bytes | Genuine PNG | Same genuine PNG |
| Part-level `Content-Type` | | `text/plain` |
| HTTP status | | |
| Upload outcome / message | | |

**Evidence to capture:** Labelled baseline and modified multipart request snippets and their responses, with session secrets redacted.

**Expected outcome:** Explain how DVWA treats a changed client-declared MIME type, without claiming that this alone proves or disproves server-side content inspection.

---

### Task 6 – Test benign filename handling and collisions

1. Using the image editor or file manager, make a copy of the harmless PNG named **`Week 7 Image.png`** (spaces and mixed case); retain valid PNG image content.
2. Upload it to the authorised DVWA File Upload page at the selected and recorded security level.
3. Record the supplied filename and whether the server preserves, normalises or renames it; note any disclosed storage path or relevant error.
4. Repeat the **same harmless upload once** to observe how the application handles a duplicate filename. Record whether it overwrites, renames, rejects or otherwise handles the collision, *only when supported by observable evidence*.
5. Do **not** use traversal strings, operating-system directory paths or executable extensions.

| Attempt | Client filename | Result/status | Returned filename/path | Observed collision behaviour |
|---|---|---|---|---|
| Initial | `Week 7 Image.png` | | | Not applicable |
| Repeat | `Week 7 Image.png` | | | |

**Evidence to capture:** Both upload requests/responses and the original versus returned filenames or paths.

**Expected outcome:** Describe observed filename normalisation and duplicate-handling behaviour, without assuming an overwrite occurred unless it was verified.

---

### Task 7 – Test modest file-size differences

1. Retain the small **100 × 100 PNG** as the baseline.
2. In GIMP, create a **larger but still modest** genuine PNG (for example, **600 × 600 pixels**) named `week7-image-large.png`. Do not generate excessive traffic or unusually large files.
3. Confirm actual file types and sizes:

   ```bash
   ls -lh week7-image.png week7-image-large.png
   file week7-image.png week7-image-large.png
   ```

4. Upload both at the **same recorded DVWA security level**. Record each file's byte size, HTTP status, application message and acceptance/rejection.
5. If a visible browser-side limit or server-side limit is reported, record its actual wording. Do not infer a numeric limit merely because one file succeeds and another fails.

| File | Actual size (bytes) | Security level | HTTP status | Accepted/rejected | Message / limit observed |
|---|---|---|---|---|---|
| `week7-image.png` | | | | | |
| `week7-image-large.png` | | | | | |

**Evidence to capture:** File sizes/types and corresponding upload requests and server responses.

**Expected outcome:** Explain what the limited comparison does—and does not—establish about upload-size controls.

---


# Part C – File Inclusion and Path Handling

**Objective:** Examine how DVWA selects and loads application pages using URL parameters, establish normal file inclusion behaviour, test how the application handles an invalid page reference, and compare path validation across Low, Medium and High security levels.

**Target:** Authorised DVWA application on Ubuntu-Server.

**Tools:** Firefox Developer Tools, Burp Suite (Proxy and Repeater).

**Important:** Use only the authorised DVWA File Inclusion module and harmless test values. Do not attempt to access operating-system files, credentials, configuration files or resources outside the permitted lab scope.

---

## Task 11 – Observe Normal File Inclusion Navigation

**Objective:** Identify how DVWA uses a URL parameter to select application pages and establish the expected behaviour when legitimate navigation links are used.

### Step 1 – Open the File Inclusion Module

1. On **Kali-Attacker**, open Firefox.
2. Log in to the authorised DVWA application.
3. Set the DVWA security level to **Low**.
4. Select **File Inclusion** from the DVWA navigation menu.
5. Confirm that the File Inclusion page loads.

**Example application address:**

```text
http://192.168.1.100/vulnerabilities/fi/
```

Replace the example IP address with your actual authorised target address.

### Step 2 – Examine the Available Page Links

1. Identify the page links displayed within the File Inclusion module.
2. Before clicking a link, observe its destination using the browser's link preview or Firefox Inspector.
3. Click each application-provided link separately.
4. Record the resulting URL and visible page content.
5. Return to the File Inclusion module before testing the next link.

Depending on the DVWA version, the module may provide links similar to:

- `file1.php`
- `file2.php`
- `file3.php`

**Important:** These are example page names. Record the actual names shown in your DVWA environment.

Do not manually modify the URLs during this task.

### Step 3 – Identify the Page-Selection Parameter

Examine the URL after selecting an available page.

For example:

```text
http://192.168.1.100/vulnerabilities/fi/?page=file1.php
```

In this example:

- `/vulnerabilities/fi/` identifies the File Inclusion module.
- `page` is the query parameter.
- `file1.php` is the parameter value identifying the selected page.

Record the actual parameter and values observed in your environment.

### Step 4 – Inspect Requests Using Firefox Developer Tools

1. Press **F12** to open Firefox Developer Tools.
2. Select the **Network** panel.
3. Enable **Persist Logs**.
4. Select one of the File Inclusion page links.
5. Locate the corresponding HTTP request.
6. Open the **Headers** panel and record:
   - Request method.
   - Request URL and path.
   - Query parameter name and value.
   - HTTP response status.
   - Redirect destination, if applicable.
7. Inspect the **Response** panel and identify the content returned.

Repeat for each application-provided page link.

### Step 5 – Record Normal Navigation Results

Complete the following table.

| Page Link | Query Parameter | Parameter Value | HTTP Status | Page Loaded Successfully? | Visible Content |
|---|---|---|---|---|---|
| Page 1 | | | | | |
| Page 2 | | | | | |
| Page 3 | | | | | |

### Questions

1. Which query parameter controls the selected page?
2. Did the application use the same request path for all three page links?
3. What changed in the URL when a different page was selected?
4. Did each application-provided link return the expected content?
5. Does the observed behaviour demonstrate that the parameter affects page selection? Explain using your evidence.

### Evidence to Capture

- DVWA File Inclusion page showing the available links.
- Browser address bar showing a selected page parameter.
- Firefox Network request showing the parameter and HTTP status.
- Response content for at least two legitimate page selections.
- Completed normal navigation table.

**Expected Outcome:** Students identify the page-selection parameter, record legitimate page values and establish the expected behaviour of normal File Inclusion navigation.

---

## Task 12 – Establish a Controlled Path Baseline Using Burp Repeater

**Objective:** Capture a legitimate File Inclusion request and reproduce its normal behaviour using Burp Suite before modifying any request parameters.

### Step 1 – Capture a Legitimate Page Request

1. Keep DVWA security set to **Low**.
2. Open **Burp Suite**.
3. Use the Burp browser or the Firefox browser configured to send traffic through Burp.
4. Navigate to **DVWA → File Inclusion**.
5. Select one legitimate page link, such as `file1.php`.
6. In Burp Suite, open **Proxy → HTTP history**.
7. Locate the corresponding File Inclusion request.

An example request may resemble:

```http
GET /vulnerabilities/fi/?page=file1.php HTTP/1.1
Host: 192.168.1.100
Cookie: PHPSESSID=[REDACTED]; security=low
```

**Note:** The example is illustrative. Use the actual request captured from your lab environment.

### Step 2 – Send the Request to Repeater

1. Right-click the captured request.
2. Select **Send to Repeater**.
3. Open **Repeater**.
4. Confirm that the request method, path and `page` parameter remain unchanged.
5. Click **Send**.
6. Examine the returned HTTP response.

### Step 3 – Record the Baseline Response

Record:

- HTTP request method and path.
- Query parameter and value.
- HTTP response status.
- Response body length or the length reported by Burp.
- Visible application content.
- Any relevant error or redirect.

**Important:** Use the same response-length measurement consistently throughout subsequent comparisons. Do not confuse an HTTP `Content-Length` header with the total response length reported by Burp.

### Step 4 – Confirm Baseline Reproducibility

1. Keep the original request unchanged.
2. Click **Send** again.
3. Compare the second response with the first.
4. Confirm whether:
   - The HTTP status is unchanged.
   - The selected page content is unchanged.
   - The response length is consistent.
   - Any differences are attributable to dynamic page content or other observable changes.

Do not modify the `page` parameter at this stage.

### Step 5 – Record the Results

| Observation | First Request | Repeated Request |
|---|---|---|
| HTTP method | | |
| Request path | | |
| Page parameter/value | | |
| Response status | | |
| Response body length | | |
| Page content | | |
| Error observed? | | |
| Consistent result? | | |

### Questions

1. Was the baseline reproduced successfully in Burp Repeater?
2. Did the same page content appear in both responses?
3. Were any differences observed between the two responses?
4. Why is an unchanged baseline useful before testing an invalid page parameter?

### Evidence to Capture

- Original request in Burp Proxy HTTP history.
- Unchanged request in Burp Repeater.
- Corresponding response showing legitimate page content.
- Evidence of the repeated request and completed baseline table.

**Expected Outcome:** Students establish a repeatable HTTP baseline for a legitimate File Inclusion request, allowing controlled comparisons with modified page parameters.

---

## Task 13 – Test a Non-Existent Benign Page

**Objective:** Determine how DVWA handles an invalid page reference and whether its response exposes implementation details.

### Step 1 – Preserve the Baseline

1. Open Burp Repeater.
2. Locate the legitimate File Inclusion request from Task 12.
3. Duplicate the Repeater tab or retain an unchanged copy.
4. Confirm that the original baseline request remains available for comparison.

### Step 2 – Modify Only the Page Parameter

In the duplicated request, change the legitimate page value to a clearly fictitious filename.

**Baseline example:**

```http
GET /vulnerabilities/fi/?page=file1.php HTTP/1.1
```

**Modified example:**

```http
GET /vulnerabilities/fi/?page=week7-not-a-page.php HTTP/1.1
```

Do not modify any other request parameter, HTTP method, session setting or header.

**Important:** `week7-not-a-page.php` is only a harmless, non-existent page reference. Students are not required to create this file.

### Step 3 – Submit the Controlled Request

1. Click **Send** in Burp Repeater.
2. Inspect the HTTP response.
3. Record:
   - HTTP status.
   - Response body length.
   - Any application error message.
   - Whether normal page content remains visible.
   - Whether the application redirects to another page.
   - Whether the response reveals a server-side file path, warning or implementation detail.

Do not assume a particular error message or HTTP status will occur.

### Step 4 – Compare with the Baseline

Complete the following table.

| Observation | Legitimate Page | Non-Existent Page |
|---|---|---|
| Page parameter value | `file1.php` | `week7-not-a-page.php` |
| HTTP status | | |
| Response body length | | |
| Page content returned | | |
| Error or warning displayed | | |
| Redirect observed | | |
| Server-side path disclosed? | | |

### Step 5 – Interpret the Result

Answer the following questions:

1. Did DVWA reject the non-existent page, display an error or show fallback content?
2. Did the HTTP status differ from the legitimate page request?
3. Did the response reveal server-side file paths or implementation details?
4. Did the application include the requested fictitious file, or did it only attempt to process the input?
5. Why does an error message alone not demonstrate successful local file inclusion?

**Important:** A warning that a file could not be opened may suggest that the supplied value reached a file-handling function. However, this alone does not validate a local file inclusion vulnerability or demonstrate access to unauthorised files.

### Evidence to Capture

- Original legitimate request.
- Modified request containing `week7-not-a-page.php`.
- Resulting HTTP response.
- Any displayed error or fallback message.
- Completed baseline-versus-modified comparison table.

**Expected Outcome:** Students determine how DVWA responds to an unknown page reference and distinguish file-handling errors from evidence of successful unauthorised file inclusion.

---

## Task 14 – Compare File Inclusion Validation Across Security Levels

**Objective:** Compare how DVWA handles legitimate and non-existent page references at Low, Medium and High security levels.

### Step 1 – Confirm the Low-Security Results

Use the observations already collected in Tasks 12 and 13.

Record:

- Response to a legitimate page selection.
- Response to the non-existent `week7-not-a-page.php` value.
- HTTP status and response length.
- Error, warning, redirect or fallback behaviour.

You do not need to repeat these requests unless the original evidence is incomplete.

### Step 2 – Repeat at Medium Security

1. Open **DVWA → DVWA Security**.
2. Change the security level to **Medium**.
3. Click **Submit**.
4. Confirm that the selected security level is Medium.
5. Return to the File Inclusion module.
6. Open a legitimate application-provided page.
7. Capture the corresponding request in Burp Suite.
8. Send the request to Repeater.
9. Record the normal page response.
10. Change only the `page` parameter to:

   ```text
   week7-not-a-page.php
   ```

11. Send the modified request once.
12. Record the response and compare it with the legitimate page.

**Important:** When the DVWA security level changes, the `security` cookie value may also change. Capture a fresh valid request for that security level rather than reusing an outdated session or security-level setting.

### Step 3 – Repeat at High Security

1. Change DVWA security to **High**.
2. Confirm that the selected security level is High.
3. Return to the File Inclusion module.
4. Capture a legitimate page-selection request.
5. Send the unchanged request to Burp Repeater.
6. Record the normal response.
7. Replace only the selected page value with:

   ```text
   week7-not-a-page.php
   ```

8. Send the modified request.
9. Record the response status, body length and visible content.

### Step 4 – Complete the Security-Level Comparison

| Security Level | Page Value | HTTP Status | Response Body Length | Error/Redirect Behaviour | Result |
|---|---|---|---|---|---|
| Low | Legitimate page | | | | |
| Low | `week7-not-a-page.php` | | | | |
| Medium | Legitimate page | | | | |
| Medium | `week7-not-a-page.php` | | | | |
| High | Legitimate page | | | | |
| High | `week7-not-a-page.php` | | | | |

### Step 5 – Analyse Validation Differences

Using the completed table, answer:

1. Did all three security levels allow legitimate page navigation?
2. How did each security level respond to the non-existent page?
3. Were there differences in HTTP status, response length, error messages or redirects?
4. Did any security level display server-side paths or implementation warnings?
5. Did the observations suggest restricted page selection or fixed navigation behaviour?
6. Can the evidence establish that an allow-list is implemented, or would further testing or code inspection be required?
7. Was unauthorised file inclusion actually demonstrated at any security level?

**Important:** Do not conclude that a security level prevents all file inclusion vulnerabilities merely because the fictitious page was rejected. Likewise, do not claim a confirmed vulnerability based only on an error message.

### Evidence to Capture

- Legitimate and invalid page requests at each security level.
- Relevant Burp Repeater responses.
- Screenshots of any differences in errors, redirects or displayed content.
- Completed six-row comparison table.

**Expected Outcome:** Students identify and explain observable differences in page-selection and file-handling behaviour across Low, Medium and High security levels while avoiding unsupported conclusions about vulnerability validation.

---

## Task 15 – Assess File Inclusion Risks and Recommend Remediation

**Objective:** Interpret the observed behaviour from Tasks 11–14, identify potential security risks and recommend controls for safer server-side file handling.

### Step 1 – Explain the Page-Selection Mechanism

Using your evidence, briefly explain:

1. How the File Inclusion module uses the `page` query parameter.
2. How changing the parameter value affects the requested page.
3. What the application returns when a valid page is selected.
4. What happens when a non-existent page is requested.

Reference the relevant request and response evidence from Tasks 11–14.

### Step 2 – Distinguish Observed Behaviour from Potential Risk

Complete the following table.

| Observation | Supported Interpretation | Unsupported Conclusion |
|---|---|---|
| Legitimate page loads | The application recognises the selected page value | All page values are unrestricted |
| Non-existent page produces an error | The application rejects or cannot process that page reference | Local file inclusion is confirmed |
| Server-side path appears in an error | Implementation information may be disclosed | Sensitive files can be accessed |
| Behaviour changes at High security | The application handles the tested input differently | Every file inclusion weakness is prevented |

Add at least one additional observation from your testing.

### Step 3 – Identify Potential File Inclusion Risks

Briefly explain why unrestricted file or path selection may create security risks.

Consider:

- Access to files outside the intended page list.
- Exposure of internal application paths.
- Disclosure of sensitive information.
- Unintended processing of local files.
- Inconsistent validation of user-controlled page values.

**Important:** Describe these as potential risks unless the corresponding behaviour was directly demonstrated during authorised testing.

### Step 4 – Recommend Server-Side Controls

For each potential weakness, recommend an appropriate remediation.

| Potential Weakness | Recommended Control |
|---|---|
| Unrestricted page selection | Use a fixed allow-list of permitted page identifiers |
| User input directly used as a file path | Map trusted page identifiers to predefined server-side files |
| Unexpected directory or path references | Enforce strict server-side path validation and containment |
| Detailed file errors exposed to users | Return generic errors and log technical details securely |
| Excessive filesystem permissions | Run the application using least-privilege permissions |

Explain why a fixed allow-list is generally safer than accepting arbitrary filenames or paths directly from a URL parameter.

### Step 5 – Write a Short Security Interpretation

Write approximately **150–200 words** addressing:

1. The authorised DVWA File Inclusion module tested.
2. The legitimate page-selection behaviour.
3. The response to the non-existent test page.
4. Differences observed across Low, Medium and High security levels.
5. Any implementation details disclosed by the responses.
6. Recommended remediation.
7. Whether a file inclusion vulnerability was actually validated.

Your interpretation must distinguish actual observations from assumptions or potential security impacts.

### Evidence to Capture

- Completed observation and interpretation table.
- Completed remediation table.
- Written 150–200-word security interpretation.
- References to supporting HTTP evidence from Tasks 11–14.

**Expected Outcome:** Students understand the security implications of user-controlled file-selection parameters, identify evidence-based file-handling risks and recommend appropriate server-side validation controls.

---

## Part C – Evidence and Security Requirements

Students must:

- Use only the authorised DVWA File Inclusion module.
- Preserve an unchanged baseline request.
- Modify only one controlled variable at a time.
- Use legitimate page names and the specified harmless fictitious filename.
- Avoid operating-system paths, sensitive files, credentials and configuration files.
- Redact `PHPSESSID`, credentials and other session secrets in screenshots.
- Record actual HTTP responses instead of assuming expected outcomes.
- Distinguish attempted file inclusion from successful unauthorised file inclusion.
- Avoid claiming vulnerabilities unless supported by direct evidence.
- Save clearly labelled evidence in the designated Week 7 evidence folder.

**Final Expected Outcome:** Students produce a documented comparison of legitimate and invalid File Inclusion requests across security levels, supported by Firefox Developer Tools and Burp Suite evidence, together with an interpretation of potential file-handling risks and appropriate remediation.

---


# Part D – Server-Side Handling and Impact Assessment

**Objective:** Analyse how DVWA processes uploaded files and file inclusion requests, distinguish demonstrated security weaknesses from potential risks, recommend secure file-handling controls, and design appropriate remediation verification tests.

**Target:** Authorised DVWA application running on Ubuntu-Server.

**Tools:** Firefox Developer Tools, Burp Suite (Proxy and Repeater), and existing evidence from Parts B and C.

**Important:** This part primarily involves analysing previously captured evidence. Repeat requests only when necessary to clarify an observation. Do not introduce executable files, malicious payloads, sensitive file paths, or requests outside the authorised lab scope.

---

## Task 16 – Map the File-Handling Data Flow

**Objective:** Create a simple diagram illustrating how untrusted user input travels from the browser to the DVWA server and how upload and file inclusion requests should be validated.

### Step 1 – Identify the Main Components

Using the observations collected in earlier tasks, identify:

1. **Browser / Burp Suite** – Where the user submits files or page-selection parameters.
2. **DVWA Request Handler** – Where the server receives and processes HTTP requests.
3. **File Upload Endpoint** – Where uploaded filenames, file content, and metadata are received.
4. **File Inclusion Endpoint** – Where a user-controlled parameter identifies a requested application page.
5. **Validation Controls** – Where the server should check whether the requested operation is permitted.
6. **Server Response** – Where the application returns accepted content, a success message, or a rejection.

### Step 2 – Draw the Data-Flow Diagram

Using Microsoft PowerPoint, diagrams.net, or another approved diagram tool, create a diagram similar to the following:

```text
                     Browser / Burp Suite
                      (Untrusted Input)
                              |
                              v
                     DVWA Request Handler
                              |
                 +------------+------------+
                 |                         |
                 v                         v
          File Upload Endpoint     File Inclusion Endpoint
                 |                         |
                 v                         v
          File-Type / Size          Page Parameter
             Validation               Validation
                 |                         |
          +------+------+           +------+------+
          |             |           |             |
          v             v           v             v
       Accepted       Rejected    Approved       Rejected
          |             |          Page             |
          v             v           |              v
       Secure        Upload         v           Safe Error
       Storage       Error       Display         Response
          |                       Content
          v
       Upload
       Response
```

**Note:** This diagram illustrates recommended secure processing. It does not establish that DVWA implements every validation control shown.

### Step 3 – Annotate Untrusted Input

Identify where user-controlled data enters the application.

For the **File Upload** functionality, consider:

- Uploaded filename.
- Filename extension.
- Declared MIME type.
- Actual file content.
- File size.

For the **File Inclusion** functionality, consider:

- Query parameter name.
- Requested page value.
- User-supplied filename or page identifier.

Mark these points in your diagram as **Untrusted Input**.

### Step 4 – Identify Where Validation Should Occur

Add annotations showing where server-side validation should be performed.

For file uploads, indicate where the server should:

1. Check permitted filename extensions.
2. Validate actual file type and content where appropriate.
3. Enforce file-size limits.
4. Generate or normalise safe stored filenames.
5. Store files in an appropriate non-executable location.

For file inclusion, indicate where the server should:

1. Check the requested page against an approved list.
2. Map permitted page identifiers to predefined server-side files.
3. Reject unexpected page values.
4. Prevent access outside approved application content.

### Step 5 – Identify Safe Rejection Behaviour

Explain what the server should return when an upload or page-selection request is rejected.

A secure application should:

- Clearly indicate that the requested operation was unsuccessful.
- Avoid exposing absolute server paths or sensitive implementation details.
- Avoid storing rejected files.
- Avoid including unauthorised content.
- Record relevant technical information in internal logs where appropriate.

**Important:** Do not assume that a rejected operation must return a particular HTTP status. Record the actual behaviour observed in DVWA.

### Step 6 – Explain the Data Flow

Write approximately **100–150 words** explaining:

1. How uploaded files reach the server.
2. How page-selection parameters influence file inclusion.
3. Where input validation should occur.
4. Why validation should be enforced on the server rather than relying only on browser controls.
5. How rejected requests should be handled.

### Evidence to Capture

- Completed file-handling data-flow diagram.
- Clearly labelled untrusted input locations.
- Validation and rejection points.
- Short explanation of the diagram.

**Expected Outcome:** Students can map upload and file inclusion request flows, identify trust boundaries, and explain the role of server-side validation and safe rejection handling.

---

## Task 17 – Review HTTP Response Headers and Error Handling

**Objective:** Examine how DVWA responds to successful and unsuccessful file-handling requests and determine whether the responses disclose unnecessary implementation details.

### Step 1 – Select Relevant Requests

Using your evidence from Parts B and C, select the following requests where available:

1. A successful or normally processed file upload.
2. A rejected or unsuccessful file upload.
3. A legitimate File Inclusion page request.
4. A request containing the fictitious page value `week7-not-a-page.php`.

If a particular test did not produce a rejection, record this limitation rather than inventing a failed response.

### Step 2 – Inspect Responses in Firefox Developer Tools

1. Open Firefox Developer Tools using **F12**.
2. Select the **Network** panel.
3. Locate the relevant file-handling request.
4. Open the **Headers** panel.
5. Record:
   - HTTP response status.
   - `Content-Type`.
   - `Content-Length`, if provided.
   - `Location`, if a redirect occurs.
   - `Cache-Control`, if present.
   - Other relevant security headers.
6. Open the **Response** panel and examine the returned content.

Do not treat an absent header as a vulnerability without considering the application behaviour and security requirements.

### Step 3 – Inspect the Same Responses in Burp Suite

1. Open **Burp Suite → Proxy → HTTP history**.
2. Locate the corresponding request.
3. Inspect the raw HTTP response.
4. Compare the results with Firefox Developer Tools.
5. Where a request was previously tested in Repeater, review the Repeater response.
6. Confirm whether the recorded HTTP status and response content are consistent.

**Important:** A successful HTTP response does not necessarily mean the application accepted the uploaded file or included the requested page.

### Step 4 – Examine Error Handling

For an unsuccessful request, determine whether the response displays:

- A generic application error.
- A file-not-found message.
- A PHP warning or exception.
- An absolute filesystem path.
- A server-side script or function name.
- Other internal implementation details.

If the response contains an absolute path, record the relevant evidence without attempting to access that path.

### Step 5 – Compare Responses

Complete the following table.

| Observation | Normal Upload | Failed Upload | Normal Inclusion | Unknown Page |
|---|---|---|---|---|
| HTTP status | | | | |
| Content-Type | | | | |
| Response length | | | | |
| Redirect observed? | | | | |
| Cache-Control | | | | |
| Application message | | | | |
| Server path disclosed? | | | | |
| Internal error details? | | | | |

Use **Not observed** or **Not applicable** where appropriate.

### Step 6 – Analyse the Results

Answer the following questions:

1. Did successful and unsuccessful file-handling requests return different HTTP statuses?
2. Were redirects observed, and what triggered them?
3. Did the response content clearly indicate whether an operation succeeded or failed?
4. Did any errors disclose absolute server paths or internal implementation details?
5. Were any response headers relevant to caching or handling uploaded content?
6. Which observations represent actual information exposure, and which are only normal application behaviour?

**Important:** Report information exposure only when the response directly displays internal details. Do not infer sensitive information disclosure merely from an HTTP error code.

### Evidence to Capture

- At least one normal file-handling response.
- At least one rejected or unsuccessful response, if observed.
- Relevant response headers and application messages.
- Completed HTTP response comparison table.
- A short interpretation of any demonstrated information disclosure.

**Expected Outcome:** Students can interpret HTTP responses and distinguish normal application errors from observable information disclosure.

---

## Task 18 – Assess Demonstrated and Potential Security Impact

**Objective:** Evaluate the security implications of the file-handling results and distinguish confirmed observations from possible consequences that were not demonstrated.

### Step 1 – Review Previous Evidence

Review the results recorded in Parts B and C, including:

- Valid PNG image uploads.
- Plain-text file uploads.
- Changes to the declared MIME type.
- Filename handling and duplicate filenames.
- File-size validation.
- Legitimate File Inclusion navigation.
- Non-existent page handling.
- Differences across DVWA security levels.

Use only observations that are supported by your recorded evidence.

### Step 2 – Identify Demonstrated Behaviour

For each test, identify what the application actually did.

Examples of observed behaviour may include:

- Accepting or rejecting a file.
- Displaying a stored filename or upload path.
- Preserving or changing a submitted filename.
- Returning different responses for modified MIME declarations.
- Returning a valid page when a permitted page identifier was supplied.
- Displaying an error when a non-existent page was requested.

Do not assume these behaviours occurred unless they were observed in your testing.

### Step 3 – Distinguish Observation from Interpretation

For each result, explain what the evidence supports.

For example:

| Observed Behaviour | Supported Interpretation | Unsupported Assumption |
|---|---|---|
| PNG upload accepted | DVWA accepted the tested PNG file | Every image type is securely validated |
| Text file rejected | DVWA rejected the tested text file | All unauthorised file types are blocked |
| Unknown page returned an error | The application could not process the requested page | Arbitrary file inclusion is confirmed |
| Uploaded filename changed | The application modified the stored filename | Filename collision attacks are impossible |

### Step 4 – Complete the Security Impact Table

Use your own evidence to complete the table.

| Test | Observed Behaviour | Supported Interpretation | Potential Impact (Not Necessarily Demonstrated) |
|---|---|---|---|
| Valid image upload | | | |
| Text-file upload | | | |
| MIME declaration change | | | |
| Filename handling | | | |
| File-size handling | | | |
| Normal File Inclusion | | | |
| Unknown benign page | | | |

### Step 5 – Consider Security Impact Categories

For each relevant observation, consider whether it has implications for:

**Confidentiality**

Could inappropriate file handling potentially expose information?

**Integrity**

Could insufficient validation potentially allow unauthorised changes to stored files or application content?

**Availability**

Could insufficient file-size restrictions or repeated processing potentially consume excessive resources?

**Information Disclosure**

Did the application actually reveal server paths, filenames or internal error details?

**Important:** Distinguish demonstrated impact from potential impact. For example, accepting an uploaded image does not demonstrate remote code execution, and a file-not-found error does not prove arbitrary file read.

### Step 6 – State Whether a Vulnerability Was Validated

Write a short statement answering:

1. Was a file upload validation weakness directly demonstrated?
2. Was unauthorised file inclusion directly demonstrated?
3. Did the testing reveal internal server information?
4. Were the results sufficient to confirm a vulnerability?
5. What additional authorised testing would be required for inconclusive observations?

Use one of the following forms as appropriate:

- **Validated:** The evidence directly demonstrates the claimed weakness.
- **Not validated:** The test did not demonstrate the claimed weakness.
- **Inconclusive:** The available evidence is insufficient to confirm or exclude the weakness.

### Evidence to Capture

- Completed security impact table.
- References to relevant screenshots and HTTP responses.
- A short statement distinguishing validated findings from potential risks.
- Explanation of any limitations affecting the conclusions.

**Expected Outcome:** Students can produce evidence-based security findings without overstating the consequences of observed file-handling behaviour.

---

## Task 19 – Recommend Secure File-Handling Controls

**Objective:** Recommend appropriate security controls for file uploads and file inclusion, linking each recommendation to an observed weakness or clearly identifying it as a preventive best practice.

### Step 1 – Review the Findings

Review the results from Tasks 11–18 and identify:

1. Any demonstrated file-type validation issues.
2. Any unexpected filename or storage behaviour.
3. Any limitations in file-size handling.
4. Any weaknesses in page-selection validation.
5. Any internal information disclosed through errors.

If no weakness was validated in a particular area, state that the recommendation is preventive.

### Step 2 – Recommend File Upload Controls

Consider the following controls.

**Server-Side File-Type Validation**

Validate permitted extensions and inspect actual file content where appropriate.

**Extension Allow-List**

Accept only file extensions required for the application's intended function.

**File-Size Limits**

Enforce reasonable maximum file sizes on the server.

**Safe Filename Handling**

Generate unique server-side filenames and avoid relying directly on user-supplied names for storage.

**Secure Storage**

Store uploaded files outside executable application directories where feasible, or otherwise ensure uploaded content cannot be executed.

**Least-Privilege Permissions**

Ensure the application can access only the directories and files required for its operation.

**Safe File Delivery**

Use appropriate response content types and content-disposition settings when serving uploaded files.

### Step 3 – Recommend File Inclusion Controls

Consider the following controls.

**Fixed Page Allow-List**

Use approved page identifiers mapped to known application templates.

**Avoid Direct Path Construction**

Do not concatenate arbitrary user input into filesystem paths.

**Canonical Path Validation**

Where dynamic paths are unavoidable, resolve and validate paths against an approved base directory.

**Safe Error Handling**

Return generic user-facing errors without displaying absolute paths or implementation details.

**Internal Logging**

Record failed page-selection attempts securely without exposing sensitive information to users.

### Step 4 – Connect Recommendations to Observations

Complete the following table.

| Observed Finding or Validation Gap | Security Risk | Recommended Control | Evidence Reference | Recommendation Type |
|---|---|---|---|---|
| | | | | |
| | | | | |
| | | | | |
| | | | | |

For **Recommendation Type**, use:

- **Evidence-based remediation** – Addresses a weakness demonstrated by your testing.
- **Preventive best practice** – Improves security without a directly validated weakness.

### Step 5 – Explain Key Recommendations

Choose at least three recommendations and briefly explain:

1. What weakness or risk the recommendation addresses.
2. How the control reduces the risk.
3. Where the control should be implemented.
4. What evidence would demonstrate that the control works.

At least one explanation should address upload validation, and one should address File Inclusion validation.

### Questions

1. Why is server-side validation more reliable than browser-only restrictions?
2. Why should uploaded filenames not be trusted directly?
3. Why should uploaded files generally be prevented from executing as server-side code?
4. How does a fixed allow-list reduce risks associated with File Inclusion parameters?
5. Why should applications avoid exposing detailed filesystem errors?
6. How can least-privilege permissions reduce the consequences of a file-handling weakness?

### Evidence to Capture

- Completed recommendations table.
- Explanations of at least three recommended controls.
- References linking recommendations to observed evidence.
- Identification of preventive recommendations where no weakness was directly validated.

**Expected Outcome:** Students can propose realistic, technically appropriate remediation controls and explain how those controls address file-handling risks.

---

## Task 20 – Design a Remediation Verification Test

**Objective:** Design a controlled retest that could determine whether a recommended file-handling security control successfully addresses an observed weakness or validation gap.

### Step 1 – Select One Finding

Choose one result from earlier tasks, such as:

- An unexpected file type being accepted.
- A declared MIME-type change affecting upload acceptance.
- Unsafe or inconsistent filename handling.
- Missing or unclear file-size restrictions.
- A file inclusion error exposing implementation details.
- Unexpected handling of an unknown page value.

Select a finding that is supported by your evidence.

If no weakness was validated, select a validation gap or preventive control and label the retest as proposed.

### Step 2 – Define the Expected Security Control

Describe the recommended change.

For example:

| Observed Issue | Proposed Control |
|---|---|
| Unexpected file type accepted | Enforce a server-side extension and content allow-list |
| Filename preserved without safe handling | Generate unique server-side filenames |
| Oversized file accepted | Enforce a server-side maximum file size |
| Unknown page reveals an absolute path | Return a generic error without internal path details |
| Page parameter accepts unrestricted values | Map approved identifiers to fixed templates |

### Step 3 – Define the Expected Behaviour After Remediation

Explain what a successful remediation should change.

For example:

- A previously accepted disallowed file type should be rejected.
- A permitted PNG upload should continue working.
- An oversized file should be rejected without excessive processing.
- Uploaded filenames should be normalised or replaced with safe generated names.
- An unknown page request should return a safe error without exposing internal paths.

**Important:** A successful remediation should address the weakness without unnecessarily breaking legitimate application functionality.

### Step 4 – Design a Before-and-After Test

Use the same benign request before and after the proposed fix.

For each comparison, keep consistent:

- Test file or page parameter.
- Target application and endpoint.
- Relevant request parameters.
- File content and size.
- Authentication context.
- Evidence collection method.

Complete the following table.

| Item | Before Remediation | After Remediation |
|---|---|---|
| Target endpoint | | |
| Test input | | |
| HTTP method | | |
| HTTP status | | |
| Application response | | |
| File accepted/page loaded? | | |
| Security weakness observed? | | |
| Expected secure behaviour | | |

If the remediation is not implemented, leave the actual after-remediation results as **Not tested** and record the expected secure behaviour separately.

### Step 5 – Specify the Retest Procedure

Write a short retest procedure containing:

1. The selected baseline request.
2. The observed behaviour before remediation.
3. The proposed server-side change.
4. The same request to be repeated after remediation.
5. The expected secure response.
6. How the response will be verified using Firefox Developer Tools or Burp Repeater.
7. How legitimate functionality will be checked to confirm it still works.

### Step 6 – Record the Retest Status

Clearly state one of the following:

**Remediation implemented and retested**

Use only when a code or configuration change was actually applied and tested. Record the actual before-and-after evidence.

**Proposed retest only — no code change performed**

Use when students cannot modify the DVWA application or are only designing a remediation verification procedure.

**Important:** Changing DVWA from Low to Medium or High security is a security-level comparison. It does not, by itself, prove that a recommended code-level remediation was implemented.

### Step 7 – Write a Verification Conclusion

Write approximately **100–150 words** explaining:

1. The selected weakness or validation gap.
2. The proposed remediation.
3. What should change after remediation.
4. How the same benign test would verify the change.
5. Whether the remediation was implemented and tested.
6. What limitations remain.

### Evidence to Capture

- Selected finding and corresponding baseline evidence.
- Proposed remediation control.
- Completed before-and-after verification table.
- Written retest procedure.
- Clear statement of retest status.
- Actual retest evidence, only if remediation was implemented.

**Expected Outcome:** Students can design a controlled remediation verification procedure, distinguish proposed testing from completed retesting, and define measurable evidence of successful security improvements.

---


# Part E – Reporting, Evidence Validation and Lab Cleanup

**Objective:** Consolidate the evidence collected during Week 7 into a professional security assessment report covering DVWA File Upload and File Inclusion, including validated observations, potential risks, remediation recommendations, retesting status and testing limitations.

**Target:** Authorised DVWA application on Ubuntu-Server.

**Tools:** Kali Linux, Firefox Developer Tools, Burp Suite and the evidence collected in Parts B, C and D.

**Required Deliverables:**

1. Completed findings table.
2. A 400–500-word Findings Summary.
3. Selected, clearly labelled screenshots and HTTP evidence.
4. Final evidence validation and cleanup confirmation.

---

## Task 21 – Create the Week 7 Findings Table

**Objective:** Consolidate file upload and File Inclusion observations into a structured, evidence-based findings table.

### Step 1 – Create the Findings Table

On **Kali-Attacker**, open a terminal and create the reporting directory if it does not already exist:

```bash
mkdir -p ~/lab-evidence/week7
```

Create the findings table:

```bash
nano ~/lab-evidence/week7/09-findings-table.md
```

Record the results using the evidence collected in Parts B, C and D.

### Step 2 – Identify the Findings to Include

Review the following activities:

**File Upload Testing**

- Valid PNG image upload.
- Plain-text file upload.
- Declared MIME-type modification.
- Filename handling and duplicate filenames.
- File-size validation.
- Low, Medium and High security-level comparisons.

**File Inclusion Testing**

- Normal page-selection behaviour.
- Legitimate File Inclusion baseline.
- Non-existent benign page request.
- Low, Medium and High security-level comparisons.
- Server-side error handling and disclosed implementation details.

Include only activities actually performed.

### Step 3 – Complete the Findings Table

Use the following structure:

| Area | Endpoint / Parameter | Baseline | Modified Input | Observed Result | Evidence File | Validated Finding? | Remediation |
|---|---|---|---|---|---|---|---|
| File upload – PNG | | | | | | | |
| File upload – TXT | | | | | | | |
| MIME-type validation | | | | | | | |
| Filename handling | | | | | | | |
| File-size validation | | | | | | | |
| File upload – security levels | | | | | | | |
| File Inclusion – normal page | | | | | | | |
| File Inclusion – unknown page | | | | | | | |
| File Inclusion – security levels | | | | | | | |

For each row:

- **Endpoint / Parameter:** Record the relevant request path or user-controlled parameter.
- **Baseline:** Record the normal input and observed result.
- **Modified Input:** Record the controlled change introduced during testing.
- **Observed Result:** Describe the actual HTTP and application behaviour.
- **Evidence File:** Identify the supporting screenshot or captured response.
- **Validated Finding?:** State whether the evidence confirms a security weakness.
- **Remediation:** Recommend an appropriate control, or identify it as a preventive best practice.

### Step 4 – Classify Each Finding

Use one of the following classifications:

| Classification | Meaning |
|---|---|
| Validated | Direct evidence demonstrates the claimed security weakness |
| Not validated | The test did not demonstrate the claimed security weakness |
| Inconclusive | The available evidence is insufficient to confirm or exclude the weakness |
| Not tested | The activity was not performed |

**Important:**

- A successful image upload does not automatically demonstrate a vulnerability.
- Acceptance of a plain-text file does not necessarily demonstrate insecure validation.
- Changing a client-supplied MIME type does not, by itself, prove that the server validates actual file content.
- A file-not-found error does not establish successful local file inclusion.
- Do not claim remote code execution, arbitrary file access or server compromise without direct authorised validation.

### Step 5 – Link Findings to Evidence

Each finding must reference the relevant evidence.

For example:

- Screenshot showing the original upload request.
- Screenshot showing the modified MIME declaration.
- Burp Repeater response showing the resulting application behaviour.
- File Inclusion baseline and unknown-page responses.
- Security-level comparison evidence.

Use descriptive filenames so the evidence can be located easily.

### Evidence to Capture

- Completed `09-findings-table.md`.
- Relevant supporting screenshots and HTTP responses.
- Clear classification of each observed finding.
- Evidence-linked remediation recommendations.

**Expected Outcome:** A professional findings table that links each tested input to its observed behaviour, supporting evidence, interpretation and recommended remediation.

---

## Task 22 – Write a 400–500-Word Findings Summary

**Objective:** Produce a concise professional report that connects the Week 7 evidence, technical observations, security implications, remediation recommendations, retesting status and limitations.

### Step 1 – Create the Findings Summary File

On Kali-Attacker, execute:

```bash
nano ~/lab-evidence/week7/10-findings-summary.md
```

Write **400–500 words** using the results recorded in Task 21.

Do not count tables, figure captions, evidence filenames or Markdown headings towards the required narrative word count.

### Step 2 – Introduce the Authorised Target

Begin the summary by identifying:

1. The authorised Ubuntu-Server target.
2. The DVWA application address.
3. The File Upload and File Inclusion modules.
4. The testing environment and tools used.
5. The scope of the authorised assessment.

**Suggested opening:**

> File-handling security testing was conducted against the authorised DVWA application hosted on Ubuntu-Server at [IP address] using Firefox Developer Tools and Burp Suite. The assessment examined file upload validation, File Inclusion page selection and related server-side handling within the designated training modules.

### Step 3 – Summarise the File Upload Baseline

Describe the normal upload behaviour observed using `week7-image.png`.

Include:

- Filename and actual file type.
- Whether the upload was accepted or rejected.
- Relevant HTTP method and response status.
- Application response message.
- Any disclosed storage location.
- Whether the stored image was successfully viewed, if tested.

Do not interpret an HTTP 200 response alone as proof of successful file storage.

### Step 4 – Summarise File Upload Validation Testing

Briefly discuss the activities actually performed, including:

- Plain-text file upload compared with the PNG baseline.
- Declared MIME-type changes.
- Filename handling and duplicate-upload behaviour.
- File-size validation.
- Differences observed at Low, Medium and High security levels.

Explain the most important differences using the actual evidence.

**Important:** Do not describe checks as having been performed if they were only recommended or planned.

### Step 5 – Summarise File Inclusion Testing

Describe the observations collected in Tasks 11–14.

Include:

1. The File Inclusion endpoint.
2. The page-selection parameter.
3. Legitimate page-selection behaviour.
4. The result of requesting `week7-not-a-page.php`.
5. Relevant HTTP status codes and response content.
6. Differences across Low, Medium and High security levels.
7. Any demonstrated disclosure of server-side paths or error details.

Explain whether the evidence confirms a file inclusion vulnerability or only shows how the application processes page-selection inputs.

### Step 6 – Reference Browser and Burp Suite Evidence

Explain how Firefox Developer Tools and Burp Suite were used to support the findings.

Where relevant, reference:

- Upload form attributes.
- Multipart form-data requests.
- Filename and declared Content-Type.
- HTTP request methods and parameters.
- Burp Repeater comparisons.
- File Inclusion requests and responses.
- HTTP response status, body content and errors.

Reference the corresponding evidence filenames in your report.

### Step 7 – Assess Demonstrated Security Impact

Clearly separate:

**Demonstrated behaviour**

What the evidence directly shows.

**Potential security risk**

What could occur if file validation or page-selection controls were insufficient.

For example, a visible absolute server path may indicate information exposure, while arbitrary file access remains unvalidated unless you demonstrate it directly.

Do not claim unsupported impacts such as remote code execution, arbitrary file reading, privilege escalation or system compromise.

### Step 8 – Recommend Remediation

Include recommendations appropriate to the observed results.

**For File Upload:**

- Server-side file-type validation.
- Permitted extension allow-lists.
- File-size restrictions.
- Safe filename generation.
- Secure, non-executable upload storage.
- Least-privilege file permissions.

**For File Inclusion:**

- Fixed allow-list mapping of page identifiers.
- Avoiding direct concatenation of user input into file paths.
- Canonical path validation where necessary.
- Generic application errors and secure internal logging.

Link recommendations to observations, or identify them as preventive best practices.

### Step 9 – State Retesting and Limitations

Clearly explain whether remediation was implemented and retested.

If no code or configuration changes were made, state:

**Proposed retest only — no code change performed.**

Changing DVWA security levels is a comparison of application configurations, not proof that a recommended code-level remediation was implemented.

Briefly acknowledge limitations such as:

- Testing only the designated DVWA modules.
- Using harmless file uploads and page references.
- Limited file types and file sizes.
- No testing of operating-system or sensitive application files.
- No executable payloads or destructive activities.
- Inability to inspect or change server-side source code, if applicable.

### Step 10 – State the Validation Conclusion

Conclude by clearly stating:

1. Whether a file upload validation weakness was validated.
2. Whether a file inclusion vulnerability was validated.
3. Whether internal information disclosure was observed.
4. Which findings remain inconclusive.
5. Whether further authorised testing is required.

### Suggested Summary Structure

The completed narrative should follow this sequence:

1. **Paragraph 1:** Authorised target, modules and baseline behaviour.
2. **Paragraph 2:** File upload validation observations.
3. **Paragraph 3:** File Inclusion observations and Burp comparisons.
4. **Paragraph 4:** Demonstrated impact and remediation recommendations.
5. **Paragraph 5:** Retesting, limitations and vulnerability validation conclusion.

### Step 11 – Check the Word Count

After saving the Markdown file, run:

```bash
wc -w ~/lab-evidence/week7/10-findings-summary.md
```

Use this as an initial check. Confirm the narrative word count separately if the file contains headings, tables or figure captions.

Revise the report until the narrative contains **400–500 words**.

### Evidence to Capture

- Completed `10-findings-summary.md`.
- Word-count verification.
- Evidence references linked to the findings.
- Explicit vulnerability validation and remediation-retest conclusions.

**Expected Outcome:** A concise professional Findings Summary that accurately connects testing evidence, security interpretation, remediation and limitations without overstating the demonstrated impact.

---

# Suggested Evidence Filenames

Use descriptive filenames for the evidence collected throughout the lab.

| Filename | Purpose |
|---|---|
| `01-target-and-modules.png` | Authorised DVWA target and module identification |
| `02-upload-form.png` | File Upload form and attributes |
| `03-image-upload-baseline.png` | Valid PNG upload baseline |
| `04-text-upload-comparison.png` | Plain-text upload comparison |
| `05-mime-request-comparison.png` | MIME declaration comparison in Burp |
| `06-filename-and-size.png` | Filename handling and file-size testing |
| `07-upload-security-levels.png` | Low/Medium/High upload comparison |
| `08-inclusion-baseline.png` | Legitimate File Inclusion page selection |
| `09-inclusion-unknown-page.png` | Non-existent benign page response |
| `10-inclusion-security-levels.png` | File Inclusion security-level comparison |
| `11-file-handling-data-flow.png` | File-handling data-flow diagram |
| `12-response-header-comparison.png` | Normal and failed HTTP response comparison |
| `13-remediation-retest.png` | Retest evidence, only if performed |
| `09-findings-table.md` | Evidence-linked findings table |
| `10-findings-summary.md` | 400–500-word professional Findings Summary |

**Note:** Only submit evidence for activities actually completed. Do not create screenshots or retest results that were not observed.

---

**Final Lab Outcome:** Students produce an evidence-based assessment of DVWA file handling, including upload validation, page-selection controls, server-side behaviour, security implications, remediation recommendations and testing limitations.


