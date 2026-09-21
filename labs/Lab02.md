# Week 2 Lab - HTTP Reconnaissance and Attack-Surface Analysis

## Estimated Time

Approximately **2 hours** for the core activities.

Allow additional time if you need to:

- install Kali tools;
- install Docker;
- download DVWA;
- download OWASP Juice Shop;
- download OWASP WebGoat.

Students using a prepared **Skillable** environment should use the pre-configured machines and should not change the supplied network configuration unless instructed.

---

## Learning Objectives

By the end of this lab, you will be able to:

- Confirm the authorised HTTP reconnaissance scope.
- Verify Kali-Attacker and Ubuntu-Server network configuration.
- Confirm that authorised web applications are running and reachable.
- Identify listening web-service ports and Docker containers.
- Use `curl` to examine HTTP requests, responses, methods, headers, and redirects.
- Compare `GET` and `HEAD` request behaviour.
- Compare remotely observed web-server information with Ubuntu-Server evidence.
- Correlate client-side HTTP requests with server-side logs or application evidence.
- Inspect authorised HTTP traffic using Burp Suite.
- Save command evidence for later reporting.
- Document limitations and produce an evidence-based findings summary.

---

## Scenario

You are continuing an authorised penetration-testing exercise.

You will use:

- **Kali-Attacker** as the HTTP reconnaissance workstation.
- **Ubuntu-Server** as the authorised target.

Ubuntu-Server hosts deliberately vulnerable training applications in Docker containers:

- **DVWA**
- **OWASP Juice Shop**
- **OWASP WebGoat**

Your task is to perform **HTTP reconnaissance and validation only**.

You will:

1. verify the environment;
2. confirm which web applications are running;
3. inspect HTTP requests and responses;
4. identify advertised HTTP methods;
5. compare `GET` and `HEAD`;
6. inspect response headers;
7. investigate redirects;
8. compare remote observations with Ubuntu-Server evidence;
9. inspect authorised HTTP traffic in Burp Suite;
10. save evidence and write a findings summary.

> [!alert]
> This lab is for **authorised reconnaissance**, not exploitation.
>
> Do not test university systems, public websites, your physical host, other students' systems, or any application that has not been explicitly authorised.

---

# Lab Environment

## Machines and Applications

| Component | Role |
|---|---|
| **Kali-Attacker** | Authorised reconnaissance workstation used for `curl`, Burp Suite, WhatWeb, Firefox/DevTools, and evidence collection. |
| **Ubuntu-Server** | Authorised target hosting the training web applications and providing server-side and container evidence. |
| **DVWA** | Authorised vulnerable training application, normally exposed on TCP port `80`. |
| **OWASP Juice Shop** | Authorised vulnerable training application, normally exposed on TCP port `3000`. |
| **OWASP WebGoat** | Authorised vulnerable training application, normally exposed on TCP port `8080`. |
| **Physical host** | Runs VMware or VirtualBox for local setups. It is **not** an authorised testing target. |
| **Skillable** | Provides the pre-configured isolated lab environment. |

For local VMware/VirtualBox testing, Kali-Attacker and Ubuntu-Server should be connected to the instructor-designated isolated or host-only network.

> [!note]
> Host-only networking instructions apply to local VMware/VirtualBox labs. Students using Skillable should keep the Skillable network configuration supplied by the instructor.

---

# Part A - Prepare Kali-Attacker

## Task 1 - Create the Week 2 Evidence Folder

On **Kali-Attacker**, open a terminal and run:

```bash
mkdir -p ~/lab-evidence/week2
```

Move into the folder:

```bash
cd ~/lab-evidence/week2
```

Confirm your location:

```bash
pwd
```

Expected result:

```text
/home/<your-user>/lab-evidence/week2
```

---

## Task 2 - Update the Kali Package List

If Kali-Attacker requires Internet access for package installation, use the instructor-approved configuration.

For a local VMware/VirtualBox setup, this may require temporarily using NAT.

