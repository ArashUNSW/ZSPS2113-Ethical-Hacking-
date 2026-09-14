# Week 1 Lab - Building a Virtual Penetration-Testing Environment and Discovering Network Services

## Estimated Time

Approximately **2–3 hours**. Students using a prepared **Skillable** environment may complete the lab in approximately **1.5–2 hours**. Allow extra time for downloads, updates, VM installation, and snapshots.

## Learning Objectives

By the end of this lab, you will be able to:

- Set up or access **Kali-Attacker** and **Ubuntu-Server**.
- Verify that both systems are connected to the authorised lab network.
- Define the permitted testing scope before reconnaissance.
- Inspect IP addresses, interfaces, and routes.
- Test connectivity between the attacker and target.
- Discover responsive hosts within an authorised subnet.
- Confirm which discovered address belongs to Ubuntu-Server.
- Use Nmap to identify open TCP ports.
- Use Nmap service detection to identify services, products, and reported versions.
- Save evidence from scans.
- Distinguish an exposed service from a confirmed vulnerability.
- Produce an evidence-based findings summary.

---

## Scenario

You are beginning an authorised penetration-testing exercise. Your environment contains two systems:

- **Kali-Attacker** — the penetration-testing workstation.
- **Ubuntu-Server** — the authorised target.

Before testing, you must verify the network, confirm the authorised scope, identify the correct target, and collect evidence. You will then use Linux networking commands and **Nmap** to discover hosts, identify open TCP ports, detect services, and document your findings.

> [!alert]
> Perform all scanning **only inside the authorised lab environment**. A responding device is not automatically an approved target.

---

# Lab Environment

## Machines and Roles

| Machine | Role |
|---|---|
| **Kali-Attacker** | Penetration-testing workstation used for connectivity checks, host discovery, TCP scanning, service detection, and evidence collection. |
| **Ubuntu-Server** | Authorised target used for discovery, port scanning, and service identification. Its console is used to confirm hostname and IP address. |
| **Physical host computer** | Runs VMware or VirtualBox for local setups. It is **not** an authorised target for scanning. |

For local testing, connect both VMs to the same authorised **host-only** network.

Example lab subnet:

```text
192.168.1.0/24 
```

> [!note]
> Use the subnet specified by your instructor if it is different.

---

# Part A - Choose Your Lab Environment

## Task 1 - Select Skillable, VirtualBox, or VMware

You may complete the lab using one of the following options.

### Option 1 - Skillable

From Moodle, open:

```text
Skillable Labs - ZSPS2113 Ethical Hacking and Penetration Testing
```

Select the current week's lab.

### Skillable Credentials

**Kali-Attacker**

```text
Username: student
Password: toor
```

**Ubuntu-Server**

```text
Username: root
Password: Pa$$w0rd
```

> [!alert]
> Do not include passwords in screenshots or submitted evidence.

If you use Skillable, continue to **Task 4 - Create an Evidence Folder**.

### Option 2 - VirtualBox

1. Download the installer for your operating system.
2. Install VirtualBox.
3. Approve required networking components.
4. Restart if prompted.
5. Launch **VirtualBox Manager**.
6. Open **About** and record the version.

**Evidence:** screenshot of VirtualBox Manager and its version.

### Option 3 - VMware

1. Install **VMware Workstation Pro** on Windows/Linux or **VMware Fusion** on macOS.
2. Follow the installation prompts.
3. Restart if required.
4. Launch VMware.
5. Open **About** and record the installed version.

**Evidence:** screenshots showing VMware running and the installed version.

---

# Part B - Prepare Kali-Attacker

## Task 2 - Set Up Kali Linux

If Kali-Attacker is already supplied through Skillable, skip to **Task 4**.

For a local VMware environment, use the official Kali Linux VMware image.

Recommended resources:

```text
RAM: 4 GB
Virtual CPUs: 2
Storage: approximately 60 GB
```

### Step 1 - Import the VM

1. Download the official Kali Linux VMware image.
2. Extract the downloaded archive.
3. Keep the extracted files together.
4. Open VMware.
5. Select **Open a Virtual Machine**.
6. Open the extracted `.vmx` file.
7. Name the VM:

```text
Kali-Attacker
```

### Step 2 - Configure Resources

With the VM powered off:

1. Allocate approximately **4 GB RAM**.
2. Allocate **2 virtual CPUs**.
3. Use **NAT** temporarily while installing updates.

### Step 3 - Start Kali

Start the VM. If VMware asks whether it was moved or copied, select:

```text
I Copied It
```

For an official prebuilt Kali VM, the default credentials may be:

