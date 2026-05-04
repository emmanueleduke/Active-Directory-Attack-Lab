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
| Domain Controller | HYDRA-DC (EMMY.local) |
| Network | NAT (same subnet) |
| Tools | Responder, ntlmrelayx, mitm6, Hashcat, John the Ripper, CrackMapExec, Nmap, Impacket |

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
├── 03-IPV6-Attack/
│   ├── README.md
│   ├── lootme/
│   └── screenshots/
├── 04-Kerberoasting/          (coming soon)
├── 05-Pass-the-Hash/          (coming soon)
└── 06-Privilege-Escalation/   (coming soon)
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
| Captured | SAM Hashes, Meterpreter Shell, Interactive SMB Shell |
| MITRE | [T1557.001](https://attack.mitre.org/techniques/T1557/001/), [T1550.002](https://attack.mitre.org/techniques/T1550/002/), [T1003.002](https://attack.mitre.org/techniques/T1003/002/) |
| Difficulty | Intermediate |

---

### ✅ 03 — IPv6 Attack (Full Domain Compromise)
**[View Module →](03-IPV6-Attack/README.md)**

Exploits IPv6 being enabled by default on all modern Windows machines. mitm6 poisons DHCPv6 requests, assigns itself as rogue DNS, and relays authentication to the DC via LDAPS — resulting in full domain enumeration, hash dumping, and SYSTEM access on the Domain Controller.

| Detail | Info |
|--------|------|
| Tools | mitm6 + ntlmrelayx + secretsdump |
| Captured | Full domain enumeration, all domain hashes, DC shell |
| MITRE | [T1557.001](https://attack.mitre.org/techniques/T1557/001/), [T1003.003](https://attack.mitre.org/techniques/T1003/003/), [T1550.002](https://attack.mitre.org/techniques/T1550/002/) |
| Difficulty | Advanced |

---

### 🔜 04 — Kerberoasting
Requests Kerberos service tickets for domain service accounts and cracks them offline to extract plaintext credentials.

### 🔜 05 — Pass-the-Hash
Uses captured NTLM hashes directly for authentication without cracking — enabling lateral movement across the domain.

### 🔜 06 — Privilege Escalation
Exploits AD misconfigurations to escalate from standard user to Domain Admin.

---

## MITRE ATT&CK Mapping

| Module | Technique | ID |
|--------|-----------|-----|
| LLMNR Poisoning | Adversary-in-the-Middle: LLMNR/NBT-NS Poisoning | [T1557.001](https://attack.mitre.org/techniques/T1557/001/) |
| SMB Relay | Use of Alternate Authentication Material: Pass the Hash | [T1550.002](https://attack.mitre.org/techniques/T1550/002/) |
| SMB Relay | OS Credential Dumping: SAM | [T1003.002](https://attack.mitre.org/techniques/T1003/002/) |
| IPv6 Attack | OS Credential Dumping: NTDS | [T1003.003](https://attack.mitre.org/techniques/T1003/003/) |
| IPv6 Attack | Unsecured Credentials | [T1552](https://attack.mitre.org/techniques/T1552/) |
| All Modules | Use of Valid Accounts | [T1078](https://attack.mitre.org/techniques/T1078/) |

---

## Detection & Mitigation Summary

| Attack | Key Mitigation |
|--------|---------------|
| LLMNR Poisoning | Disable LLMNR and NetBIOS via Group Policy |
| SMB Relay | Enable SMB signing on all machines |
| IPv6 Attack | Disable IPv6 if not needed, enable LDAP signing |
| All | Monitor NTLM authentication patterns (Event ID 4648, 4624) |

---

## Key Takeaways

- Legacy protocols like LLMNR expose credentials with zero effort from the attacker
- SMB Relay is more dangerous — no cracking required, works in real time
- IPv6 attacks bypass LLMNR mitigations and require zero user interaction
- A single misconfigured service account (password in description) led to full domain compromise
- SMB signing and IPv6 disabling are the most effective single controls
- Proper detection requires both **network** (Suricata) and **host** (Splunk/Event Logs) visibility

---

## Author

**Emmanuel Eduke**
SOC Analyst | Blue Team Enthusiast | Active Directory Security

---

*This project reflects a practical approach to understanding both offensive techniques and defensive detection strategies in enterprise environments.*