Run:

```bash
sudo apt update
```

> [!alert]
> Skillable users should not change the supplied network configuration unless instructed.

---

## Task 3 - Verify Firefox and Developer Tools

Check Firefox:

```bash
firefox --version
```

If Firefox is not installed:

```bash
sudo apt install firefox-esr -y
```

Start Firefox:

```bash
firefox
```

Open Developer Tools using:

```text
F12
```

or:

```text
Firefox Menu > More tools > Web Developer Tools
```

Confirm that you can see tools such as:

- Inspector;
- Console;
- Network.

---

## Task 4 - Verify `curl`

Run:

```bash
curl --version
```

If the command is not found:

```bash
sudo apt install curl -y
```

Verify again:

```bash
curl --version
```

> [!note]
> `curl` will be used throughout this lab to generate and inspect HTTP requests and responses.

---

## Task 5 - Verify Burp Suite

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

## Task 6 - Verify WhatWeb

Run:

```bash
whatweb --version
```

If WhatWeb is not installed:

```bash
sudo apt install whatweb -y
```

Verify again:

```bash
whatweb --version
```

---

## Task 7 - Record Tool Availability

Complete the table using your actual results.

| Tool | Installed? | Verification/version |
|---|---|---|
| Firefox + DevTools | Yes / No | |
| `curl` | Yes / No | |
| Burp Suite | Yes / No | |
| WhatWeb | Yes / No | |

**Evidence to capture**

Take one screenshot showing that the required HTTP reconnaissance tools are available on Kali-Attacker.

---

# Part B - Prepare the Web Application Targets

## Task 8 - Create the Evidence Folder on Ubuntu-Server

On **Ubuntu-Server**, run:

```bash
mkdir -p ~/lab-evidence/week2
```

Then:

```bash
cd ~/lab-evidence/week2
```

Confirm:

```bash
pwd
```

---

## Task 9 - Install and Start Docker

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

## Task 10 - Start DVWA

Pull the DVWA image:

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

DVWA should normally be available from Kali using:

```text
http://<UBUNTU_IP>/
```

---

## Task 11 - Start OWASP Juice Shop

Pull the image:

```bash
sudo docker pull bkimminich/juice-shop
```

Run it:

```bash
sudo docker run -d --name juice-shop \
-p 3000:3000 \
bkimminich/juice-shop
```

Check:

```bash
sudo docker ps
```

Juice Shop should normally be available from Kali using:

```text
http://<UBUNTU_IP>:3000/
```

---

## Task 12 - Start OWASP WebGoat

Pull the image:

```bash
sudo docker pull webgoat/webgoat
```

Run it:

```bash
sudo docker run -d --name webgoat \
-p 8080:8080 \
webgoat/webgoat
```

Check:

```bash
sudo docker ps
```

WebGoat should normally be available from Kali using:

```text
http://<UBUNTU_IP>:8080/WebGoat
```

---

## Task 13 - Verify Listening Web Ports

On Ubuntu-Server:

```bash
sudo ss -tlnp
```

Look for the relevant application ports.

| Application | Expected Ubuntu port | Kali URL |
|---|---:|---|
| DVWA | 80 | `http://<UBUNTU_IP>/` |
| Juice Shop | 3000 | `http://<UBUNTU_IP>:3000/` |
| WebGoat | 8080 | `http://<UBUNTU_IP>:8080/WebGoat` |

Now confirm the containers:

```bash
sudo docker ps
```

---

## Task 14 - Verify Application Connectivity from Kali

On Kali-Attacker, use `curl` to check each authorised application.

DVWA:

```bash
curl -I http://<UBUNTU_IP>/
```

Juice Shop:

```bash
curl -I http://<UBUNTU_IP>:3000/
```

WebGoat:

```bash
curl -I http://<UBUNTU_IP>:8080/WebGoat
```

Record the results.

