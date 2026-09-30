**Tutorial / Stage: DVWA Fundamentals \| Target: DVWA \| Suggested tools: Browser and Burp Suite**

## Estimated Time

Approximately 2 hours for the core activities. Allow additional time for Burp Suite configuration, troubleshooting DVWA, resetting the DVWA database, or completing the advanced extension tasks.

## Learning Objectives

- Verify that the authorised DVWA target is running and reachable.

- Identify DVWA authentication-related requests and responses.

- Observe successful and unsuccessful login behaviour.

- Identify session cookies used by DVWA and distinguish credentials from session identifiers.

- Inspect authentication and session traffic using Browser Developer Tools and Burp Suite.

- Change DVWA security levels between Low, Medium and High and compare observable controls.

- Compare request parameters, cookies, redirects, response status, response length and application behaviour across security levels.

- Observe logout and session-termination behaviour.

- Protect passwords, session IDs, cookies and tokens in submitted evidence.

- Separate observed evidence from unsupported vulnerability conclusions.

- Produce an evidence-based comparison of Low, Medium and High controls.

## Scenario

You are continuing an authorised penetration-testing exercise. This week focuses on DVWA fundamentals: authentication behaviour, sessions and differences between Low, Medium and High security levels.

- Kali-Attacker is the testing workstation.

- Ubuntu-Server is the authorised target host.

- DVWA is the deliberately vulnerable training application.

- Firefox/Browser Developer Tools and Burp Suite are used to observe client-side HTTP behaviour.

| **Alert:** Do not test university systems, public websites, the physical host, other students' systems, or any application that has not been explicitly authorised. |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|

## Lab Environment

| **Component**       | **Role**                                                                                                     |
|---------------------|--------------------------------------------------------------------------------------------------------------|
| Kali-Attacker       | Testing workstation used for Firefox, Browser Developer Tools, Burp Suite and evidence collection.           |
| Ubuntu-Server       | Authorised target host providing Docker-hosted DVWA.                                                         |
| DVWA                | Authorised vulnerable training application, normally exposed on TCP port 80.                                 |
| VMware / VirtualBox | For local setups, connect Kali-Attacker and Ubuntu-Server using the instructor-designated Host-only network. |
| Skillable           | Use the supplied pre-configured lab network. No separate Host-only or isolated-network setup is required.    |

# Part A – Prepare the Week 3 Environment

## Task 1 – Create the Week 3 Evidence Folder

On Kali-Attacker, open a terminal and run:

```bash
mkdir -p ~/lab-evidence/week3
```

```bash
cd ~/lab-evidence/week3
```

```bash
pwd
```

Expected location:

`/home/<your-user>/lab-evidence/week3`

### Evidence to capture

Take one screenshot showing the Week 3 evidence folder.

## Task 2 - Install and Start Docker

If Docker is already installed and running in Skillable, continue to the next task.

On Ubuntu-Server:

```bash
sudo apt update
```

Install Docker:

```bash
sudo apt install docker.io -y
```

Enable and start Docker:

```bash
sudo systemctl enable --now docker
```

Verify the Docker version:

```bash
sudo docker --version
```

Check the service:

```bash
sudo systemctl status docker
```

Press:

```text
q
```

to exit the status view if required.

---

## Task 3 - Start DVWA

On Ubuntu-Server. Pull the DVWA image:

```bash
sudo docker pull vulnerables/web-dvwa
```

Start the container:

```bash
sudo docker run -d --name dvwa \
-p 80:80 \
vulnerables/web-dvwa
```

Check:

```bash
sudo docker ps
```
Look for the DVWA container and record the actual result.

Complete the table below:

| **Item**       | **Observed value** |
|----------------|--------------------|
| Container name |                    |
| Image          |                    |
| Status         |                    |
| Published port |                    |

If DVWA exists but is stopped:

```bash
sudo docker start dvwa
```

DVWA should normally be available from Kali using:

```text
http://<UBUNTU_IP>:80/
```

## Task 4 - Start Burp Suite

On Kali-Attacker.

Check whether Burp Suite is available:

```bash
which burpsuite
```

If installed, you should normally see a path similar to:

```text
/usr/bin/burpsuite
```

Start Burp Suite:

```bash
burpsuite
```

Confirm that the following area is available:

```text
Proxy > HTTP history
```

If Burp Suite is not installed:

```bash
sudo apt install burpsuite -y
```

