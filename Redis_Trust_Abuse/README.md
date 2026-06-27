# Redis Trust Abuse

## 📌 Overview

This lab demonstrates how an improperly secured Redis server can expose sensitive information without requiring authentication. 
The objective was to identify an exposed Redis service, enumerate stored data, and assess the security risks associated with insecure Redis configurations.

---

## 🎯 Objective

- Identify an exposed Redis service.
- Connect to Redis without authentication.
- Enumerate stored keys.
- Retrieve sensitive information.
- Assess the security impact.
- Recommend mitigation strategies.

---

## 🖥️ Lab Environment

| Component | Description |
|-----------|-------------|
| Attacker Machine | GIR-Forge |
| Target Machine | GIR-Citadel |
| Service | Redis |
| Default Port | 6379 |

---

## 📖 Background

Redis is an open-source, in-memory key-value database widely used for:

- Application caching
- Session management
- Message queues
- Temporary data storage
- API response caching

Since Redis stores data in memory (RAM), it provides extremely fast read and write operations.

If Redis is exposed without proper authentication or network restrictions, attackers may gain access to sensitive information stored by applications.

---

# Attack Flow

```
Network Scan
      │
      ▼
Redis Service Discovered
      │
      ▼
Connect to Redis
      │
      ▼
No Authentication Required
      │
      ▼
Enumerate Keys
      │
      ▼
Read Sensitive Information
```

---

# Methodology

## Step 1 – Discover Redis Service

Scan the target system for Redis.

```bash
nmap -p 6379 -sV <Target-IP>
```

### Purpose

- Detect Redis service
- Identify service version
- Confirm Redis is running

---

## Step 2 – Connect to Redis

Connect using the Redis client.

```bash
redis-cli -h <Target-IP>
```

### Purpose

- Test whether authentication is required
- Establish communication with the Redis server

---

## Step 3 – Enumerate Keys

List every key stored inside Redis.

```redis
KEYS *
```

### Purpose

- Identify stored information
- Discover sensitive configuration data
- Understand application data stored in Redis

---

## Step 4 – Retrieve Sensitive Data

Read the value of a specific key.

```redis
GET Db_password
```

Example:

```
Db_password = RedisDB0123
```

Retrieve another key.

```redis
GET Internal_api_key
```

Example:

```
Internal_api_key = GIR-INT-KEY-98765
```

---

# Findings

The Redis server allowed anonymous connections.

No authentication was required.

Sensitive information was stored in plaintext, including:

- Database Password
- Internal API Key

This demonstrated an insecure Redis deployment that could expose confidential application data.

---

# Security Impact

An attacker could:

- Read application secrets
- Obtain database credentials
- Discover internal API keys
- Perform reconnaissance
- Use exposed credentials to access additional services

This significantly increases the organization's attack surface.

---

# MITRE ATT&CK Mapping

| Technique | Description |
|------------|------------|
| T1046 | Network Service Discovery |
| T1552 | Unsecured Credentials |
| T1592 | Gather Victim Host Information |

---

# SOC Detection Opportunities

A SOC analyst should monitor for:

- Unauthorized Redis connections
- Access from unknown IP addresses
- Enumeration using `KEYS *`
- Excessive `GET` commands
- Redis exposed outside internal networks
- Retrieval of sensitive configuration values

Possible Indicators of Compromise (IOCs):

- Unexpected Redis client connections
- Large numbers of key enumeration requests
- Access to sensitive keys such as passwords or API tokens

---

# Remediation

- Enable Redis authentication.
- Restrict Redis to trusted hosts only.
- Disable external access.
- Store sensitive data securely.
- Use firewall rules to block unauthorized connections.
- Monitor Redis logs for suspicious activity.

---

# Skills Demonstrated

- Network Enumeration
- Redis Enumeration
- Service Discovery
- Security Misconfiguration Assessment
- Information Disclosure Analysis
- Credential Discovery
- Threat Analysis
- SOC Detection Awareness

---

# Screenshots


- Nmap service detection
- <img width="888" height="164" alt="Redis trust abuse 1" src="https://github.com/user-attachments/assets/a5a0b693-c002-4ed7-a384-2ee1a5164146" />

- Redis connection
- - `KEYS *` output
- <img width="688" height="97" alt="Redis trust abuse 2" src="https://github.com/user-attachments/assets/349dd2db-4d50-4d2c-af47-cf1fbddfcc8a" />

- `GET Db_password`
- <img width="399" height="67" alt="Redis trust abuse 3 " src="https://github.com/user-attachments/assets/c6e6fd64-98c3-4d31-8ad1-eec0cfa83ed7" />

- `GET Internal_api_key`
<img width="399" height="67" alt="Redis trust abuse 3 " src="https://github.com/user-attachments/assets/5bb7aba5-cbb7-4566-a37b-0d2403823015" />

<img width="529" height="144" alt="Redis trust abuse 4 " src="https://github.com/user-attachments/assets/f3935154-b191-404b-9551-1fcced008b34" />

---

# Conclusion

This lab demonstrated how an exposed Redis server without authentication can disclose sensitive information such as database passwords and API keys. Proper authentication, access controls, and continuous monitoring are essential to secure Redis deployments against unauthorized access.