| Application | Port | Reachable? | HTTP status |
|---|---:|---|---|
| DVWA | 80 | Yes / No | |
| Juice Shop | 3000 | Yes / No | |
| WebGoat | 8080 | Yes / No | |

---

## Task 15 - Return to the Isolated Lab Network

If you temporarily changed a local VMware/VirtualBox machine to NAT to download packages or container images:

1. shut down the VM;
2. restore the instructor-designated host-only or isolated adapter;
3. start the VM again;
4. confirm its new IP address.

Skillable users should continue using the supplied Skillable network.

Record the following statement:

> I will perform HTTP reconnaissance only against instructor-authorised web applications. I will not perform exploitation unless explicitly instructed.

---

# Part C - Validate the Target Environment

## Task 16 - Check Kali-Attacker Network Configuration

On Kali-Attacker:

```bash
ip -br addr
```

Then:

```bash
ip route
```

Then check the route to Ubuntu-Server:

```bash
ip route get <UBUNTU_IP>
```

Record the active interface and IPv4 address.

---

## Task 17 - Check Ubuntu-Server Network Configuration

On Ubuntu-Server:

```bash
hostname
```

Then:

```bash
ip -br addr
```

Then:

```bash
ip route
```

Record the results.

| System | Interface | IPv4 address/prefix | Role |
|---|---|---|---|
| Kali-Attacker | | | Reconnaissance workstation |
| Ubuntu-Server | | | Authorised target |

---

## Task 18 - Confirm Listening TCP Services

On Ubuntu-Server:

```bash
sudo ss -tlnp
```

This displays listening TCP services and associated processes.

Possible output may include entries similar to:

```text
LISTEN 0 511 0.0.0.0:80   0.0.0.0:* users:(("apache2",pid=...))
LISTEN 0 128 0.0.0.0:3000 0.0.0.0:* users:(("node",pid=...))
```

Only record what you actually observe.

---

## Task 19 - Filter for Web Ports

Run:

```bash
sudo ss -tlnp | grep -E ':80|:443|:3000|:8080|:8081|:9091'
```

This reduces the output to ports relevant to likely web services.

Complete the table using your environment.

| Port | Observed? | Process or service |
|---:|---|---|
| 80 | Yes / No | |
| 443 | Yes / No | |
| 3000 | Yes / No | |
| 8080 | Yes / No | |
| 8081 | Yes / No | |
| 9091 | Yes / No | |

Do not record a service as present unless it is actually shown.

---

## Task 20 - Verify Docker Containers

Run:

```bash
sudo docker ps
```

Review:

- `CONTAINER ID`
- `IMAGE`
- `STATUS`
- `PORTS`
- `NAMES`

Complete:

| Application | Container/Image | Status | Published port |
|---|---|---|---|
| DVWA | | | |
| OWASP Juice Shop | | | |
| OWASP WebGoat | | | |

If an expected container is not shown, check all containers:

```bash
sudo docker ps -a
```

> [!note]
> If a container is absent or stopped, record that observation. Do not assume that it is running.

---

## Task 21 - Interpret the Listening Address

Review:

```bash
sudo ss -tlnp
```

and:

```bash
sudo docker ps
```

Interpret common listening addresses as follows:

```text
0.0.0.0:<PORT>
```

The service is listening on available IPv4 interfaces and may be reachable from Kali-Attacker.

```text
127.0.0.1:<PORT>
```

The service is listening only on Ubuntu-Server's loopback interface and normally cannot be accessed directly from Kali-Attacker.

```text
<UBUNTU_IP>:<PORT>
```

The service is bound specifically to that Ubuntu-Server interface.

Complete:

| Application | Listening address | Port | Process/container | Status |
|---|---|---:|---|---|
| DVWA | | | | |
| Juice Shop | | | | |
| WebGoat | | | | |

> [!note]
> A service listening on Ubuntu-Server is not automatically confirmed as reachable from Kali-Attacker. Network settings, firewall rules, interface binding, or application configuration may still affect access.

