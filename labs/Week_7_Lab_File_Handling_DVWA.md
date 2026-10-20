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

## Task 1 – Create the Week 7 Evidence Folder

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

# Part B – Environment Setup and File Upload Baseline Testing

**Objective:** Verify the authorised DVWA environment, identify the File Upload and File Inclusion modules, create harmless test files, and examine how DVWA validates file uploads at Low, Medium, and High security levels.

**Tools:** Kali Linux, Ubuntu-Server, Firefox Developer Tools, Burp Suite and GIMP.

---

## Task 2 – Confirm the DVWA Environment and Identify File-Handling Modules

### Step 1 – Verify the Authorised Target

1. Start **Kali-Attacker** and **Ubuntu-Server**.
2. On Ubuntu-Server, confirm the IP address:

   ```bash
   ip -br addr
   ```

3. Verify that the DVWA Docker container is running:

   ```bash
   sudo docker ps
   ```

4. Confirm that DVWA is running from the `vulnerables/web-dvwa` image and that HTTP port 80 is published.
5. On Kali-Attacker, open Firefox and navigate to:

   ```text
   http://192.168.1.100:80/
   ```

   Replace this example address with your authorised Ubuntu-Server IP address.

6. Log in using the supplied lab credentials.
7. Confirm that the DVWA home page loads successfully.

**Important:** If DVWA is not listed, is unhealthy, or cannot be accessed, refer to the Docker setup and troubleshooting instructions from earlier labs before continuing.

### Step 2 – Identify the File-Handling Modules

1. In DVWA, open **File Upload**.
2. Record the application URL and identify the upload form, file-selection control and submission button.
3. Open **File Inclusion**.
4. Record the application URL and identify any user-controlled file or page-selection parameters.
5. Do not modify file paths or submit test files at this stage.

### Step 3 – Inspect Initial HTTP Requests

1. Open Firefox Developer Tools using **F12**.
2. Select the **Network** panel and enable **Persist Logs**.
3. Reload the File Upload and File Inclusion pages separately.
4. Inspect the relevant requests and responses.
5. Record:
   - HTTP method and request path.
   - Query parameters, if present.
   - HTTP response status.
   - Redirect destination, if applicable.
   - Relevant cookie names, without recording session values.

### Step 4 – Record the Environment

Complete the table using your actual observations.

| Item | Observed Value |
|---|---|
| Testing machine | Kali-Attacker |
| Authorised target | Ubuntu-Server |
| Ubuntu-Server IP address | |
| DVWA application address | |
| Docker image | |
| Published HTTP port | |
| DVWA container status | |
| Initial HTTP response status | |
| Redirect destination (if any) | |
| File Upload module path | |
| File Inclusion module path | |
| Selected DVWA security level | Low |

### Evidence to Capture

- Ubuntu-Server IP address and DVWA container status.
- DVWA home page showing the authorised application URL.
- File Upload and File Inclusion module pages.
- Firefox Network panel showing initial request and response details.
- Completed environment table.

**Expected Outcome:** The authorised DVWA environment is verified, both file-handling modules are identified, and their initial HTTP behaviour is documented.

---

## Task 3 – Create and Verify Harmless Test Files

**Objective:** Prepare two harmless files for examining upload validation without using scripts or executable content.

### Step 1 – Create a Plain-Text File

On **Kali-Attacker**, open a terminal and execute:

```bash
printf 'Week 7 authorised upload test - plain text only.\n' > week7-note.txt
```

This creates a file named `week7-note.txt`.

### Step 2 – Create a Genuine PNG Image

Use an approved image editor available on Kali Linux, such as **GIMP**.

1. Open GIMP.
2. Select **File → New**.
3. Set the image dimensions to **100 × 100 pixels**.
4. Choose a background colour, such as light grey.
5. Select **File → Export As**.
6. Enter the filename `week7-image.png`.
7. Save the image in the same directory as `week7-note.txt`.

