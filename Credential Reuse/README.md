# Credential Reuse

## Overview

This lab demonstrates the security risks associated with credential reuse across multiple services. The objective was to validate whether credentials obtained from one service could be used to gain unauthorized access to additional services hosted on the target system.

## Lab Environment

| Component        | Description            |
| ---------------- | ---------------------- |
| Attacker Machine | GIR Forge              |
| Target Machine   | GIR-Citadel            |
| Services Tested  | FTP, SSH, IMAPS, POP3S |

---

## Objective

* Identify valid credentials.
* Test credential reuse across multiple services.
* Assess authentication security posture.
* Demonstrate the impact of password reuse.
* Analyze security risks associated with shared credentials.

---

## Background

Credential reuse occurs when the same username and password combination is used across multiple services.

If an attacker compromises one service, they may gain access to additional systems without performing further attacks.

Credential reuse is a common cause of enterprise breaches and lateral movement.

---

## Methodology

### Step 1 – Identify Available Services

Enumerate services running on the target.

```bash
nmap -sV <target-ip>
```

Purpose:

* Identify authentication services
* Determine attack surface
* Confirm exposed protocols

Services discovered:

* FTP (21)
* SSH (22)
* IMAPS (993)
* POP3S (995)

---

### Step 2 – Validate FTP Credentials

Test known credentials against FTP.

```bash
ftp <target-ip>
```

Credentials used:

```text
Username: opsadmin
Password: Password@123

Username: formerftp
Password: Password@!23
```
<img width="1280" height="800" alt="ftp login" src="https://github.com/user-attachments/assets/c49bab5d-80aa-48ad-a228-9d141cb17f93" />

For user := formerftp

<img width="567" height="217" alt="formerftp login" src="https://github.com/user-attachments/assets/1b8af6ae-616b-4a8b-9fb6-299fe2d329bc" />


Purpose:

* Verify credential validity
* Confirm successful authentication

---

### Step 3 – Validate SSH Access

Attempt SSH authentication using the same credentials.

```bash
ssh opsadmin@<target-ip>
```

<img width="970" height="717" alt="ssh" src="https://github.com/user-attachments/assets/343bfa1e-b145-4fb0-9983-938af2773599" />

For user := formerftp

<img width="1256" height="728" alt="ssh formerftp" src="https://github.com/user-attachments/assets/cbe43691-7bc1-4c9a-85a5-94444f8cc91e" />


Purpose:

* Test credential reuse
* Validate remote shell access

---

### Step 4 – Validate IMAPS Authentication

Connect to the IMAPS service.

```bash
openssl s_client -connect <target-ip>:993 -quiet
```

Authenticate:

```text
a login opsadmin Password@123
```

<img width="1280" height="245" alt="openssl" src="https://github.com/user-attachments/assets/82f1bbee-f25c-478c-9745-08d188cb1ac1" />


Purpose:

* Test email authentication
* Confirm cross-service credential reuse

---

### Step 5 – Validate POP3S Authentication

Connect to POP3S.

```bash
openssl s_client -connect <target-ip>:995 -quiet
```

Authenticate: user opsadmin / pass Password@123

<img width="650" height="155" alt="pop3" src="https://github.com/user-attachments/assets/80b39c65-7a2b-4b26-b666-ef7970c38e83" />


```text
user opsadmin
pass Password@123
```

Purpose:

* Verify mailbox access
* Demonstrate shared credentials

---

## Findings

The same credentials successfully authenticated against multiple services.

Validated credentials:

```text
opsadmin : Password@123
```

Affected services:

* FTP
* SSH
* IMAPS
* POP3S

This confirms credential reuse across the environment.

---

## Security Impact

Credential reuse significantly increases risk because compromise of a single account can provide access to multiple services.

Potential impacts include:

* Unauthorized system access
* Email compromise
* Lateral movement
* Data exposure
* Privilege escalation opportunities

---

## MITRE ATT&CK Mapping

| Technique | Description      |
| --------- | ---------------- |
| T1078     | Valid Accounts   |
| T1021.004 | SSH              |
| T1110     | Brute Force      |
| T1114     | Email Collection |

---

## Detection Opportunities

SOC teams should monitor:

* Multiple successful logins from the same IP
* Authentication activity across multiple services
* Login anomalies
* Credential reuse patterns
* Access to mail services after remote login

---

## Remediation

* Enforce unique passwords
* Implement Multi-Factor Authentication (MFA)
* Apply password rotation policies
* Monitor authentication logs
* Configure account lockout controls

---

## Skills Demonstrated

* Service Enumeration
* Authentication Testing
* Credential Validation
* Attack Path Analysis
* Security Assessment
* SOC Detection Awareness

---

## Conclusion

This lab demonstrated how credential reuse can lead to compromise across multiple services. The findings highlight the importance of unique credentials, strong authentication controls, and continuous monitoring of authentication activity.