**Evidence to capture**

- Filtered `ss` output.
- Relevant Docker containers.
- Completed listener table.

---

# Part D - Establish an HTTP Baseline

## Task 22 - Display Response Headers Only

On Kali-Attacker:

```bash
curl -I http://<UBUNTU_IP>/
```

Observe the response headers.

---

## Task 23 - Send a Silent Request

Run:

```bash
curl -s http://<UBUNTU_IP>/
```

Observe how the output differs from a normal `curl` command.

---

## Task 24 - Display Headers and Response Content

Run:

```bash
curl -i http://<UBUNTU_IP>/
```

This displays both response headers and the response body.

---

## Task 25 - Display Verbose HTTP Communication

Run:

```bash
curl -v http://<UBUNTU_IP>/
```

Observe request information, response information, and connection details.

---

## Task 26 - Save the Baseline Response

Run:

```bash
curl -i http://<UBUNTU_IP>/ > http-baseline.txt
```

Confirm:

```bash
ls -lh http-baseline.txt
```

Record your observations.

| Item | Observed result |
|---|---|
| HTTP version | |
| Method | |
| Status code | |
| Content-Type | |
| Server | |

### Knowledge Check

1. What does `curl -I` display?
2. How does `curl -I` differ from a normal request?
3. What is the purpose of `curl -s`?
4. What additional information does `curl -i` display?
5. What extra details are visible with `curl -v`?

---

# Part E - HTTP Methods and Header Fingerprinting

## Task 27 - Identify Advertised HTTP Methods

Run:

```bash
curl -i -X OPTIONS http://<UBUNTU_IP>/
```

Look for an:

```text
Allow:
```

header.

Record only methods that are actually advertised.

| Method | Observed? | Normal purpose |
|---|---|---|
| GET | Yes / No | Retrieve a resource or data |
| HEAD | Yes / No | Retrieve response headers without the response body |
| POST | Yes / No | Submit data to a resource |
| OPTIONS | Yes / No | Request communication options or supported methods |
| PUT | Yes / No | Create or replace a resource |
| DELETE | Yes / No | Request removal of a resource |

> [!alert]
> Do not execute state-changing methods such as `PUT` or `DELETE` simply because the server advertises them.

### Knowledge Check

1. What is the purpose of `OPTIONS`?
2. Which HTTP methods were advertised?
3. What is the difference between `GET`, `POST`, `PUT`, and `DELETE`?
4. Why should `PUT` or `DELETE` not be tested only because they appear in an `Allow` header?
5. Does an advertised method automatically mean it can be successfully used?

---

## Task 28 - Compare GET and HEAD

Send a GET request:

```bash
curl -i http://<UBUNTU_IP>/
```

Observe:

- status code;
- response headers;
- response body;
- `Content-Type`;
- `Content-Length`, if present.

Now send a HEAD request:

```bash
curl -I http://<UBUNTU_IP>/
```

Observe:

- status code;
- response headers;
- `Content-Type`;
- `Content-Length`, if present;
- absence of the response body.

Complete:

| Feature | GET | HEAD |
|---|---|---|
| HTTP status code | | |
| Response headers returned | | |
| Response body returned | | |
| Content-Type | | |
| Content-Length | | |
| Server header | | |

---

## Task 29 - Build a Response-Header Inventory

Run:

```bash
curl -s -D - -o /dev/null http://<UBUNTU_IP>/
```

This displays response headers while discarding the body.

For each header:

- record the actual value if present;
- record `Not observed` if absent;
- do not guess.

Complete:

| Header | Observed value | Security/functional relevance |
|---|---|---|
| Server | | Identifies remotely reported server information |
| Content-Type | | Describes the media type of the response |
| Set-Cookie | | Indicates that the application is setting cookie data |
| Content-Security-Policy | | |
| X-Frame-Options | | |
| X-Content-Type-Options | | |
| Strict-Transport-Security | | |