**Important:** Do not simply rename a text file to `.png`. The file must contain valid PNG image data.

### Step 3 – Verify Both Files

In the Kali terminal, execute:

```bash
ls -lh week7-note.txt week7-image.png
file week7-note.txt week7-image.png
```

Confirm that:

- Both files exist.
- `week7-note.txt` is recognised as a plain-text file.
- `week7-image.png` is recognised as PNG image data.
- Neither file contains scripts or executable content.

### Evidence to Capture

Capture the terminal output showing both filenames, file sizes and detected file types.

**Expected Outcome:** Two verified harmless test files are ready for controlled upload testing.

---

## Task 4 – Test File Upload Validation and Compare Security Levels

**Objective:** Examine how DVWA processes genuine PNG and plain-text files, inspect their HTTP requests and responses, and compare upload validation across Low, Medium and High security settings.

### Step 1 – Establish the Low-Security Baseline

1. Open **DVWA → DVWA Security**.
2. Set the security level to **Low** and submit the change.
3. Navigate to **DVWA → File Upload**.
4. Open Firefox Developer Tools and select **Network**.
5. Upload `week7-image.png`.
6. Record:
   - Whether the upload was accepted or rejected.
   - HTTP method and request path.
   - HTTP response status.
   - Application response message.
   - Storage path disclosed, if any.
7. Upload `week7-note.txt` and record the same information.

**Important:** HTTP 200 does not necessarily indicate a successful upload. Confirm the result using the application response and, where authorised, evidence of file storage.

### Step 2 – Inspect Upload Requests in Firefox Developer Tools

For each upload:

1. Locate the relevant request in the **Network** panel.
2. Inspect the **Headers** and **Request** sections.
3. Identify:
   - HTTP method.
   - Request path.
   - Form parameters.
   - Uploaded filename.
   - Declared MIME type.
   - HTTP response status.
   - Application response content.
4. Check the **Storage** panel for relevant cookie names without exposing session values.

Record any observable differences between the two upload requests.

### Step 3 – Inspect the Same Requests in Burp Suite

1. Open **Burp Suite → Proxy → HTTP history**.
2. Locate the requests for `week7-image.png` and `week7-note.txt`.
3. Inspect the multipart form-data request body, where available.
4. Identify the uploaded filename and declared Content-Type.
5. Compare the corresponding server responses.
6. Send an unchanged baseline request to **Burp Suite → Repeater**, where appropriate.
7. Reissue the unchanged request only if it is safe and authorised, and record the response.

**Important:** Repeater testing may submit the same file again. Record any duplicate-upload behaviour and avoid unnecessary repeated submissions.

When comparing requests, modify only **one controlled variable at a time**.

### Step 5 – Repeat at Medium and High Security

1. Change DVWA security to **Medium**.
2. Repeat the PNG and text-file upload tests.
3. Record the results using the same observations as the Low-security baseline.
4. Change DVWA security to **High**.
5. Repeat both tests again.
6. Compare the results across all three security levels.

Use the same two files throughout the testing to maintain a consistent comparison.

### Step 5 – Record the Results

Complete the following table using actual observations.

| Security Level | Test File | Upload Result | HTTP Status | Application Response / Storage Path |
|---|---|---|---|---|
| Low | `week7-image.png` | | | |
| Low | `week7-note.txt` | | | |
| Medium | `week7-image.png` | | | |
| Medium | `week7-note.txt` | | | |
| High | `week7-image.png` | | | |
| High | `week7-note.txt` | | | |

### Step 6 – Analyse Upload Validation

Using your evidence, briefly answer:

1. Were both files accepted at Low, Medium and High security levels?
2. Did DVWA handle the PNG image differently from the text file?
3. What differences were observed in the upload requests and responses?
4. What evidence suggests that DVWA checks file extensions, MIME types or actual file content?
5. Was a server-side storage path disclosed for any successful upload?
6. Did the selected security level change the observed validation behaviour?
7. What additional authorised tests would be required to determine whether file-type restrictions could be bypassed?
8. What server-side controls would you recommend to improve upload security?