```text
Username: kali
Password: kali
```

Use instructor-provided credentials if different.

### Step 4 - Update Kali

```bash
sudo apt update
```

```bash
sudo apt full-upgrade -y
```

Restart if required.

### Step 5 - Verify Kali

```bash
whoami
```

```bash
cat /etc/os-release
```

```bash
ip addr
```

Confirm that Kali is working and that a network interface is present.

### Step 6 - Configure the Lab Network

Shut down Kali-Attacker and change its network adapter from NAT to the instructor-designated **host-only** network.

Disconnect unnecessary NAT or bridged adapters during testing.

### Step 7 - Create a Snapshot

Create a snapshot named:

```text
Clean Kali-Attacker Installation
```

**Evidence:** VM name, hardware settings, network settings, verification commands, final host-only configuration, and snapshot.

---

# Part C - Prepare Ubuntu-Server

## Task 3 - Install or Access Ubuntu-Server

If Ubuntu-Server is already supplied through Skillable, skip to **Task 4**.

### Step 1 - Create the VM

Create a new VMware virtual machine and choose:

```text
Linux
Ubuntu 64-bit
```

Name the VM:

```text
Ubuntu-Server
```

### Step 2 - Configure Resources

Recommended resources:

```text
RAM: 4 GB
Virtual CPUs: 2
Virtual disk: 60 GB
```

Attach the Ubuntu Server ISO and use NAT temporarily for installation and updates.

### Step 3 - Install Ubuntu Server

Configure language, keyboard, guided storage, hostname, user account, and password.

Use the hostname:

```text
ubuntu-server
```

### Step 4 - Update Ubuntu

```bash
sudo apt update
```

```bash
sudo apt full-upgrade -y
```

Restart if required.

### Step 5 - Verify Ubuntu

```bash
hostname
```

```bash
whoami
```

```bash
cat /etc/os-release
```

```bash
ip addr
```

### Step 6 - Configure the Lab Network

Shut down Ubuntu-Server and connect it to the **same host-only network** as Kali-Attacker.

Start it again and check:

```bash
ip addr
```

### Step 7 - Create a Snapshot

Create a snapshot named:

```text
Clean Ubuntu-Server Installation
```

**Evidence:** VM name, hardware settings, installation completion, verification commands, final host-only network, IP address, and snapshot.

---

# Part D - Prepare Evidence Collection

## Task 4 - Create an Evidence Folder

On **Kali-Attacker**, run:

```bash
mkdir -p ~/lab-evidence/week1
```

Then:

```bash
cd ~/lab-evidence/week1
```

Confirm the location:

```bash
pwd
```

Expected result:

```text
/home/<your-user>/lab-evidence/week1
```

Save all scan outputs from this lab in this folder.

---

# Part E - Confirm the Authorised Scope

## Task 5 - Verify the Testing Scope

The example authorised discovery subnet is:

```text
192.168.1.0/24
```

Permitted activities:

| Activity | Authorised scope |
|---|---|
| Host discovery | Instructor-approved lab subnet |
| TCP port scanning | Confirmed Ubuntu-Server IP only |
| Service/version detection | Confirmed Ubuntu-Server IP only |

Do **not** scan:

- the physical host computer;
- VMware network infrastructure;
- university systems;
- home-network devices;
- another student's systems;
- unidentified responding devices;
- any system not explicitly authorised.

Record this statement in your notes:

> I will perform host discovery within the authorised lab subnet. Port scanning and service-version detection will be restricted to the confirmed IP address of Ubuntu-Server.

---

# Part F - Check Network Configuration

## Task 6 - Identify the IP Address on Each VM

Run on **both** VMs:

```bash
ip -br addr
```

Example:

```text
lo       UNKNOWN        127.0.0.1/8
eth0     UP             192.168.1.10/24
```

Ignore the loopback interface `lo`.

Record:

| VM | Lab interface | IPv4 address/prefix |
|---|---|---|
| Kali-Attacker |  |  |
| Ubuntu-Server |  |  |

---

## Task 7 - Check the Routing Table

Run on both VMs:

```bash
ip route
```

Look for a route to the lab subnet.

Example:

```text
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10
```

> [!note]
> A host-only network does not need a default gateway for communication between VMs on the same subnet.

---

## Task 8 - Confirm Kali's Route to Ubuntu

On Kali-Attacker:

```bash
ip route get 192.168.1.20
```

Replace `192.168.1.20` with Ubuntu-Server's actual address.

Example:

```text
192.168.1.20 dev eth0 src 192.168.1.10
```

- `dev` shows the interface used.
- `src` shows Kali's source address.

