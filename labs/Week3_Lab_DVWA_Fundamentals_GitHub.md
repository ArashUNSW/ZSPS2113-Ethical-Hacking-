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
cd ~/lab-evidence/week3
pwd
```

Expected location:

`/home/<your-user>/lab-evidence/week3`

### Evidence to capture

Take one screenshot showing the Week 3 evidence folder.

## Task 2 – Verify the DVWA Target

On Ubuntu-Server:

```bash
sudo docker ps
```

Look for the DVWA container and record the actual result.

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

If required, confirm the port:

```bash
sudo ss -tlnp | grep ':80'
```

## Task 3 – Verify DVWA Connectivity from Kali

```bash
curl -I http://<UBUNTU_IP>/
```

| **Item**                    | **Observed value** |
|-----------------------------|--------------------|
| Target IP                   |                    |
| HTTP status                 |                    |
| Location header, if present |                    |
| Server header, if present   |                    |
| Content-Type                |                    |

Then open DVWA in Firefox:

`http://<UBUNTU_IP>/`

# Part B – Establish the Authentication Baseline

## Task 4 – Identify the DVWA Login Page

1.  Open the DVWA login page in Firefox.

2.  Open Developer Tools and select the Network panel.

3.  Reload the page.

4.  Locate the request for login.php.

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

## Task 5 – Examine the Login Form

Using Browser Developer Tools, inspect the login form. Look for the form action, HTTP method, username field, password field, and hidden fields or tokens if present.

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

## Task 6 – Capture an Unsuccessful Login

Enter an intentionally incorrect training username/password combination. In Developer Tools → Network, locate the corresponding request.

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

## Task 7 – Capture a Successful Login

Log in using the instructor-provided DVWA credentials. Do not include the password in screenshots or submitted evidence.

| **Item**                           | **Observed value** |
|------------------------------------|--------------------|
| Request method                     |                    |
| Request path                       |                    |
| Response status                    |                    |
| Redirect destination               |                    |
| Authenticated page observed        |                    |
| Cookie/session identifier present? | Yes / No           |

### Knowledge Check

5.  What changed between the failed and successful login?

6.  Was the username/password sent on every later request?

7.  What appears to maintain the authenticated session?

# Part D – Examine Session Behaviour

## Task 8 – Identify DVWA Cookies

Open Developer Tools → Storage/Application → Cookies. Record cookie names only unless specifically instructed otherwise.

| **Cookie** | **Observed?** | **Likely functional purpose** |
|------------|---------------|-------------------------------|
| PHPSESSID  | Yes / No      | PHP session identifier        |
| security   | Yes / No      | DVWA security-level selection |
| Other      |               |                               |

| **Note:** Do not submit the actual value of PHPSESSID. |
|--------------------------------------------------------|

## Task 9 – Observe the Session Cookie in Burp Suite

```bash
burpsuite
```

Open Proxy → HTTP history. Using the lab-configured browser, visit an authenticated DVWA page. Select one request and inspect the method, path, request headers, Cookie header, response status and Set-Cookie header if present.

| **Item**             | **Observation** |
|----------------------|-----------------|
| Method               |                 |
| Path                 |                 |
| Cookie names         |                 |
| Response status      |                 |
| Set-Cookie observed? |                 |

### Evidence requirement

Take one screenshot showing the request/response in Burp, but redact session IDs and passwords.

## Task 10 – Observe Session Persistence

8.  While authenticated, navigate to several DVWA pages.

9.  Observe the requests in Burp.

10. Compare their Cookie headers.

| **Observation**                           | **Result** |
|-------------------------------------------|------------|
| Same session-cookie name across requests? |            |
| Session identifier sent automatically?    |            |
| Credentials resent on every request?      |            |
| Authentication persists across pages?     |            |

### Interpretation

Complete: After successful login, DVWA appears to maintain authentication using \_\_\_\_\_\_\_\_\_\_ rather than resending the username and password with every request.

# Part E – Establish the DVWA Security Levels

## Task 11 – Locate DVWA Security Settings

Within DVWA, open DVWA Security and identify the levels available in your environment.