**Important:** Do not conclude that a vulnerability exists solely because a file was accepted. Support conclusions with the actual HTTP and application behaviour observed.

### Evidence to Capture

Save clearly labelled evidence showing:

- Successful or unsuccessful uploads of both test files.
- Low, Medium and High security settings.
- Relevant upload requests and responses in Firefox Developer Tools.
- Corresponding requests and responses in Burp Suite.
- Any observed file-type restrictions or storage paths.
- Completed security-level comparison table.

### Expected Outcome

Students should be able to:

- Establish a reliable upload baseline.
- Compare how DVWA handles genuine image and text files.
- Identify observable file-type validation differences.
- Inspect upload requests using Firefox Developer Tools and Burp Suite.
- Distinguish file extensions and declared MIME types from actual file content.
- Explain potential file-handling weaknesses without making unsupported vulnerability claims.
- Recommend appropriate controls, including server-side allow-list validation, file-size limits, safe filename handling, secure storage locations and least-privilege access.

---

### Evidence Handling and Security Requirements

- Conduct testing only against the authorised DVWA training application.
- Use harmless test files without scripts or executable payloads.
- Do not test other applications, servers or external systems.
- Redact `PHPSESSID`, authentication credentials and other session secrets from screenshots and shared reports.
- Preserve relevant HTTP methods, request paths, parameters, status codes and response content.
- Save evidence in the designated Week 7 evidence folder.
- Report only vulnerabilities supported by observed evidence.
- Perform required cleanup or restoration after completing the laboratory activities.

---

## Part B – File upload validation

### Task 4 – Inspect the upload form

1. Open DVWA **File Upload**.
2. Identify the form method and encoding type (e.g., multipart form data).
3. Locate the upload field name and any visible file restrictions.
4. Explain why client-side acceptance does not prove server-side validation.

**Evidence to capture:** Form attributes and upload field.

### Task 5 – Submit a valid image baseline

1. Upload the benign `week7-image.png`.
2. Record the filename, actual file type, size, HTTP request, status, and application message.
3. If a file location is displayed, record the location **only within the authorised DVWA application**.
4. Confirm whether the stored file can be viewed as an image, without attempting code execution.

**Evidence to capture:** Screenshot of the upload result, multipart request and response.

### Task 6 – Test a benign alternative file type

1. Attempt to upload `week7-note.txt` without modifying its contents.
2. Record whether the application accepts or rejects it.
3. Compare the text-file result with the valid-image baseline.
4. Do **not** infer that accepting `.txt` alone demonstrates a vulnerability; consider the intended upload policy.

**Evidence to capture:** Accepted/rejected result, status and message.

### Task 7 – Compare extension and declared content type

1. In Burp, inspect the original multipart upload request for the image.
2. Identify `filename` and the declared part `Content-Type`.
3. In an authorised copy of the request, change **only** the declared part content type to a benign alternate value (e.g., `text/plain`) while keeping the original image bytes and filename unchanged.
4. Submit once in Repeater and compare with the baseline.
5. Restore the original request after testing.

**Evidence to capture:** Side-by-side request changes and observed response.  
**Interpretation prompt:** Does this establish server-side content inspection, or only show how the application treats a client-supplied header?

### Task 8 – Test filename handling safely

1. Upload a harmless image using a descriptive filename with a space or mixed case (e.g., `Week 7 Image.png`).
2. Record whether the server preserves, normalises, or renames the filename.
3. Observe any collision-handling behaviour by repeating a benign upload with the same name.
4. **Do not** supply traversal sequences or filenames targeting operating-system directories.

**Evidence to capture:** Original filename, returned filename/path, and messages.

### Task 9 – Test file size limits

1. Compare a small permitted image with a larger permitted image prepared for the lab.
2. Record file sizes and results, without generating excessive traffic or large files.
3. Note browser-level restrictions separately from server responses.