> [!note]
> This confirms route selection. It does not prove the target is reachable.

---

## Task 9 - Record Network Configuration

Complete:

| Virtual machine | Lab interface | IPv4 address/prefix | Route to lab subnet |
|---|---|---|---|
| Kali-Attacker |  |  |  |
| Ubuntu-Server |  |  |  |

If an address is missing, duplicated, outside the authorised subnet, or routed through the wrong interface, resolve the issue before continuing.

---

# Part G - Test Connectivity

## Task 10 - Ping Ubuntu-Server

On Kali-Attacker:

```bash
ping -c 4 192.168.1.20
```

Replace the example IP with Ubuntu-Server's actual IP.

The option:

```text
-c 4
```

limits the test to four requests.

---

## Task 11 - Interpret the Ping Result

| Result | Interpretation |
|---|---|
| `4 transmitted, 4 received, 0% packet loss` | Ubuntu-Server responded to all requests. |
| Some replies received | Connectivity exists, but packet loss occurred. |
| `4 transmitted, 0 received, 100% packet loss` | No ICMP echo replies were received. |
| `Destination Host Unreachable` | The reporting system could not reach the destination. |

> [!note]
> A failed ping does **not** prove that a target is offline. ICMP may be filtered.

Complete:

| Source | Target IP | Requests sent | Replies received | Packet loss | Interpretation |
|---|---|---:|---:|---:|---|
| Kali-Attacker |  | 4 |  |  |  |

**Evidence:** screenshot showing the ping command and final summary.

---

# Part H - Discover Responsive Hosts

## Task 12 - Run an Authorised Host-Discovery Scan

On Kali-Attacker:

```bash
cd ~/lab-evidence/week1
```

Then:

```bash
sudo nmap -sn -n 192.168.1.0/24 -oN host-discovery.txt
```

Use the instructor-approved subnet if different.

---

## Task 13 - Understand the Discovery Command

| Command element | Purpose |
|---|---|
| `sudo` | Runs Nmap with elevated privileges. |
| `-sn` | Performs host discovery without a port scan. |
| `-n` | Disables reverse DNS resolution. |
| `192.168.1.0/24` | Specifies the authorised subnet. |
| `-oN host-discovery.txt` | Saves readable output to a file. |

Typical output may include:

```text
Nmap scan report for 192.168.1.20
Host is up.
```

---

## Task 14 - Review the Saved Discovery File

```bash
cat host-discovery.txt
```

Confirm it exists:

```bash
ls -lh host-discovery.txt
```

Look for:

```text
Nmap scan report for
```

and:

```text
Host is up
```

---

## Task 15 - Build a Host Inventory

Complete:

| Responding IP | MAC address if shown | Verified device | In authorised inventory? | Approved for further scanning? |
|---|---|---|---|---|
|  |  | Kali-Attacker | Yes | No - testing workstation |
|  |  | Ubuntu-Server | Yes | Yes - once confirmed |
|  |  | Unverified device | Unknown | No |

> [!alert]
> Do not scan an unidentified device.

**Key point:**

```text
Discovery identifies responding hosts.
Authorisation determines which hosts may be tested.
```

---

# Part I - Confirm the Target

## Task 16 - Confirm Ubuntu-Server's IP Address

On Ubuntu-Server:

```bash
hostname
```

Then:

```bash
ip -br addr
```

Record the IPv4 address of the lab interface. Ignore `127.0.0.1`.

---

## Task 17 - Match Ubuntu with the Discovery Results

On Kali-Attacker:

```bash
cat ~/lab-evidence/week1/host-discovery.txt
```

Find the address that exactly matches the Ubuntu console.

---

## Task 18 - Identify Kali Separately

On Kali-Attacker:

```bash
ip -br addr
```

Record Kali's IP and label it:

```text
Testing workstation - not target
```

---

## Task 19 - Record the Confirmed Target

| Device | IP address | Verification evidence | Approved for TCP/service scanning? |
|---|---|---|---|
| Ubuntu-Server |  | Ubuntu console IP matched discovery output | Yes |
| Kali-Attacker |  | Kali interface address | No |
| Other responding device |  | Asset list / unverified | Only if explicitly authorised |

> [!alert]
> If Ubuntu's console address does not appear in the discovery results, stop and investigate. Do not substitute another responding address.

---

# Part J - Identify Open TCP Ports

## Task 20 - Run a Full TCP Connect Scan

On Kali-Attacker:

```bash
cd ~/lab-evidence/week1
```

Then:

```bash
nmap -sT -n -p- 192.168.1.20 -oN ubuntu-tcp-ports.txt
```

