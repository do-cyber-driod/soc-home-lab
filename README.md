# soc-home-lab
Home SOC environment built on Ubuntu — Splunk SIEM deployment, SSH brute force simulation using Hydra, real-time attack detection and dashboard visualisation.

# SOC Home Lab - Brute Force Detection with Splunk

## Overview
A home Security Operations Centre (SOC) environment built on Ubuntu, 
using Splunk SIEM to ingest system logs, simulate a real brute force 
attack, and detect it through custom SPL queries and a live dashboard.

## Objectives
- Configure Splunk to ingest and monitor Linux authentication logs
- Simulate a real SSH brute force attack using Hydra
- Detect and analyse the attack using Splunk Processing Language (SPL)
- Build a SOC dashboard to visualise attack patterns in real time

## Tools & Technologies
- **OS:** Ubuntu Linux
- **SIEM:** Splunk Enterprise (Free Trial)
- **Attack Tool:** Hydra v9.6
- **Log Sources:** /var/log/auth.log, /var/log/syslog
- **Protocol Targeted:** SSH (Port 22)

## Lab Setup
- Splunk installed locally on Ubuntu machine
- Authentication logs ingested via Splunk Data Inputs
- OpenSSH server configured as the target service
- Hydra used to simulate brute force from localhost (127.0.0.1)

## Attack Simulation
Simulated a brute force attack against the local SSH service using 
Hydra with the rockyou.txt wordlist targeting the local user account.

Command used:hydra -l harvey -P ~/rockyou.txt -t 4 ssh://127.0.0.1

This generated 127 failed authentication attempts recorded in 
/var/log/auth.log within minutes.

## Detection - SPL Query
The following Splunk query was used to detect and summarise the attack:
index=main sourcetype="Authorization Log" "Failed password"
| rex "Failed password for (?<user>\S+) from (?<src_ip>\S+)"
| stats count by src_ip, user
| sort -count

### Detection Result
| src_ip    | user   | count |
|-----------|--------|-------|
| 127.0.0.1 | harvey | 127   |

127 failed login attempts from a single IP in a short timeframe —
a clear indicator of a brute force attack.

## Attack Timeline Query
index=main sourcetype="Authorization Log" "Failed password"
| rex "Failed password for (?<user>\S+) from (?<src_ip>\S+)"
| timechart count span=1m

This produced a timeline chart showing a sudden spike in failed 
logins — consistent with automated brute force activity.

## Dashboard
Built a Splunk dashboard called "SOC Home Lab" containing:
- **Panel 1:** Brute Force Detection table (src_ip, user, count)
- **Panel 2:** Attack Timeline showing failed logins per minute

## Screenshots
### Brute Force Detection Table
![Brute Force Detection](<./Auth logs.png>)

### Attack Timeline Dashboard
![Attack Timeline](<add screenshot here>)

### Live Auth Log Events in Splunk
![Auth Logs](<add screenshot here>)

## Key Findings
- Hydra generated 127 failed SSH authentication attempts in under 
  2 minutes
- All attempts originated from 127.0.0.1 targeting user "harvey"
- Splunk successfully detected the attack pattern through log 
  correlation
- Attack spike was clearly visible on the timeline dashboard

## What I Learned
- How to configure Splunk data inputs for Linux system logs
- How brute force attacks appear in authentication logs
- Writing SPL queries to extract fields and detect attack patterns
- Building real-time SOC dashboards for threat visualisation
- How a SOC analyst would triage and escalate this type of incident

## Certifications
- CompTIA Security+ (May 2026)