| **Security level** | **Available?** |
|--------------------|----------------|
| Low                | Yes / No       |
| Medium             | Yes / No       |
| High               | Yes / No       |
| Impossible         | Yes / No       |

For this lab, test Low, Medium and High.

## Task 12 – Confirm the Security Cookie

Set DVWA to Low. In Browser Developer Tools or Burp Suite, inspect a subsequent request and look for the security cookie. Repeat for Medium and High.

| **Selected level** | **Observed cookie value** |
|--------------------|---------------------------|
| Low                |                           |
| Medium             |                           |
| High               |                           |

### Knowledge Check

What mechanism does DVWA appear to use to remember the selected security level?

# Part F – Authentication Comparison Across Security Levels

## Task 13 – Test Authentication at Low Security

Set DVWA Security → Low. Perform one unsuccessful authentication observation and one successful login/session observation where appropriate. Inspect the requests using Burp Suite.

| **Low security observation**  | **Result** |
|-------------------------------|------------|
| Authentication request method |            |
| Parameters observed           |            |
| Session cookie observed       |            |
| Security cookie               |            |
| Error behaviour               |            |
| Redirect behaviour            |            |
| Additional control observed   |            |

## Task 14 – Test Authentication at Medium Security

Set DVWA Security → Medium. Perform one unsuccessful authentication observation and one successful login/session observation where appropriate. Inspect the requests using Burp Suite.

| **Medium security observation** | **Result** |
|---------------------------------|------------|
| Authentication request method   |            |
| Parameters observed             |            |
| Session cookie observed         |            |
| Security cookie                 |            |
| Error behaviour                 |            |
| Redirect behaviour              |            |
| Additional control observed     |            |

| **Note:** Do not assume Medium is stronger in every observable area. Record only what actually changes. |
|---------------------------------------------------------------------------------------------------------|

## Task 15 – Test Authentication at High Security

Set DVWA Security → High. Perform one unsuccessful authentication observation and one successful login/session observation where appropriate. Inspect the requests using Burp Suite.

| **High security observation** | **Result** |
|-------------------------------|------------|
| Authentication request method |            |
| Parameters observed           |            |
| Session cookie observed       |            |
| Security cookie               |            |
| Error behaviour               |            |
| Redirect behaviour            |            |
| Additional control observed   |            |

# Part G – Compare Low, Medium and High Controls

## Task 16 – Build a Security-Level Comparison Table

| **Control / behaviour**       | **Low** | **Medium** | **High** |
|-------------------------------|---------|------------|----------|
| Authentication method         |         |            |          |
| Session cookie present        |         |            |          |
| Security cookie value         |         |            |          |
| Hidden token observed         |         |            |          |
| Error response behaviour      |         |            |          |
| Redirect behaviour            |         |            |          |
| Request parameters            |         |            |          |
| Additional validation/control |         |            |          |
| Noticeable delay/throttling   |         |            |          |
| Other observed difference     |         |            |          |

| **Note:** Use “Not observed” when a control is absent from your evidence. Do not infer that a control exists simply because the selected level is called High. |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------|

# Part H – Compare Requests in Burp Suite

## Task 17 – Capture One Request from Each Security Level

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

## Task 18 – Observe Logout Behaviour

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

## Task 19 – Distinguish Credentials from Sessions

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

## Task 20 – Separate Evidence from Assumption

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

## Task 21 – Summarise the Security-Level Differences

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

## Task 22 – Save Required Evidence

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

## Task 23 – Write a 300–400 Word Findings Summary

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

## Task 24 – Check Your Work

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

# Part O – Advanced DVWA Authentication and Session Analysis

| **Note:** These tasks are extensions for students who complete the core lab. They are still restricted to the instructor-authorised DVWA environment. |
|-------------------------------------------------------------------------------------------------------------------------------------------------------|

## Task 25 – Compare Authenticated and Unauthenticated Requests

27. Choose one DVWA page that requires authentication.

28. While logged in, capture the request in Burp Suite → Proxy → HTTP history.

29. Send the request to Repeater and record the original response.

30. Remove the PHPSESSID cookie from the copied request.

31. Send the request again and compare the responses.

