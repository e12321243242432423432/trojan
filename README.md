⚠️ **DISCLAIMER: FOR EDUCATIONAL AND RESEARCH PURPOSES ONLY.**  
This repository contains a Proof of Concept (PoC) demonstrating how malware can exploit legitimate system utilities and social engineering techniques. Do not deploy this code against systems without explicit permission. The author is not responsible for any misuse.

---

## 📌 Project Overview
This project illustrates a multi-stage backdoor and ransomware logic targeting Windows environments. It demonstrates how a lightweight loader can coordinate system configuration changes, persistence, and local network traversal.

### 🔄 Execution Flow
[User Execution (Admin)]│▼[cheat_easy.exe (Loader)] ───► Launches ───► [poc.dll]│▼┌───────────────────────┐│  System Modification  │└───────────┬───────────┘│┌────────────────────────────────────┼────────────────────────────────────┐▼                                    ▼                                    ▼[Data Destruction]                  [User Lockout]                       [Network Traversal]• Forced BitLocker Drive            • Resets active user                 • Enables Remote Desktop (RDP)Encryption (manage-bde)           password to %random%             • Automatically maps port 3389• Overrides TPM check via Registry  • Registers hacker:Pass123           on home router via PowerShellas Local Administrator               UPnP COM Object
---

## 🛠️ Technical Deep Dive

### 1. Living off the Land (BitLocker Exploitation)
Instead of compiling a custom encryption engine, the PoC relies on Windows' built-in security features. By modifying the `HKLM\Software\Policies\Microsoft\FVE` registry keys, it bypasses the physical TPM requirement, enabling `manage-bde` to silently lock the C: drive.

### 2. Backdoor & Remote Access Setup
The DLL configures a complete environment for unauthorized remote desktop connections:
* Modifies terminal server registry entries to accept inbound RDP connections.
* Modifies Windows Advanced Firewall rules (`netsh advfirewall`).
* Executes a PowerShell script utilizing the `HNetCfg.NATUPnP` COM Object to dynamically trigger port forwarding (Port 3389) on the local NAT gateway.

### 3. Social Engineering Strategy
The payload is compiled under names like `cheat_easy.exe` to leverage the psychology of end-users searching for gaming modifications. This specifically targets the user habit of ignoring antivirus alerts, turning security warnings into an invitation to grant administrative privileges.

---

## 🔬 Lab Observations (Analysis in Sandbox)
* **Kernel Stability:** The code does not crash `ntoskrnl.exe`. No `KeBugCheck` (BSOD) is triggered since the operations leverage standard system API calls.
* **Telemetry Trigger:** Modern AV clouds instantly flag the binary behavior during sandboxing due to registry manipulation and the presence of `manage-bde` syntax.

---
Created by [@e12321243242432423432]