---
## Task 5 – Verify DVWA Connectivity

On Kali-Attacker

```bash
curl -I http://<UBUNTU_IP>:80/
```

Complete the table below:

| **Item**                    | **Observed value** |
|-----------------------------|--------------------|
| Target IP                   |                    |
| HTTP status                 |                    |
| Location header, if present |                    |
| Server header, if present   |                    |
| Content-Type                |                    |

Then open DVWA in Firefox:

```bash
http://<UBUNTU_IP>:80/
```

# Part B – Establish the Authentication Baseline

## Task 6 – Identify the DVWA Login Page

On Kali-Attacker

1.  Open the DVWA login page in Firefox.

2.  Open Web Developer Tools and select the Network panel.

3.  Reload the page.

4.  Locate the request for login.php.

Complete the table below:

| **Item**           | **Observed value** |
|--------------------|--------------------|
| HTTP method        |                    |
| Requested path     |                    |
| Response status    |                    |
| Content-Type       |                    |
| Cookies observed   |                    |
| Redirect observed? |                    |

### Evidence to capture

Take a screenshot of the Network panel showing the login request.

## Task 7 – Examine the Login Form

On Kali-Attacker

Using Browser Developer Tools, inspect the login form. Look for the form action, HTTP method, username field, password field, and any hidden fields or tokens.

Complete the table below:

| **Form property**                | **Observed value** |
|----------------------------------|--------------------|
| Form action                      |                    |
| Form method                      |                    |
| Username parameter               |                    |
| Password parameter               |                    |
| Hidden parameter/token observed? |                    |

| **Note:** Do not include an actual password in submitted evidence. |
|--------------------------------------------------------------------|

# Part C – Observe Authentication Behaviour

## Task 8 – Capture an Unsuccessful Login

On Kali-Attacker

Enter an intentionally incorrect username/password combination. In Web Developer Tools → Network, locate the corresponding request.

Complete the table below:

| **Item**                | **Observed value** |
|-------------------------|--------------------|
| Request method          |                    |
| Request path            |                    |
| Response status         |                    |
| Redirect                |                    |
| Error/message displayed |                    |
| New cookie set?         | Yes / No           |

### Knowledge Check

What observable evidence tells you that authentication failed?

## Task 9 – Capture a Successful Login

On Kali-Attacker

Log in using the instructor-provided DVWA credentials. Do not include the password in screenshots or submitted evidence.

Username: 

```bash
admin
```

Password: 

```bash
password
```
Complete the table below:

| **Item**                           | **Observed value** |
|------------------------------------|--------------------|
| Request method                     |                    |
| Request path                       |                    |
| Response status                    |                    |
| Redirect destination               |                    |
| Authenticated page observed        |                    |
| Cookie/session identifier present? | Yes / No           |

### Knowledge Check

1.  What changed between the failed and successful login?

2.  Was the username/password sent on every later request?

3.  What appears to maintain the authenticated session?

# Part D – Examine Session Behaviour

## Task 10 – Identify DVWA Cookies

On Kali-Attacker

Open Developer Tools → Storage/Application → Cookies. Record cookie names only unless specifically instructed otherwise.

Complete the table below:

| **Cookie** | **Observed?** | **Likely functional purpose** |
|------------|---------------|-------------------------------|
| PHPSESSID  | Yes / No      | PHP session identifier        |
| security   | Yes / No      | DVWA security-level selection |
| Other      |               |                               |

| **Note:** Do not submit the actual value of PHPSESSID. |
|--------------------------------------------------------|

## Task 11 – Observe the Session Cookie in Burp Suite

On Kali-Attacker, start Burp Suite:

```bash
burpsuite
```

Open:

Proxy → HTTP history

Using the lab-configured browser, visit an authenticated DVWA page. Select one request and inspect:

HTTP method
Request path
Request headers
Cookie header
Response status
Set-Cookie header, if present

Complete the table below:

| **Item**             | **Observation** |
|----------------------|-----------------|
| Method               |                 |
| Path                 |                 |
| Cookie names         |                 |
| Response status      |                 |
| Set-Cookie observed? |                 |

### Evidence requirement

Take one screenshot showing the request/response in Burp, but redact session IDs and passwords.

## Task 12 – Observe Session Persistence

While you are logged in to DVWA:

1. Navigate to at least three different authenticated DVWA pages.

