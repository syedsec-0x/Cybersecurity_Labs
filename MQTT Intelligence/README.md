#  MQTT Intelligence

## Overview

This lab demonstrates MQTT service enumeration and intelligence gathering against an exposed MQTT broker. The objective was to determine whether the broker allowed unauthenticated subscriptions and exposed operational information through MQTT topics.

## Lab Environment

| Component        | Description |
| ---------------- | ----------- |
| Attacker Machine | Gir forge   |
| Target Machine   | GIR-Citadel |
| Protocol         | MQTT        |
| Default Port     | 1883        |

#NOTE : -  Make sure you have set both the Machines network adapter to host only mode.


---

## Objective

* Identify MQTT services running on the target.
* Enumerate accessible MQTT topics.
* Determine whether anonymous subscriptions are permitted.
* Collect operational intelligence exposed by the broker.
* Analyze security risks associated with insecure MQTT configurations

---

## Background

MQTT (Message Queuing Telemetry Transport) is a lightweight publish/subscribe messaging protocol commonly used in:

* IoT devices
* Smart homes
* Industrial automation
* Monitoring systems
* Telemetry platforms

A central MQTT broker handles communication between publishers and subscribers.

---

## Methodology

### Step 1 – Identify MQTT Service

Scan the target for MQTT services.

```bash
nmap -p 1883 -sV <target-ip>     ex:       nmap -p 1883 -sV 192.168.56.101
```
<img width="819" height="159" alt="df46" src="https://github.com/user-attachments/assets/60d342aa-881e-4cbe-8a3a-a531e42b2bf1" />

Purpose:

* Detect MQTT broker
* Identify service version
* Confirm MQTT availability

---

### Step 2 – Subscribe to All Topics

Attempt to subscribe to all available topics.

```bash
mosquitto_sub -h <target-ip> -t '#' -d      ex-        mosquitto_sub -h 192.168.56.101 -t '#' -d
```
Explanation:

* `mosquitto_sub` = MQTT subscriber client
* `-h` = target host
* `-t '#'` = subscribe to all topics
* `-d` = debug output

Purpose:

* Monitor all MQTT traffic
* Identify exposed messages
* Test anonymous access
* 
<img width="595" height="230" alt="VirtualBox_GIR_FORGE_05_06_2026_15_14_46" src="https://github.com/user-attachments/assets/887cfbb7-3173-4288-bfae-1aef0911dc6a" />

---

### Step 3 – Enumerate Broker System Topics

Query internal MQTT broker topics.

```bash
mosquitto_sub -h <target-ip> -t '$SYS/#' -v    ex =         mosquitto_sub -h 192.168.56.101 -t '$SYS/#' -v
```
<img width="732" height="76" alt="image" src="https://github.com/user-attachments/assets/fe1fadc4-9a32-413b-8011-72432551848b" />

Purpose:

* Gather broker statistics
* Identify uptime information
* View client activity
* Enumerate broker operational data

<img width="1920" height="1020" alt="Screenshot 2026-05-28 160545" src="https://github.com/user-attachments/assets/cf3c4a97-801d-41f5-aad3-f0510ef98223" />


---

## Findings

The MQTT broker allowed subscriptions without authentication.

The broker exposed:

* Operational telemetry
* Internal topic information
* Broker statistics
* System status information

This indicates weak access controls and unnecessary information disclosure.

---

## Security Impact

An attacker may be able to:

* Monitor device communications
* Gather infrastructure intelligence
* Observe operational activity
* Map connected systems
* Collect information for future attacks

---

## MITRE ATT&CK Mapping

| Technique | Description                       |
| --------- | --------------------------------- |
| T1046     | Network Service Discovery         |
| T1590     | Gather Victim Network Information |
| T1040     | Network Traffic Collection        |

---

## Detection Opportunities

SOC teams should monitor:

* Anonymous MQTT subscriptions
* Excessive topic enumeration
* Unknown MQTT clients
* Repeated subscriptions to `$SYS/#`
* Unusual MQTT traffic patterns

---

## Remediation

● Restrict $SYS/# topics to administrative users only. 
● Enforce authentication and ACLs for all topics. 
● Enable TLS (port 8883) for encrypted communication.
● Disable anonymous MQTT access. 


---

## Skills Demonstrated

* Network Enumeration
* Service Discovery
* MQTT Security Assessment
* Protocol Analysis
* Security Misconfiguration Identification
* Threat Modeling
* SOC Detection Awareness

---

## Conclusion

This lab demonstrated how improperly secured MQTT brokers can expose sensitive operational intelligence. Anonymous subscriptions and unrestricted access to system topics increase the attack surface and may assist attackers during reconnaissance activities.