| **Item**                 | **Authenticated request** | **Without session cookie** |
|--------------------------|---------------------------|----------------------------|
| HTTP status              |                           |                            |
| Response length          |                           |                            |
| Redirect observed        |                           |                            |
| Page/content returned    |                           |                            |
| Authentication required? |                           |                            |

### Knowledge Check

What evidence indicates that the application relies on the session cookie to identify an authenticated user?

## Task 26 – Test Session Isolation Between Two Browsers

Open DVWA in a normal Firefox window and a Firefox Private Browsing window. Log in separately in both windows. Record cookie names and whether the values differ. Do not include complete session values in submitted evidence.

| **Observation**              | **Normal window** | **Private window** |
|------------------------------|-------------------|--------------------|
| PHPSESSID present            |                   |                    |
| Session IDs identical?       |                   |                    |
| security cookie present      |                   |                    |
| Logged-in state independent? |                   |                    |

Log out from only one window and observe whether the second session remains authenticated.

### Question

Does logging out of one browser session terminate the other session? Record only what your environment demonstrates.

## Task 27 – Examine Session Invalidation After Logout

32. Log in to DVWA.

33. Capture an authenticated request in Burp.

34. Send it to Repeater.

35. Log out using the browser.

36. Return to the previously captured request in Repeater.

37. Send it again without changing it.

| **Item**                       | **Before logout** | **After logout** |
|--------------------------------|-------------------|------------------|
| Status code                    |                   |                  |
| Redirect                       |                   |                  |
| Authenticated content returned |                   |                  |
| Session accepted?              |                   |                  |

### Interpretation

Choose only the statement supported by your result: old session remained accepted; old session was rejected; result was inconclusive.

## Task 28 – Compare Cookie Attributes

Using Burp or Browser Developer Tools, inspect any Set-Cookie headers returned by DVWA. Look specifically for HttpOnly, Secure, SameSite, Path and expiry/max-age information.

| **Cookie attribute** | **PHPSESSID**           | **security**            |
|----------------------|-------------------------|-------------------------|
| Path                 |                         |                         |
| HttpOnly             | Observed / Not observed | Observed / Not observed |
| Secure               | Observed / Not observed | Observed / Not observed |
| SameSite             | Observed / Not observed | Observed / Not observed |
| Expires / Max-Age    |                         |                         |

| **Note:** For each missing attribute, record “Not observed”. Do not write that its absence automatically proves exploitation is possible. |
|-------------------------------------------------------------------------------------------------------------------------------------------|

## Task 29 – Compare the Same Request Across Low, Medium and High

Choose one identical DVWA function. Capture the corresponding request at Low, Medium and High, and send each request to Burp Repeater.

| **Feature**     | **Low** | **Medium** | **High** |
|-----------------|---------|------------|----------|
| HTTP method     |         |            |          |
| Path            |         |            |          |
| Parameters      |         |            |          |
| Cookie names    |         |            |          |
| Token present   |         |            |          |
| Response status |         |            |          |
| Response length |         |            |          |
| Error/message   |         |            |          |
| Delay observed  |         |            |          |

### Challenge

Identify the specific technical control that changed rather than simply writing “High is more secure”. Use only statements demonstrated by your evidence.

## Task 30 – Controlled Security-Cookie Modification

Capture an authorised DVWA request containing security=low and send it to Burp Repeater. Change only the security cookie to medium and then high, sending the request after each change.

| **Cookie value sent** | **Response status** | **Application behaviour** | **Level reflected by application** |
|-----------------------|---------------------|---------------------------|------------------------------------|
| low                   |                     |                           |                                    |
| medium                |                     |                           |                                    |
| high                  |                     |                           |                                    |

### Question

Does the application appear to determine the selected security level from the client-supplied cookie? State this only if your evidence supports it.

## Task 31 – Compare Valid and Invalid Session IDs

Using a request copied to Burp Repeater, send the original authenticated request and record the response. Then replace the session ID with a clearly invalid test value and send the request again.

`PHPSESSID=invalid-session-test`

| **Note:** Do not test other users’ session identifiers. |
|---------------------------------------------------------|