> [!note]
> A missing security header should be recorded as an observation. It does not, by itself, prove that the application is vulnerable.

---

## Task 30 - Compare Headers Across All Three Applications

DVWA:

```bash
curl -s -D - -o /dev/null http://<UBUNTU_IP>/
```

Juice Shop:

```bash
curl -s -D - -o /dev/null http://<UBUNTU_IP>:3000/
```

WebGoat:

```bash
curl -s -D - -o /dev/null http://<UBUNTU_IP>:8080/WebGoat
```

Complete:

| Header | DVWA | Juice Shop | WebGoat |
|---|---|---|---|
| Server | | | |
| Content-Type | | | |
| Set-Cookie | | | |
| Content-Security-Policy | | | |
| X-Frame-Options | | | |
| X-Content-Type-Options | | | |
| Strict-Transport-Security | | | |

### Knowledge Check

1. What information can the `Server` header reveal?
2. Why can this information be useful during reconnaissance?
3. What is the purpose of `Content-Type`?
4. What is the purpose of `Set-Cookie`?

---

# Part F - Redirect Behaviour

## Task 31 - Inspect the Initial Response

Run:

```bash
curl -I http://<UBUNTU_IP>/
```

Check for:

- HTTP status code;
- `Location` header;
- redirect destination, if present.

Example only:

```text
HTTP/1.1 302 Found
Location: /login
```

This indicates that the server is directing the client to another location.

---

## Task 32 - Follow Redirects

Run:

```bash
curl -L -I http://<UBUNTU_IP>/
```

The `-L` option tells `curl` to follow redirects.

Compare the result with:

```bash
curl -I http://<UBUNTU_IP>/
```

Complete:

| Test | Initial status | Location header | Final status | Redirect followed? |
|---|---|---|---|---|
| `curl -I` | | | N/A | No |
| `curl -L -I` | | | | Yes |

If no redirect occurs, record:

```text
No redirect observed
```

### Knowledge Check

1. What does an HTTP redirect tell the client to do?
2. What is the purpose of the `Location` header?
3. What does `curl -L` do?
4. What is the difference between `curl -I` and `curl -L -I`?
5. Which HTTP status-code family is commonly associated with redirects?
6. Why is redirect behaviour useful during reconnaissance?

---

# Part G - Validate Server-Side Information

## Task 33 - Identify Web Services on Ubuntu-Server

On Ubuntu-Server:

```bash
sudo ss -tlnp
```

Identify the relevant web ports and associated processes.

---

## Task 34 - Inspect the Docker Applications

Run:

```bash
sudo docker ps
```

Where appropriate, inspect individual containers:

```bash
sudo docker inspect dvwa
```

```bash
sudo docker inspect juice-shop
```

```bash
sudo docker inspect webgoat
```

Record what Ubuntu-Server shows about the running application/container technology.

---

## Task 35 - Compare Remote and Local Evidence

On Kali-Attacker:

```bash
curl -I http://<UBUNTU_IP>/
```

Record any `Server` header.

Compare that remote observation with the evidence collected on Ubuntu-Server.

| Evidence source | Observed product/version |
|---|---|
| Kali HTTP response (`Server` header) | |
| Ubuntu/Docker-side evidence | |

### Knowledge Check

1. Why should remotely reported server information be compared with information obtained directly from Ubuntu-Server?
2. Does the `Server` response header always reveal the exact installed product and version?
3. Why might version information be reduced or hidden in HTTP responses?

---

# Part H - Correlate Client Requests with Server Logs

## Task 36 - Monitor an Appropriate Web-Server Log

On Ubuntu-Server, use only the command appropriate to the web server present in your environment.

For Apache:

```bash
sudo tail -f /var/log/apache2/access.log
```

For Nginx:

```bash
sudo tail -f /var/log/nginx/access.log
```

Leave the log running while you generate a request from Kali.

---

## Task 37 - Generate an HTTP Request from Kali