**Evidence to capture:** Size, response status, message and any configured limit observed.

### Task 10 – Compare DVWA security levels

1. Repeat **one** benign upload scenario at **Low**, **Medium**, and **High** DVWA security.
2. Record the same metrics for each level: input, HTTP status, response, file acceptance, and any changed validation message.
3. If different security levels use different rules, describe only what the captured evidence demonstrates.

**Evidence to capture:** Three labelled comparisons and a table.

---

## Part C – File inclusion and path handling

### Task 11 – Observe normal File Inclusion navigation

1. Open DVWA **File Inclusion**.
2. Select each application-provided page link without manually changing the URL.
3. Identify the query parameter controlling the selected page (typically `page`, if present).
4. Record the allowed page values and successful responses.

**Evidence to capture:** Navigation links, URL parameter and normal page output.

### Task 12 – Establish a controlled path baseline

1. Send one normal File Inclusion page request to Burp Repeater.
2. Record the exact query parameter, response status, body length and visible content.
3. Repeat the unchanged request to confirm consistent baseline behaviour.

**Evidence to capture:** Repeater baseline request/response.

### Task 13 – Test a non-existent benign page

1. Replace the page parameter **only** with a clearly fictitious training filename, such as `week7-not-a-page.php`.
2. Record the server response and any error or fallback page.
3. Do not attempt to reference `/etc/passwd`, keys, application configuration files, or any path outside the authorised training material.
4. Explain whether the response reveals implementation details or simply rejects unknown pages.

**Evidence to capture:** Modified query and resulting response.

### Task 14 – Check parameter validation across security levels

1. Repeat the **normal permitted page** and **non-existent benign page** checks at Low, Medium and High security.
2. Compare HTTP status, response length, visible errors, and whether an allow-list or fixed navigation behaviour is apparent.
3. Avoid asserting a path traversal or local file inclusion vulnerability unless a controlled, authorised file outside the intended page list is actually included.

**Evidence to capture:** Six-row comparison table and selected HTTP evidence.

### Task 15 – Review path and file inclusion risk

Explain in a few sentences:

- How a URL parameter can influence file selection.
- Why unrestricted server-side path construction can be dangerous.
- Why an error message by itself is **not** proof of successful local file inclusion.
- How a fixed allow-list of permitted page identifiers reduces exposure.

**Evidence to capture:** Written interpretation, linked to Tasks 11–14.

---

## Part D – Server-side handling and impact assessment

### Task 16 – Map the file-handling data flow

Draw a small diagram showing:

```text
Browser / Burp
      |
      v
DVWA request handler
      |
      +---- Upload endpoint ----> validation ----> storage / rejection
      |
      +---- Inclusion endpoint -> page selection -> authorised content / rejection
```

Annotate where **untrusted input** enters, where validation should occur, and what the server should return on rejection.

### Task 17 – Review response headers and error handling

1. In Burp and Firefox, compare relevant upload/inclusion responses.
2. Note content type, redirects, caching behaviour (if relevant), and error messages.
3. Check whether file errors disclose absolute server paths or internal details.
4. Only report information exposure when the evidence actually displays it.

**Evidence to capture:** One normal response and one rejected/failed response.

### Task 18 – Assess demonstrated versus potential impact

Complete the following table using **your own observed results**:

| Test | Observed behaviour | Supported interpretation | Potential impact (not necessarily demonstrated) |
|---|---|---|---|
| Valid image upload | | | |
| Text-file upload | | | |
| MIME declaration change | | | |
| Filename handling | | | |
| Normal file inclusion | | | |
| Unknown benign page | | | |

Do not claim remote-code execution, arbitrary file read, or server compromise without direct, authorised validation.

### Task 19 – Identify secure design recommendations

Recommend controls appropriate to the observed weaknesses:

- **Uploads:** server-side type validation using actual file content where appropriate; extension allow-list; size limits; generated filenames; safe storage outside the executable web root; least-privilege file permissions; safe download/content-disposition handling.
- **File inclusion:** fixed allow-list mapping of identifiers to approved templates; canonical path checks where paths are unavoidable; avoid concatenating untrusted values into file paths; generic errors and internal logging.
- **General:** least privilege, secure configuration and retesting.

For every recommendation, link it to a specific observation or label it as a preventive best practice.

### Task 20 – Design a remediation verification test

1. Choose one observed weakness or validation gap.
2. State what a successful fix should change.
3. Define a **before/after** test using the same benign request and expected response.
4. If no remediation can be implemented in the lab, write **“Proposed retest only — no code change performed”**.
5. Do not describe changing DVWA's security level as proof that a code-level remediation was implemented.

---

## Part E – Reporting

### Task 21 – Create the Week 7 findings table

Create `~/lab-evidence/week7/09-findings-table.md` with at least these columns:

| Area | Endpoint / parameter | Baseline | Modified input | Observed result | Evidence file | Validated finding? | Remediation |
|---|---|---|---|---|---|---|---|
| File upload | | | | | | | |
| File inclusion | | | | | | | |

### Task 22 – Write a 400–500-word Findings Summary

Create `~/lab-evidence/week7/10-findings-summary.md`. Include:

1. The authorised target, DVWA address, and tested modules.
2. Baseline image upload and normal file inclusion behaviour.
3. File-type, content-type, filename, and size observations **actually performed**.
4. File inclusion and unknown-page observations, with HTTP evidence.
5. Browser Developer Tools and Burp Suite comparisons.
6. Observed impact, separated from potential risk.
7. Relevant recommendations for upload and path handling.
8. Whether a remediation retest was performed; if not, state that clearly.
9. Limitations and whether any **upload-validation or file-inclusion vulnerability was validated**.

Do not count table contents, figure captions, or evidence filenames toward the 400–500-word narrative.

### Task 23 – Validate the final report

Before submission, confirm:

- [ ] DVWA target IP/address and security settings are stated.
- [ ] Upload and inclusion modules are both covered.
- [ ] A normal baseline is documented for each module.
- [ ] At least one controlled comparison is recorded for each module.
- [ ] Browser DevTools and Burp evidence are labelled and legible.
- [ ] Sensitive cookies and credentials are redacted.
- [ ] Every security claim is tied to evidence or labelled as a potential risk.
- [ ] Remediation and retest status are explicit.
- [ ] Scope, limitations and cleanup/restoration are recorded.
- [ ] Findings Summary is 400–500 words.

### Task 24 – Cleanup and submit

1. Remove any benign test uploads **only where the DVWA lab instructions authorise cleanup**; do not delete unrelated files.
2. Restore the agreed lab security level and other modified settings.
3. Save the evidence and submit the findings table, summary and selected labelled screenshots.
4. Do not include live session IDs, credentials, or sensitive file contents.

---

## Suggested evidence filenames

| Filename | Purpose |
|---|---|
| `01-target-and-modules.png` | Authorised DVWA target and selected modules |
| `02-upload-form.png` | Upload form inspection |
| `03-image-upload-baseline.png` | Valid upload baseline |
| `04-text-upload-comparison.png` | Benign type comparison |
| `05-mime-request-comparison.png` | Burp MIME declaration comparison |
| `06-filename-and-size.png` | Filename and size outcomes |
| `07-upload-security-levels.png` | Low/Medium/High upload comparison |
| `08-inclusion-baseline.png` | Normal page selection |
| `09-inclusion-unknown-page.png` | Benign invalid page result |
| `10-inclusion-security-levels.png` | Low/Medium/High comparison |
| `09-findings-table.md` | Evidence-linked findings table |
| `10-findings-summary.md` | 400–500-word professional summary |

## Expected learning outcome

Students can demonstrate and document how DVWA handles uploaded files and page-selection inputs, distinguish server-side validation from browser controls, and produce evidence-based findings with proportionate remediation and honest limitations.