2. In Burp Suite → Proxy → HTTP history, locate the requests generated by those page visits.

3. Select each request and inspect the Cookie header.

4. Compare the requests and record whether:

  - the same session-cookie name is used;

  - the browser sends the session identifier automatically;

  - the username and password are sent again;

  - the authenticated session remains active as you move between pages.

Complete the table below:

| **Observation**                           | **Result** |
|-------------------------------------------|------------|
| Same session-cookie name across requests? |   Yes/No   |
| Session identifier sent automatically?    |   Yes/No   |
| Credentials resent on every request?      |   Yes/No   |
| Authentication persists across pages?     |   Yes/No   |

### Interpretation

Complete the statement: After successful login, DVWA appears to maintain authentication using \_\_\_\_\_\_\_\_\_\_ rather than resending the username and password with every request.

Answer: session cookie/session identifier

### Evidence requirement

Use Burp Suite evidence to support your answers. Do not include the actual session ID or password in screenshots or submitted work.

# Part E – Establish the DVWA Security Levels

## Task 13 – Locate DVWA Security Settings

Within DVWA:

1. Log in to DVWA.

2. From the left-hand menu, select DVWA Security.

3. Identify the security levels available in your environment.

4. Record only the levels that are actually shown.

Complete the table below:

| **Security level** | **Available?** |
|--------------------|----------------|
| Low                | Yes / No       |
| Medium             | Yes / No       |
| High               | Yes / No       |
| Impossible         | Yes / No       |

For this lab, you will test Low, Medium and High.

## Task 14 – Confirm the Security Cookie

1. In DVWA Security, set the security level to Low.

2. Open Browser Developer Tools or Burp Suite.

3. Navigate to another DVWA page so that a new request is generated.

4. Inspect the request's Cookie header.

5. Look for the DVWA security cookie, for example: security=low

6. Repeat the process after changing the DVWA security level to Medium and High

Set DVWA to Low. In Browser Developer Tools or Burp Suite, inspect a subsequent request and look for the security cookie. Repeat for Medium and High.

Complete the table below:

| **Selected level** | **Observed cookie value** |
|--------------------|---------------------------|
| Low                |                           |
| Medium             |                           |
| High               |                           |

### Knowledge Check

What mechanism does DVWA appear to use to remember the selected security level?

# Part F – Authentication Comparison Across Security Levels

## Task 15 – Observe Behaviour at Low Security

Set:

DVWA Security → Low

While authenticated:

1. Open the DVWA function selected for this lab.

2. Perform the same authorised test action you will repeat at Medium and High.

3. In Burp Suite → Proxy → HTTP history, locate the corresponding request.

4. Inspect the request and response.

5. Record only what is actually observed.

Complete the table below:

| **Low security observation**  | **Result** |
|-------------------------------|------------|
| HTTP method                   |            |
| Request path                  |            |
| Parameters observed           |            |
| Session cookie observed?      |  Yes / No  |
| Security cookie value         |            |
| Response status               |            |
| Error/message behaviour       |            |
| Redirect behaviour            |            |
| Additional control observed   |            |

## Task 16 – Observe Behaviour at Medium Security

Change:

DVWA Security → Medium

Repeat exactly the same test performed at Low security.

In Burp Suite, inspect the corresponding request and response.

Complete the table below:

| **Medium security observation** | **Result** |
|---------------------------------|------------|
| HTTP method                     |            |
| Request path                    |            |
| Parameters observed             |            |
| Session cookie observed?        |  Yes / No  |
| Security cookie value           |            |
| Response status                 |            |
| Error/message behaviour         |            |
| Redirect behaviour              |            |
| Additional control observed     |            |

| **Note:** Do not assume Medium is stronger in every observable area. Record only differences demonstrated by your evidence. |
|-----------------------------------------------------------------------------------------------------------------------------|

## Task 17 – Observe Behaviour at High Security

Change:

DVWA Security → High

Repeat the same test so you can compare the results fairly with Low and Medium.

Complete the table below:

| **High security observation**   | **Result** |
|---------------------------------|------------|
| HTTP method                     |            |
| Request path                    |            |
| Parameters observed             |            |
| Session cookie observed?        |  Yes / No  |
| Security cookie value           |            |
| Response status                 |            |
| Error/message behaviour         |            |
| Redirect behaviour              |            |
| Additional control observed     |            |