On Kali-Attacker:

```bash
curl http://<UBUNTU_IP>/
```

Return to Ubuntu-Server.

Look for a corresponding log entry.

Possible fields include:

- Kali's source IP;
- HTTP method;
- requested resource;
- status code;
- `User-Agent`, if recorded.

Press:

```text
Ctrl + C
```

to stop log monitoring.

Complete:

| Item | Observed value |
|---|---|
| Source IP | |
| HTTP method | |
| Requested resource | |
| Status code | |
| User-Agent | |

---

# Part I - Browser Developer Tools and Burp Suite

## Task 38 - Inspect a Request with Browser Developer Tools

On Kali-Attacker:

1. Open Firefox.
2. Press `F12`.
3. Open the **Network** panel.
4. Browse to one authorised lab application.
5. Select one request.
6. Review the request and response information.

Record relevant observations such as:

- method;
- URL/path;
- status;
- content type;
- request headers;
- response headers.

---

## Task 39 - Capture HTTP Traffic with Burp Suite

Start Burp Suite:

```bash
burpsuite
```

Open:

```text
Proxy > HTTP history
```

Using the lab-configured browser/proxy, open one authorised application.

Locate one request and inspect:

- HTTP method;
- path;
- HTTP version;
- `Host` header;
- request headers;
- cookies;
- parameters;
- request body, if present;
- response status;
- response headers.

> [!alert]
> Do not modify, replay, or manipulate requests unless your instructor explicitly authorises it.

Complete:

| Item | Observation |
|---|---|
| Method | |
| Path | |
| HTTP version | |
| Host | |
| Parameters | |
| Cookies | |
| Response status | |

**Evidence to capture**

Take a screenshot showing one authorised request and its response in Burp Suite.

> [!alert]
> Protect passwords, authentication cookies, session IDs, tokens, and other sensitive values in screenshots or submitted evidence.

---

## Task 40 - Compare the Evidence Sources

Complete:

| Source/Tool | What it reveals | Example from your lab |
|---|---|---|
| Browser DevTools | Browser-generated requests and application behaviour | |
| Burp Suite | Detailed HTTP requests and responses | |
| `curl` | Precisely controlled HTTP requests and raw responses | |
| Ubuntu access log | Server-side record of received requests | |

---

# Part J - Technology Fingerprinting and Evidence Collection

## Task 41 - Run WhatWeb on the Authorised Target

On Kali-Attacker:

```bash
whatweb http://<UBUNTU_IP>/
```

Record only what the tool actually reports.

Do not convert a technology fingerprint directly into a vulnerability claim.

---

## Task 42 - Save the Required Evidence Files

On Kali-Attacker:

```bash
cd ~/lab-evidence/week2
```

Save a baseline response:

```bash
curl -i http://<UBUNTU_IP>/ > baseline-response.txt
```

Save response headers:

```bash
curl -I http://<UBUNTU_IP>/ > response-headers.txt
```

Save the OPTIONS response:

```bash
curl -i -X OPTIONS http://<UBUNTU_IP>/ > options-response.txt
```

Save WhatWeb output:

```bash
whatweb http://<UBUNTU_IP>/ > technology-fingerprint.txt
```

List the saved files:

```bash
ls -lh
```

You should now have evidence files similar to:

```text
baseline-response.txt
response-headers.txt
options-response.txt
technology-fingerprint.txt
```

---

# Part K - Evidence, Interpretation, and Reporting

## Task 43 - Separate Evidence from Assumption

Use the following examples as a guide.

| Observed evidence | Supported interpretation | Unsupported assumption |
|---|---|---|
| `Server: Apache` observed | The HTTP response identifies the server as Apache | Apache is vulnerable |
| GET and HEAD are accepted | The server responds to these HTTP methods | The methods are insecure |
| `Set-Cookie` observed | The application uses cookies | The session can be hijacked |
| Security header not observed | The header was absent from the tested response | The application is exploitable |
| `302 Found` with `Location: /login` | The client is redirected to `/login` | Authentication can be bypassed |
| Docker container running | The application container is active | The application is vulnerable |

