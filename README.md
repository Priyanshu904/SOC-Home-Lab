# SOC Home Lab

A hands-on security learning project where I set up a SIEM (Security Information and Event Management) system, monitored it for alerts, and simulated attacks to understand how security tools detect threats. This is a personal learning lab, not production-level work.

## Project Overview

This lab demonstrates:
- **SIEM Deployment**: Single-node Wazuh 4.7.3 setup on Linux using Docker
- **Agent Monitoring**: Wazuh agent collecting security events from a Linux host
- **Alert Detection**: 285+ security events captured and analyzed
- **Compliance Scanning**: Automatic SCA scans against CIS benchmarks
- **Attack Simulation**: Three different attack scenarios to understand alert patterns

## Architecture

```
┌─────────────────────────────────────────┐
│        Wazuh Manager (Docker)           │
│  Single-node deployment on Linux Mint   │
│         127.0.0.1:1515 (API)            │
└─────────────────────────────────────────┘
              ↑
              │ (Agent communication)
              │
┌─────────────────────────────────────────┐
│    Wazuh Agent (linuxmint-host)         │
│      IP: 192.168.1.12                   │
│  Collecting logs & system metrics       │
└─────────────────────────────────────────┘

Attack Vector:
┌──────────────────────────────────────┐
│  Kali Linux (Docker Container)       │
│  Running: Nmap, Hydra, SSH brute     │
└──────────────────────────────────────┘
```

## Setup Steps

1. **Deployed Wazuh 4.7.3** using Docker on Linux Mint
2. **Installed Wazuh Agent** on the host machine
   - Agent name: `linuxmint-host`
   - Agent IP: `192.168.1.12`
3. **Configured Agent** to forward system logs and security events
4. **Enabled SCA** for automatic CIS benchmark compliance scanning
5. **Verified Communication** between manager and agent

## Attacks Simulated & Detection

| Attack | Tool | Description | Rules Fired | Alert Level |
|--------|------|-------------|-------------|-------------|
| Port Scan | Nmap | `nmap -sS -sV -O` scan against host | Rule 533 | 7 |
| Manual SSH Brute Force | SSH | 10 login attempts with invalid username | Rules 5710, 5712 | 5, 10 |
| Automated SSH Brute Force | Hydra | 10 password attempts against root account | Rules 2582, 5760 | 10, 5 |

**MITRE ATT&CK Mapping**: Hydra attack automatically mapped to **T1110** (Brute Force) and **T1110.001** (Password Guessing)

## Key Findings

- **Alert Sensitivity**: Wazuh detected all three attack types, including the first invalid SSH attempt. Rules fired within seconds of the attack.
- **Rule Levels**: High-level alerts (level 10) were triggered for brute force attacks, while port scans triggered moderate alerts (level 7).
- **Automatic Correlation**: The system automatically correlated multiple failed login attempts into a single brute force attack pattern.
- **SCA Value**: Compliance scanning ran automatically and identified areas where the host deviated from CIS benchmarks—useful for hardening.

## Screenshots

### Wazuh Dashboard - Alert Overview
[screenshot1: Main dashboard showing 285 hits and attack timeline]

### SSH Brute Force Detection
[screenshot2: Alert table with rules 5710/5712 for SSH attempts]

### Nmap Port Scan Alert
[screenshot3: Rule 533 fired with port scan details]

### Automated Attack Output (Hydra)
[screenshot4: Terminal showing Hydra execution and password attempts]

## Tools & Technologies Used

- **Wazuh 4.7.3** — SIEM platform for log aggregation and threat detection
- **Docker** — Containerization for Wazuh deployment
- **Linux Mint** — Host OS and test environment
- **Kali Linux** — Container with attack tools
- **Nmap** — Network reconnaissance tool
- **Hydra** — Brute force attack tool
- **OpenSSH** — SSH server for testing

## References

- [Wazuh Documentation](https://documentation.wazuh.com/)
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks/)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)
- [Nmap User Guide](https://nmap.org/book/)

---

**Purpose**: This lab was built as a learning exercise to understand how SIEM systems work and how they detect common attack patterns. It helped me see the connection between offensive techniques and defensive detection in practice.
