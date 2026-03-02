# Nmap-and-VirusTotal-Analysis
Nmap scanning and VirusTotal intelligence analysis assignment.
                                         Part A – Nmap Scanning
STEP 1
Basic Nmap Scan:

Command Used
nmap scanme.nmap.org

Explanation of Command
nmap → Network scanning tool.
scanme.nmap.org → Official test server provided by Nmap for safe scanning.
This command performs a default TCP SYN scan.
It scans the top 1000 commonly used TCP ports.

It identifies:
Whether the host is up
Open ports
Basic service names

Scan Output:
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-02 10:24 +0530
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.24s latency).
Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
Not shown: 996 closed tcp ports (reset)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
9929/tcp  open  nping-echo
31337/tcp open  Elite

Nmap done: 1 IP address (1 host up) scanned in 10.24 seconds


Scan Results Analysis
Host Information
The target host is up and reachable.
IPv4 Address: 45.33.32.156
IPv6 address is also available but not scanned.
Latency: 0.24 seconds

Open  Ports Identified:

| Port  | State | Service    |
| ----- | ----- | ---------- |
| 22    | Open  | SSH        |
| 80    | Open  | HTTP       |
| 9929  | Open  | nping-echo |
| 31337 | Open  | Elite      |

3️.Port & Service Analysis
Port 22 – SSH:
  Used for secure remote login.
  Allows administrators to manage servers remotely.
  Security Risk: May be vulnerable to brute-force attacks if weak             credentials are used.

Port 80 – HTTP:
Used for web services.
Allows users to access web pages hosted on the server.
Security Risk: Web vulnerabilities such as XSS or SQL injection may exist if the web application is insecure.

Port 9929 – Nping Echo:
Used by Nmap’s nping tool for packet testing.
Not commonly open on most servers.
Indicates testing environment.

Port 31337 – Elite:
Historically associated with backdoors or testing services.
Often used in security research or demonstration environments.
Could pose a risk if misconfigured.

4️.Observations

Out of 1000 scanned ports, 996 were closed.
Only 4 ports are open.
The server is properly configured to limit exposed services.
This confirms basic reconnaissance findings.

STEP 2:

Service Version Detection Scan

Command Used
nmap -sV scanme.nmap.org

Explanation of Command
nmap → Network scanning tool.
-sV → Enables service version detection.
scanme.nmap.org → Target host.

The -sV option sends additional probes to open ports in order to identify:
Software name
Version number
Product details
Service implementation
This helps in vulnerability assessment.

Scan Output:

Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-02 14:41 +0530
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.23s latency).
Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
Not shown: 996 closed tcp ports (reset)
PORT      STATE SERVICE    VERSION
22/tcp    open  tcpwrapped
80/tcp    open  http       Apache httpd 2.4.7 ((Ubuntu))
9929/tcp  open  nping-echo Nping echo
31337/tcp open  tcpwrapped

Service detection performed.
Nmap done: 1 IP address (1 host up) scanned in 48.18 seconds


New Information Discovered

Compared to the basic scan:
The web server is running Apache httpd 2.4.7 on Ubuntu.
Service version information is now visible.
Some services appear as tcpwrapped, meaning the service is protected or filtered.

Updated Port & Service Analysis
Port	Service	Version	Analysis
22	tcpwrapped	Not disclosed	Likely SSH but protected
80	HTTP	Apache 2.4.7 (Ubuntu)	Web server running
9929	Nping Echo	Nping echo	Testing service
31337	tcpwrapped	Not disclosed	Possibly protected service

Vulnerability Reasoning
>>Apache 2.4.7 (Ubuntu):
Apache 2.4.7 is an older version.
Older Apache versions may contain known vulnerabilities (CVEs).
If not patched, it could allow:
Information disclosure
Remote code execution
Misconfiguration exploitation

>>tcpwrapped Services
tcpwrapped means the service is protected by TCP wrappers or firewall rules.
This is a good security practice.
It prevents unauthorized enumeration.

>Observation:
The server exposes limited services.
Version detection provides deeper intelligence than the basic scan.
This scan helps in identifying potential security risks.

STEP 3:

Aggressive Scan (-A)
Command Used
nmap -A scanme.nmap.org
Explanation of Command
nmap → Network scanning tool
-A → Enables aggressive scan features
scanme.nmap.org → Target host

The -A option enables:
OS Detection
Version Detection
Default NSE Script Scanning
Traceroute
This scan provides deeper reconnaissance compared to previous scans.

Scan Output:

PORT      STATE SERVICE    VERSION
22/tcp    open  ssh        OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 (Ubuntu Linux; protocol 2.0)
80/tcp    open  http       Apache httpd 2.4.7 ((Ubuntu))
9929/tcp  open  nping-echo Nping echo
31337/tcp open  tcpwrapped




New Information Discovered

Compared to previous scans:

Port 22 – SSH
Now identified as:
    OpenSSH 6.6.1p1
    Running on Ubuntu
SSH host keys were revealed (DSA, ECDSA, ED25519)