Write one statement based on your own results:

```text
Confirmed observation:
```

```text
Potential security relevance:
```

```text
Further verification required:
```

---

## Task 44 - Identify Assessment Limitations

Identify at least **two limitations** that directly apply to your lab results.

Possible limitations from this lab include:

- the activity focused on HTTP reconnaissance rather than exploitation;
- only instructor-authorised applications were tested;
- only observed HTTP resources and behaviour were assessed;
- authenticated or role-restricted functionality may not have been fully tested;
- hidden endpoints or functionality may exist;
- remotely reported server information may be incomplete or intentionally reduced;
- HTTP headers do not necessarily reveal installed patch status;
- browser, `curl`, and Burp-generated requests may produce different behaviour;
- Docker/container architecture may affect what is visible from the Ubuntu host;
- HTTPS-specific behaviour may not have been examined;
- results represent the application state at the time of testing.

Record the limitations that actually apply to your work.

---

## Task 45 - Write a 150–200 Word Findings Summary

Your summary should include:

- authorised application or applications assessed;
- Ubuntu-Server IP address;
- relevant application ports;
- HTTP methods observed;
- significant response headers;
- GET and HEAD behaviour;
- redirect behaviour, where observed;
- server/application technology identified;
- Ubuntu/Docker-side evidence;
- relevant Burp Suite observations;
- important limitations;
- whether any vulnerability was actually validated;
- names of supporting evidence files.

You may use this structure:

```text
HTTP reconnaissance was performed against [application] hosted on Ubuntu-Server
at [IP:port]. The application responded to [methods], with HTTP responses showing
[status codes/headers]. GET and HEAD testing showed [key difference or similarity],
while redirect testing identified [result].

Remote observations from Kali-Attacker were compared with [Docker/server-side
evidence] on Ubuntu-Server. Burp Suite confirmed [relevant request/response
behaviour]. These results identify observable HTTP behaviour but do not by
themselves establish an exploitable vulnerability.

The assessment was limited by [limitations]. No vulnerability was validated during
this reconnaissance [if applicable]. Supporting evidence is stored in [filenames].
```

> [!note]
> If no vulnerability was validated, state that explicitly.

---

# Final Validation

## Task 46 - Check Your Work

Before completing the lab, confirm that you have:

- [ ] Verified the required reconnaissance tools on Kali-Attacker.
- [ ] Confirmed the authorised web applications.
- [ ] Verified Kali-Attacker network configuration.
- [ ] Verified Ubuntu-Server network configuration.
- [ ] Confirmed that the required Docker containers are running.
- [ ] Identified relevant listening web ports.
- [ ] Verified application connectivity from Kali-Attacker.
- [ ] Collected baseline HTTP response evidence.
- [ ] Examined advertised HTTP methods using `OPTIONS`.
- [ ] Compared GET and HEAD behaviour.
- [ ] Examined relevant HTTP response headers.
- [ ] Compared response headers across authorised applications.
- [ ] Investigated redirect behaviour.
- [ ] Compared remote observations with Ubuntu/Docker evidence.
- [ ] Correlated a client request with server-side evidence where available.
- [ ] Captured an authorised request and response using Burp Suite.
- [ ] Saved command output under `~/lab-evidence/week2`.
- [ ] Protected passwords, session IDs, cookies, tokens, and other sensitive values.
- [ ] Completed the findings summary.
- [ ] Remained within the authorised testing scope.

---

# Troubleshooting

## Problem - An Application Is Not Reachable

On Ubuntu-Server, check the containers:

```bash
sudo docker ps
```

Then check listening ports:

```bash
sudo ss -tlnp
```

On Kali-Attacker, verify the route:

```bash
ip route get <UBUNTU_IP>
```