# Part G – Compare Low, Medium and High Controls

## Task 18 – Build a Security-Level Comparison Table

Using the evidence collected from the same DVWA function tested at Low, Medium and High security, complete the table below.

Record only what you actually observed in Browser Developer Tools or Burp Suite.

| **Control / behaviour**       | **Low** | **Medium** | **High** |
|-------------------------------|---------|------------|----------|
| HTTP method                   |         |            |          |
| Session cookie present        |         |            |          |
| Security cookie value         |         |            |          |
| Hidden token observed         |         |            |          |
| Error response behaviour      |         |            |          |
| Redirect behaviour            |         |            |          |
| Request parameters            |         |            |          |
| Additional validation/control |         |            |          |
| Noticeable delay/throttling   |         |            |          |
| Other observed difference     |         |            |          |

| **Note:** Use Not observed when a control or difference is not present in your evidence. Do not assume that a control exists, or that it is stronger, simply because the selected level is called High. |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

### Knowledge Check

Which behaviours remained the same across all three levels?

Which controls changed as the security level increased?

Which differences were directly visible in the request or response?

Which conclusions would require further testing before you could confirm them?

# Part H – Compare Requests in Burp Suite

## Task 19 – Capture One Request from Each Security Level

Using Burp HTTP history, identify one comparable request for Low, Medium and High.

| **Item**        | **Low** | **Medium** | **High** |
|-----------------|---------|------------|----------|
| Method          |         |            |          |
| Path            |         |            |          |
| Parameters      |         |            |          |
| Cookie names    |         |            |          |
| Security cookie |         |            |          |
| Response status |         |            |          |
| Response length |         |            |          |
| Redirect        |         |            |          |

### Evidence to capture

Take screenshots of the three requests. Protect passwords, PHPSESSID, authentication tokens and other sensitive values.

# Part I – Session Termination

## Task 20 – Observe Logout Behaviour

11. While authenticated, identify your current session cookie.

12. Select Logout.

13. Observe the request and response in Burp.

14. Attempt to return to an authenticated DVWA page.

| **Item**                                                | **Observed value** |
|---------------------------------------------------------|--------------------|
| Logout method                                           |                    |
| Logout path                                             |                    |
| Response status                                         |                    |
| Redirect destination                                    |                    |
| Session cookie changed/removed?                         |                    |
| Authenticated page still accessible without logging in? |                    |

### Knowledge Check

15. What evidence shows that the authenticated session ended?

16. Did the browser still retain any DVWA-related cookies?

17. Is retaining a cookie equivalent to retaining an authenticated session?

# Part J – Session vs Authentication

## Task 21 – Distinguish Credentials from Sessions

| **Item**                         | **Authentication credential or session data?** | **Purpose** |
|----------------------------------|------------------------------------------------|-------------|
| Username                         |                                                |             |
| Password                         |                                                |             |
| PHPSESSID                        |                                                |             |
| security cookie                  |                                                |             |
| Login POST request               |                                                |             |
| Subsequent authenticated request |                                                |             |

### Knowledge Check

What is the difference between authenticating a user and maintaining an authenticated session?

# Part K – Evidence Interpretation

## Task 22 – Separate Evidence from Assumption

| **Observed evidence**                   | **Supported interpretation**                | **Unsupported assumption**         |
|-----------------------------------------|---------------------------------------------|------------------------------------|
| PHPSESSID observed                      | Application uses a PHP session identifier   | Session can be hijacked            |
| security=low observed                   | DVWA is configured at Low level             | Every control is vulnerable        |
| Login POST observed                     | Credentials are submitted using POST        | Credentials are securely protected |
| Different High-level behaviour observed | Control behaviour differs by security level | High is completely secure          |
| Logout redirects to login page          | Application performs logout/redirection     | Session invalidation is perfect    |
| Your example 1                          |                                             |                                    |
| Your example 2                          |                                             |                                    |

# Part L – Findings Table

## Task 23 – Summarise the Security-Level Differences

| **Area**                 | **Low** | **Medium** | **High** | **Evidence source** |
|--------------------------|---------|------------|----------|---------------------|
| Authentication behaviour |         |            |          | Browser/Burp        |
| Session behaviour        |         |            |          | Browser/Burp        |
| Security cookie          |         |            |          | Browser/Burp        |
| Request parameters       |         |            |          | Burp                |
| Error handling           |         |            |          | Browser/Burp        |
| Additional controls      |         |            |          | Browser/Burp        |