Replace `192.168.1.20` with the confirmed Ubuntu-Server IP address.

A full TCP scan may take several minutes.

---

## Task 21 - Understand the TCP Scan

| Command element | Purpose |
|---|---|
| `-sT` | Performs a TCP connect scan. |
| `-n` | Disables reverse DNS resolution. |
| `-p-` | Scans TCP ports 1-65535. |
| Target IP | Specifies the confirmed Ubuntu-Server only. |
| `-oN ubuntu-tcp-ports.txt` | Saves results to a readable text file. |

---

## Task 22 - Interpret Port States

| Port state | Meaning |
|---|---|
| **open** | An application is accepting TCP connections. |
| **closed** | The host is reachable, but no application is accepting connections on the port. |
| **filtered** | Nmap cannot conclusively determine the port state because filtering or another network obstacle prevents a clear response. |

> [!note]
> An **open port is not automatically a vulnerability**.

---

## Task 23 - Review and Record Open Ports

```bash
cat ubuntu-tcp-ports.txt
```

Complete:

| Target IP | Port/protocol | State | Nmap service label |
|---|---|---|---|
|  |  |  |  |

Also record any summary such as:

```text
Not shown: ... closed tcp ports
```

**Evidence:** scan command, completed scan, open ports, summaries, and `ubuntu-tcp-ports.txt`.

---

# Part K - Identify Service Versions

## Task 24 - Review the Open Ports

```bash
cat ~/lab-evidence/week1/ubuntu-tcp-ports.txt
```

Write down only the ports marked `open`.

Example:

```text
22
80
```

---

## Task 25 - Run Service-Version Detection

If the open ports are `22` and `80`, run:

```bash
nmap -sT -sV -n -p 22,80 192.168.1.20 -oN ubuntu-services.txt
```

Replace the port list and target IP with your actual results.

---

## Task 26 - Understand the Version Scan

| Command element | Purpose |
|---|---|
| `-sT` | Uses a TCP connect scan. |
| `-sV` | Sends service-specific probes to identify applications and versions. |
| `-n` | Disables reverse DNS resolution. |
| `-p 22,80` | Restricts testing to listed open ports. |
| `-oN ubuntu-services.txt` | Saves readable output to a file. |

---

## Task 27 - Interpret Service Detection

Typical columns are:

```text
PORT     STATE   SERVICE   VERSION
```

Distinguish between:

- **Service** — protocol/service type, such as SSH or HTTP.
- **Product** — software providing the service, such as OpenSSH or Apache httpd.
- **Version** — reported software release, where available.

> [!note]
> Service detection is stronger evidence than assuming a service from a port number, but the result can still be incomplete or uncertain.

---

## Task 28 - Build a Service Inventory

Complete:

| Target IP | Port/protocol | State | Detected service | Product | Reported version |
|---|---|---|---|---|---|
|  |  |  |  |  |  |

If Nmap does not identify a product or version, enter:

```text
Not identified
```

Do not guess.

**Evidence:** command, results, `ubuntu-services.txt`, and completed service inventory.

---

# Part L - Interpret the Findings

## Task 29 - Review the Evidence Files

```bash
cd ~/lab-evidence/week1
```

Then:

```bash
ls -lh
```

You should have files similar to:

```text
host-discovery.txt
ubuntu-tcp-ports.txt
ubuntu-services.txt
```

Review them:

```bash
cat host-discovery.txt
```

```bash
cat ubuntu-tcp-ports.txt
```

```bash
cat ubuntu-services.txt
```

---

## Task 30 - Separate Observation from Conclusion

| Observation | Appropriate interpretation |
|---|---|
| A TCP port is open. | A service is accessible; this alone does not establish a vulnerability. |
| Nmap reports a product/version. | This is a lead for further verification. |
| A port is filtered. | Nmap could not conclusively determine whether it was open or closed. |
| A host did not respond. | It may be offline, or discovery traffic may have been blocked. |

---

## Task 31 - Identify Limitations

Your findings should acknowledge that:

- the lab assessed TCP ports;
- UDP services were not assessed;
- application security was not fully tested;
- product/version detection may be incomplete;
- reported versions may not perfectly represent installed patch status;
- configuration can affect vulnerability applicability;
- a version string alone does not prove exploitability.

---

## Task 32 - Explain Further Verification Requirements

Before reporting a vulnerability, you would need to:

1. confirm the exact service identity;
2. confirm installed package version and patch status;
3. compare verified information with vendor security advisories;
4. confirm that required vulnerable conditions are present;
5. perform only instructor-approved, non-disruptive validation;
6. retain supporting evidence.

---

