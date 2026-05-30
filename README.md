# Sysmon-Threat-Hunting-Investigation
Proactive threat hunt investigating an EDR bypass to uncover Base64 obfuscated PowerShell payloads, schtasks persistence, and fileless C2 beacons using Sysmon telemetry.
# Endpoint Threat Hunting: Investigating EDR Bypass and Fileless Execution

## Project Description
This case study details a proactive threat hunting investigation triggered by a network anomaly warning. An alert flagged a connection to a known malicious external IP address, but the automated Endpoint Detection and Response system failed to block or mitigate the activity. Operating under the assumption of a live compromise, I analyzed raw Sysmon event logs to trace the initial entry vector, identify the persistence mechanism, decode the obfuscated attack payloads, and evaluate the threat actor's ultimate objective.

## Analytical Framework and Telemetry Correlated
* **Sysmon Event ID 3 (Network Connection):** Utilized to isolate the exact process communicating out of the network interface. This analysis confirmed that PowerShell initiated an outbound connection over Port 80 to the malicious destination IP 192.168.10.45.
* **Sysmon Event ID 1 (Process Creation):** Analyzed to hunt for persistence indicators. This revealed an unauthorized deployment of the Windows Task Scheduler binary, schtasks.exe, executing a covert persistence loop.

## The Attack Chain Timeline Uncovered

### 1. Initial Execution and Evasion
The threat actor initiated execution via PowerShell using a heavily obfuscated, Base64-encoded command string. This technique effectively hid the program's true intent from standard signature-based antivirus definitions, allowing the process to execute completely unhindered by the local endpoint defenses.

### 2. Persistence Mechanism
To ensure continued access to the system following user logouts or reboots, the adversary established a persistent foothold using the local task scheduler. The investigation captured a command creating a recurring task under the deceptive name WindowsUpdateCheck. This task was engineered to silently execute a hidden local payload, Update.exe, deep within the Public user directory every 30 minutes.

### 3. Payload Deobfuscation and C2 Discovery
The obfuscated PowerShell payload was extracted and safely decoded inside a Linux terminal environment. The de-cloaked command exposed an active cradle utilizing the Net.WebClient Class to execute an Invoke-Expression function. The process was mapped making a call to pull a raw script file, shell.ps1, straight from the attacker's staging server.

## Forensic Conclusions
The investigation proved that the endpoint suffered a confirmed fileless command and control intrusion. Because the secondary stage payload is fetched directly from the network and compiled straight into the local system's active memory context, it leaves no immediate footprint on the physical hard drive. This underscores the critical importance of behavioral log tracking over basic automated scanning tools.

## Full Threat Hunting Brief PDF
The complete forensic file—including the raw JSON log snips, VS Code workspace configurations, and the step-by-step Linux terminal decoding sequence—is fully preserved in this repository. 

Please download the complete PDF report directly from the file explorer view below to read the full illustrated case study!
