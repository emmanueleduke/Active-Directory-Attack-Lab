# Active Directory Attack Lab

This repository contains hands-on simulations of common **Active Directory (AD) attack techniques** performed in a controlled lab environment.

The goal is to understand how attackers exploit misconfigurations in AD and how **Security Operations Center (SOC)** teams can detect and mitigate these threats.

> ⚠️ All activities were conducted in an isolated lab for educational purposes only.

---

## Objectives

- Simulate real-world Active Directory attack techniques
- Understand credential exposure and lateral movement risks
- Map attacker behavior to SOC detection strategies
- Demonstrate defensive mitigation techniques

---

## Lab Environment

| Component | Description |
|-----------|-------------|
| Attacker | Kali Linux (VMware) |
| Victim 1 | THEPUNISHER — Windows Domain-Joined Machine |
| Victim 2 | SPIDERMAN — Windows Domain-Joined Machine |
| Domain Controller | EMMY DC |
| Network | NAT (same subnet) |
| Tools | Responder, ntlmrelayx, Hashcat, John the Ripper, CrackMapExec, Nmap |

---

## Project Structure

```text
Active-Directory-Attack-Lab/
├── README.md
├── 01-LLMNR-Poisoning/
│   ├── README.md
│   └── screenshots/
├── 02-SMB-Relay/
│   ├── README.md
│   └── screenshots/
├── 03-Kerberoasting/          (coming soon)
├── 04-Pass-the-Hash/          (coming soon)
└── 05-Privilege-Escalation/   (coming soon)
```

---

## Attack Modules

### ✅ 01 — LLMNR/NBT-NS Poisoning (Credential Capture)
**[View Module →](01-LLMNR-Poisoning/README.md)**

Simulates how attackers exploit legacy name resolution protocols to capture NTLM authentication hashes from victims on the same network.

| Detail | Info |
|--------|------|
| Tool | Responder |
| Captured | NTLMv2 Hash |
| MITRE | [T1557.001](https://attack.mitre.org/techniques/T1557/001/) |
| Difficulty | Beginner |

---

### ✅ 02 — SMB Relay Attack (Credential Relay & Hash Dump)
**[View Module →](02-SMB-Relay/README.md)**

Instead of cracking hashes offline, this attack relays intercepted NTLM authentication in real time to another machine — gaining admin access and dumping SAM hashes without knowing the plaintext password.

| Detail | Info |
|--------|------|
| Tools | Responder + ntlmrelayx |
| Captured | SAM Hashes (local accounts) |
| MITRE | [T1557.001](https://attack.mitre.org/techniques/T1557/001/), [T1550.002](https://attack.mitre.org/techniques/T1550/002/), [T1003.002](https://attack.mitre.org/techniques/T1003/002/) |
| Difficulty | Intermediate |

---

### 🔜 03 — Kerberoasting
Requests service tickets for domain accounts and cracks them offline to extract service account credentials.

### 🔜 04 — Pass-the-Hash
Uses captured NTLM hashes directly for authentication without cracking — enabling lateral movement across the domain.

### 🔜 05 — Privilege Escalation
Exploits AD misconfigurations to escalate from standard user to Domain Admin.

---

## MITRE ATT&CK Mapping

| Module | Technique | ID |
|--------|-----------|-----|
| LLMNR Poisoning | Adversary-in-the-Middle: LLMNR/NBT-NS Poisoning | [T1557.001](https://attack.mitre.org/techniques/T1557/001/) |
| SMB Relay | Use of Alternate Authentication Material: Pass the Hash | [T1550.002](https://attack.mitre.org/techniques/T1550/002/) |
| SMB Relay | OS Credential Dumping: SAM | [T1003.002](https://attack.mitre.org/techniques/T1003/002/) |
| Both | Use of Valid Accounts | [T1078](https://attack.mitre.org/techniques/T1078/) |

---

## Detection & Mitigation Summary

| Attack | Key Mitigation |
|--------|---------------|
| LLMNR Poisoning | Disable LLMNR and NetBIOS via Group Policy |
| SMB Relay | Enable SMB signing on all machines |
| Both | Monitor NTLM authentication patterns (Event ID 4648, 4624) |

---

## Key Takeaways

- Legacy protocols like LLMNR expose credentials with zero effort from the attacker
- SMB Relay is more dangerous — no cracking required, works in real time
- SMB signing enforcement is the single most effective control against relay attacks
- Weak passwords and excessive local admin rights amplify every attack in this lab
- Proper detection requires both **network** (Suricata) and **host** (Splunk/Event Logs) visibility

---

## Author

**Emmanuel Eduke**
SOC Analyst | Blue Team Enthusiast | Active Directory Security

---

*This project reflects a practical approach to understanding both offensive techniques and defensive detection strategies in enterprise environments.*