Then answer: Which controls became more restrictive as the security level increased? Record only controls that your testing actually demonstrated.

# Part M – Evidence Collection

## Task 24 – Save Required Evidence

18. Screenshot showing DVWA running.

19. Screenshot of unsuccessful login behaviour.

20. Screenshot of successful login behaviour.

21. Cookie-name evidence.

22. Burp request at Low.

23. Burp request at Medium.

24. Burp request at High.

25. Completed comparison table.

26. Logout/session-termination evidence.

Recommended evidence names:

```text
01-dvwa-running.png
02-login-failed.png
03-login-success.png
04-session-cookies.png
05-low-burp.png
06-medium-burp.png
07-high-burp.png
08-security-comparison.txt
09-logout-session.png
```

# Part N – Findings Summary

## Task 25 – Write a 400–500 Word Findings Summary

Your summary should include:

- authorised target tested;

- DVWA address;

- authentication behaviour observed;

- successful vs unsuccessful authentication;

- session mechanism observed;

- relevant cookie names;

- differences observed between Low, Medium and High;

- evidence from Browser Developer Tools;

- evidence from Burp Suite;

- logout/session termination behaviour;

- important limitations;

- whether any vulnerability was actually validated.

### Suggested structure

DVWA authentication and session behaviour was examined on the authorised Ubuntu-Server target using Firefox Developer Tools and Burp Suite. Authentication testing showed \[observed behaviour\]. Following successful authentication, the application used \[observed session mechanism\].

At Low security, \[observations\]. At Medium security, \[observations\]. At High security, \[observations\]. The comparison identified \[specific observable differences\].

Burp Suite confirmed \[request/cookie/response observations\]. Session testing showed \[logout/persistence observations\].

The assessment was limited to controlled authentication and session observations within DVWA. Findings therefore describe observed control behaviour and should not be interpreted as evidence of additional vulnerabilities unless separately validated.

## Task 26 – Check Your Work

☐ Verified DVWA is running.

☐ Confirmed Kali-to-DVWA connectivity.

☐ Inspected the login page.

☐ Observed an unsuccessful login.

☐ Observed a successful login.

☐ Identified session cookie names.

☐ Identified the DVWA security cookie.

☐ Inspected authentication traffic in Burp Suite.

☐ Tested Low security.

☐ Tested Medium security.

☐ Tested High security.

☐ Compared the three security levels.

☐ Examined logout/session termination.

☐ Protected passwords and session identifiers in evidence.

☐ Separated observations from assumptions.

☐ Completed the findings summary.

☐ Remained within the authorised DVWA lab scope.

# Knowledge Check

45. What is the difference between authentication and session management?

46. Why does a web application use a session identifier after successful authentication?

47. What does PHPSESSID represent in DVWA?

48. What does the DVWA security cookie represent?

49. Why should actual session identifiers be removed from screenshots?

50. How can Burp Suite help analyse authentication behaviour?

51. What evidence distinguishes a successful login from an unsuccessful login?

52. Why should Low, Medium and High be compared using the same request or workflow?

53. Does a security level named High prove that the application is secure?

54. Why should observed controls be separated from vulnerability conclusions?

55. What happens to the authenticated session after logout in your environment?

56. Why might Browser Developer Tools and Burp Suite show complementary evidence?

57. What evidence shows that a session cookie is required for an authenticated request?

58. Why is testing an invalid session ID different from testing another user’s session ID?

59. What does a change in session identifier after login potentially indicate?

60. What information can an Apache access log provide that complements Burp evidence?

61. Why should response timing tests use only a small controlled number of attempts?

62. What is the purpose of separating observed evidence, supported interpretation, security relevance and further verification required?

# Summary

In this lab, you verified DVWA, observed authentication behaviour, identified session cookies, analysed login and logout traffic with Browser Developer Tools and Burp Suite, compared Low/Medium/High security levels, and documented evidence without exposing sensitive values. The advanced tasks extend this workflow into session dependence, concurrent sessions, cookie attributes, logout invalidation, security-cookie behaviour, session-ID lifecycle, client/server evidence correlation and controlled timing comparison.

| **Note:** The objective is evidence-based analysis of the authorised DVWA training environment. A difference in behaviour or a missing control should not be converted directly into a vulnerability claim without appropriate validation. |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