Port 80 – HTTP:
Apache version confirmed again:
    Apache 2.4.7 (Ubuntu)
HTTP title:
   “Go ahead and ScanMe!”
Server header confirmed:
    Apache/2.4.7 (Ubuntu)

Operating System Detection
  OS guessed as:
       Linux (Kernel versions 4.x – 5.x range)
  Service Info confirms:
       OS: Linux

Network Distance
16 hops away
Shows routing path to the server

Comparison Between Scans:
SCAN TYPE     INFORMATION REVEALED
Basic Scan	   Open ports only
-sV Scan	     Service versions
-A Scan	       OS details, scripts, traceroute, host keys

The aggressive scan provides the most complete reconnaissance data.

Vulnerability Reasoning:
OpenSSH 6.6.1p1:
   This is an older SSH version.
   Older SSH versions may contain known CVEs.

  Potential risks:
        Weak cryptographic algorithms
        Brute-force exposure if not protected

Apache 2.4.7 (Ubuntu):
Apache 2.4.7 is outdated.
Older Apache versions may be vulnerable to:
       Remote code execution
       Information disclosure
       Directory traversal 

SSH Host Keys Exposed:
    Revealing host keys is normal.
    However, weak key lengths (like 1024-bit DSA) may be less secure.

>>Security Observation:
   The server exposes limited services.
   OS fingerprinting confirms Linux environment.
   Traceroute shows network path and infrastructure.
   This scan demonstrates how attackers gather detailed system intelligence    during reconnaissance.

STEP 4:
Full Port Scan

Command Used
nmap -p- scanme.nmap.org

Explanation of Command
    nmap → Network scanning tool
    -p- → Scan all 65,535 TCP ports
     scanme.nmap.org → Target host
By default, Nmap scans only the top 1000 commonly used ports.
The -p- option forces Nmap to scan every possible TCP port from 1 to 65535.
This ensures that no uncommon or hidden open ports are missed.

SCAN OUTPUT:

Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-02 20:22 +0530
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.24s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
9929/tcp  open  nping-echo
31337/tcp open  Elite

Nmap done: 1 IP address (1 host up) scanned in 652.09 seconds
Analysis of Full Port Scan:

Out of 65,535 ports:
  65,531 ports are closed
  4 ports are open

No additional open ports were discovered beyond the default scan.
This confirms that the server exposes only a limited number of services.

COMPARISON OF ALL SCANS:

| Scan Type  | Ports Scanned | Information Revealed              |
| ---------- | ------------- | --------------------------------- |
| Basic Scan | Top 1000      | Open ports                        |
| -sV Scan   | Top 1000      | Service versions                  |
| -A Scan    | Top 1000      | OS detection, scripts, traceroute |
| -p- Scan   | All 65535     | Confirms no hidden open ports     |

>>Security Observation
The server is well configured.
Only necessary services are exposed.
No unexpected open ports were discovered.
This demonstrates good security practice by limiting attack surface.



                          PART B


What VirusTotal is used for?

VirusTotal is an online threat intelligence platform used to analyze domains, IP addresses, URLs, and files. It aggregates results from multiple antivirus engines and security vendors to determine whether a target is malicious or safe.
It provides passive intelligence, meaning it does not directly interact with the target system.

Reputation & Detection Results:
The domain scanme.nmap.org was analyzed using VirusTotal.
All listed security vendors marked the domain as clean, and no malicious detections were found.
This indicates:
  The domain has no known malware associations.
  It is considered safe by major security engines.
  There are no active security warnings.


Domain & IP Intelligence:

VirusTotal provides the following domain intelligence:
  Registrar: Dynadot Inc
  Domain age: 27 years
  Category: Information Technology / Computer Security
  Popularity: Top 1M domain
The long operational history and classification confirm that the domain is legitimate and well-established.

Historical & Contextual Data
  The domain has been active for 27 years and has been analyzed multiple times.
  There are no historical reports of malicious activity.
  This suggests a stable and trusted online presence.

Passive Intelligence vs Active Scanning:

Active Scanning (Nmap):
  Sends packets directly to the target.
  Identifies open ports, services, and system details.
  Detectable by the target system.

Passive Intelligence (VirusTotal):
  Uses previously collected security data.
  Does not send packets to the target.
  Completely stealthy.
  Provides reputation and historical analysis.




How VirusTotal Complements Nmap Results?

Nmap revealed that the server is running SSH and Apache services.
VirusTotal confirmed that the domain is legitimate and trusted, with no malicious reputation.

This demonstrates how passive intelligence can support active reconnaissance by validating the legitimacy and security status of a target.





6️.Conclusion

This exercise demonstrated how reconnaissance and vulnerability assessment are performed using Nmap and VirusTotal. Through active scanning, I identified open ports, running services, software versions, and operating system details. Using passive intelligence from VirusTotal, I verified the domain’s legitimacy and reputation.
The assignment helped me understand:
How attackers gather information during reconnaissance.
The importance of minimizing exposed ports.
The risks of outdated software versions.
The difference between active and passive intelligence gathering.
This practical experience improved my understanding of network security and threat analysis.







