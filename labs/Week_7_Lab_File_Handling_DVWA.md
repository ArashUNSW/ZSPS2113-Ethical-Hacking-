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