| **Test**              | **Status** | **Redirect** | **Authenticated content?** |
|-----------------------|------------|--------------|----------------------------|
| Original session      |            |              |                            |
| Invalid session value |            |              |                            |

### Knowledge Check

How does DVWA respond when it receives an unknown or invalid session identifier?

## Task 32 – Examine Whether the Session ID Changes After Login

38. Clear DVWA cookies.

39. Visit the login page without authenticating.

40. Record whether a PHPSESSID exists.

41. Label the pre-login session as Session A rather than submitting its full value.

42. Log in successfully.

43. Inspect the session cookie again and label it Session B.

44. Observe the session again after logout.

| **Stage**    | **Session identifier observed?** | **Changed?** |
|--------------|----------------------------------|--------------|
| Before login |                                  |              |
| After login  |                                  |              |
| After logout |                                  |              |

### Question

Did the application issue a new session identifier after authentication?

## Task 33 – Correlate Burp Evidence with Server Logs

On Ubuntu-Server, inspect the DVWA Apache log from inside the container:

```bash
sudo docker exec -it dvwa bash
tail -f /var/log/apache2/access.log
```

Generate one request from Kali through Burp and match the client-side request with the server-side log entry.

| **Field**  | **Burp observation** | **Server log observation** |
|------------|----------------------|----------------------------|
| Source IP  |                      |                            |
| Method     |                      |                            |
| Resource   |                      |                            |
| Status     |                      |                            |
| User-Agent |                      |                            |
| Timestamp  |                      |                            |

### Question

Which information is visible in Burp but not necessarily in the Apache access log?

## Task 34 – Compare Response Timing for Failed Authentication

Perform only a small controlled number of attempts, for example three per level. Record the approximate response time for an intentionally incorrect authentication attempt at Low, Medium and High.

| **Security level** | **Attempt 1** | **Attempt 2** | **Attempt 3** | **Observable delay/control?** |
|--------------------|---------------|---------------|---------------|-------------------------------|
| Low                |               |               |               |                               |
| Medium             |               |               |               |                               |
| High               |               |               |               |                               |

| **Note:** Do not perform password spraying, dictionary attacks or high-volume guessing. |
|-----------------------------------------------------------------------------------------|

### Question

Does any security level introduce an observable delay or other response change after failed authentication?

## Task 35 – Advanced Evidence-Based Finding

Produce one structured finding using the following four sections:

### Observed evidence

Exactly what was seen. Example: After logout, replaying the previously captured request produced a redirect to the login page.

### Supported interpretation

What the evidence reasonably indicates. Example: The previously captured session was no longer accepted for that request.

### Security relevance

Why it matters. Example: Session invalidation helps prevent continued use of an authenticated session after logout.

### Further verification required

What has not been demonstrated. Example: Testing did not determine how the application handles concurrent sessions or expired sessions.

# Troubleshooting

## Problem – DVWA Is Not Reachable

On Ubuntu-Server, check the container and listening port:

```bash
sudo docker ps
sudo ss -tlnp | grep ':80'
```

On Kali-Attacker, confirm the correct target IP and test connectivity:

```bash
ip -br addr
ip route
curl -I http://<UBUNTU_IP>/
```

## Problem – DVWA Container Is Missing or Stopped

```bash
sudo docker ps -a
```

If the container exists but is stopped:

```bash
sudo docker start dvwa
```

| **Note:** Record the actual container state rather than assuming it is running. |
|---------------------------------------------------------------------------------|

## Problem – Burp Suite Is Not Available

```bash
which burpsuite
```

If installation is required and Internet access is instructor-approved:

```bash
sudo apt update
sudo apt install burpsuite -y
```

## Problem – Burp Does Not Capture Browser Traffic

- Confirm the browser proxy points to the Burp listener.
- Confirm Proxy → Intercept and HTTP history are available.
- Confirm the Burp listener is running on the expected local address/port.
- Use the lab-configured browser profile if supplied by the instructor.

## Problem – Login or Security-Level Behaviour Differs from the Lab Sheet

Record the behaviour actually observed in your environment. Do not force the result to match an example. DVWA version, browser state, container state, cookies and database state may affect behaviour.

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