Confirm that you are using the correct Ubuntu-Server IP address and application port.

---

## Problem - A Container Is Missing

Check all containers:

```bash
sudo docker ps -a
```

Record its actual state rather than assuming that it is running.

---

## Problem - `curl` Is Not Installed

Run:

```bash
sudo apt update
sudo apt install curl -y
```

Then:

```bash
curl --version
```

---

## Problem - WhatWeb Is Not Installed

Run:

```bash
sudo apt install whatweb -y
```

Then:

```bash
whatweb --version
```

---

## Problem - Burp Suite Is Not Available

Check:

```bash
which burpsuite
```

If necessary:

```bash
sudo apt install burpsuite -y
```

---

## Problem - No `Allow` Header Is Returned

Record that the header was not observed.

Do not guess which methods are supported.

---

## Problem - A Security Header Is Missing

Record:

```text
Not observed
```

Do not report the absence alone as a confirmed vulnerability.

---

## Problem - No Redirect Is Observed

Record:

```text
No redirect observed
```

Do not assume that one should occur.

---

# Knowledge Check

Answer the following questions:

1. Why must the authorised scope be confirmed before HTTP reconnaissance?
2. What information does `sudo ss -tlnp` provide?
3. What information does `sudo docker ps` provide?
4. How does `docker ps` complement `ss -tlnp`?
5. What is the difference between HTTP `GET`, `HEAD`, `POST`, and `OPTIONS`?
6. How do `curl -I`, `curl -i`, `curl -s`, and `curl -v` differ?
7. What information can HTTP response headers reveal during reconnaissance?
8. Why does an advertised HTTP method not necessarily mean that it can be successfully used?
9. What is the main difference between GET and HEAD responses?
10. What does an HTTP `3xx` response indicate?
11. What is the purpose of the `Location` response header?
12. What does the `-L` option do in `curl`?
13. Why should remotely reported server information be compared with Ubuntu/Docker-side evidence?
14. What information can a web-server access log provide?
15. How do `curl`, Burp Suite, browser tools, and server-side evidence provide different perspectives on HTTP communication?
16. Why should authentication cookies, session IDs, or tokens not be shown in screenshots?
17. Why does an absent security header not automatically prove that an application is vulnerable?
18. Why should reconnaissance observations be separated from vulnerability conclusions?
19. What limitations should be considered before reporting findings from HTTP reconnaissance?

---

# Optional Challenge

Using **one instructor-authorised web application only**:

1. Confirm its IP address and port.
2. Verify that its Docker container is running.
3. Confirm that the relevant TCP port is listening.
4. Capture a normal HTTP GET request and response.
5. Identify the method, URI, HTTP version, status code, and key headers.
6. Reproduce the request using `curl`.
7. Compare GET and HEAD behaviour.
8. Identify advertised HTTP methods using `OPTIONS`.
9. Identify redirect behaviour, if present.
10. Compare remote server information with Ubuntu/Docker evidence.
11. Capture the request and response in Burp Suite.
12. Save the supporting evidence.
13. Write:

```text
Confirmed observation:
```

```text
Potential security relevance:
```

```text
Further verification required:
```

Each statement must be supported by evidence collected during the challenge.

---

## Summary

In this lab, you:

- prepared an authorised HTTP reconnaissance environment;
- verified Kali-Attacker tools;
- prepared or verified Docker-hosted training applications;
- confirmed network configuration and listening services;
- used `curl` to inspect HTTP requests and responses;
- examined HTTP methods;
- compared GET and HEAD behaviour;
- inspected response headers;
- investigated redirects;
- compared remote observations with Ubuntu/Docker-side evidence;
- correlated client and server-side evidence;
- inspected authorised traffic using Burp Suite;
- saved supporting evidence;
- identified relevant limitations;
- separated observed evidence from unsupported assumptions;
- produced an evidence-based findings summary.

This workflow provides a foundation for later vulnerability-assessment and controlled penetration-testing activities.