## Task 33 - Write a 100-150 Word Findings Summary

Include:

- number of responsive hosts;
- confirmed Ubuntu-Server IP address;
- open TCP ports;
- detected services;
- products and versions where identified;
- uncertainty and limitations;
- whether any vulnerability was validated;
- references to saved evidence files.

Suggested structure:

```text
Host discovery identified [number] responsive hosts within the authorised
subnet. Ubuntu-Server was confirmed at [IP address]. TCP scanning identified
[ports], with service detection reporting [services/products/versions].

The results were limited by [uncertainties or coverage limitations]. These
observations identify accessible services but do not establish a confirmed
vulnerability. Further verification would include [specific checks].

Supporting evidence is recorded in [file names].
```

> [!alert]
> If you did not validate a vulnerability, state that clearly.

---

# Final Validation

## Task 34 - Check Your Work

- [ ] Accessed or created Kali-Attacker.
- [ ] Accessed or created Ubuntu-Server.
- [ ] Confirmed both use the authorised lab network.
- [ ] Recorded Kali's IP address.
- [ ] Recorded Ubuntu's IP address.
- [ ] Checked routing information.
- [ ] Tested connectivity.
- [ ] Performed authorised host discovery.
- [ ] Confirmed Ubuntu's identity before scanning.
- [ ] Performed a TCP port scan against Ubuntu only.
- [ ] Saved the TCP scan results.
- [ ] Performed service/version detection only on discovered open ports.
- [ ] Saved service-detection results.
- [ ] Completed the host inventory.
- [ ] Completed the service inventory.
- [ ] Wrote a 100-150 word findings summary.
- [ ] Distinguished observations from confirmed vulnerabilities.

---

# Troubleshooting

## Problem - Kali and Ubuntu Cannot Communicate

Check both VMs:

```bash
ip -br addr
```

Then:

```bash
ip route
```

Confirm both machines use the same host-only network, have different addresses, and are in the same subnet.

---

## Problem - Ping Fails

Check the route:

```bash
ip route get <UBUNTU_IP>
```

Also verify VM power state, network adapter, interface status, IP address, and subnet.

Remember: ICMP may be filtered.

---

## Problem - Ubuntu Is Missing from Discovery

On Ubuntu:

```bash
hostname
```

```bash
ip -br addr
```

Compare the address with:

```bash
cat ~/lab-evidence/week1/host-discovery.txt
```

Do not scan another address instead.

---

## Problem - Nmap Is Not Installed

Check:

```bash
nmap --version
```

If necessary:

```bash
sudo apt update
sudo apt install nmap -y
```

---

## Problem - No Open TCP Ports Are Reported

Record the result accurately. Do not disable security controls simply to create an open-port result. Check with your instructor whether expected services are running.

---

## Problem - Nmap Does Not Identify a Version

Record:

```text
Not identified
```

Do not guess.

---

# Knowledge Check

1. Why should the authorised scope be confirmed before scanning?
2. What is the purpose of `ip -br addr`?
3. What does `ip route` show?
4. Why does a failed ping not prove that a host is offline?
5. What does `nmap -sn` do?
6. What is the purpose of `-n` in Nmap?
7. Why must Ubuntu-Server's IP address be confirmed before a port scan?
8. What does `-p-` mean?
9. What is the difference between an **open**, **closed**, and **filtered** TCP port?
10. What does `-sV` do?
11. Why does a reported service version not automatically prove a vulnerability?
12. Why should an unidentified responding host not be scanned further?
13. What is the purpose of `-oN`?
14. What evidence should support a penetration-testing conclusion?

---

# Challenge

Using only the authorised Ubuntu-Server:

1. identify its current IP address;
2. confirm Kali's route to it;
3. run host discovery against the approved subnet;
4. locate Ubuntu in the results;
5. run a TCP scan against Ubuntu;
6. identify its open ports;
7. perform service detection only on those ports;
8. save all outputs;
9. write three statements:

```text
Confirmed observation:
Possible concern:
Further verification required:
```

Each statement must be supported by evidence collected during the lab.

---

## Summary

In this lab, you:

- prepared an authorised ethical-hacking environment;
- configured Kali-Attacker and Ubuntu-Server;
- verified IP addressing and routes;
- defined the authorised testing scope;
- tested connectivity;
- discovered responsive hosts;
- confirmed the authorised target;
- scanned TCP ports;
- identified services and reported versions;
- saved Nmap evidence;
- distinguished observations from vulnerability conclusions;
- produced an evidence-based findings summary.

You are now ready to continue with later penetration-testing activities using the same controlled and evidence-based workflow.
