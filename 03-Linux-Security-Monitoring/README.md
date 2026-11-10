# Project 03 – Linux Security Monitoring with Splunk

## Project Overview

This project demonstrates how to collect, forward, investigate, and monitor Linux security events using Splunk Enterprise and the Splunk Universal Forwarder.

A Kali Linux virtual machine was configured to forward authentication logs to a centralized Splunk server. Security events were analyzed to identify failed authentication attempts and detect repeated failures.

## Lab Environment

| Component | Configuration |
|---|---|
| SIEM | Splunk Enterprise 10.6.1.1 |
| Linux endpoint | Kali Linux 2026.3 |
| Splunk server IP | 192.168.56.104 |
| Kali Linux IP | 192.168.56.103 |
| Splunk receiving port | 9997 |
| Log source | /var/log/auth.log |
| Splunk index | main |
| Sourcetype | linux:auth |

## 1. Linux Log Collection and Forwarding

Installed and enabled rsyslog on Kali Linux.

Verified that the authentication log was created:

```bash
ls -l /var/log/auth.log
sudo tail -n 20 /var/log/auth.log
```

Configured the Splunk Universal Forwarder to monitor Linux authentication events.

**inputs.conf configuration:**

```ini
[monitor:///var/log/auth.log]
disabled = 0
index = main
sourcetype = linux:auth
host = kali
```

Configured forwarding to the Splunk server:

```ini
[tcpout]
defaultGroup = default-autolb-group

[tcpout:default-autolb-group]
server = 192.168.56.104:9997
```

Verified that the forwarder was running and the destination was active.

## 2. Authentication Log Ingestion Verification

Verified successful ingestion of Kali authentication events in Splunk.

**SPL query:**

```spl
index=main host=kali sourcetype=linux:auth earliest=-24h
| sort -_time
| table _time host sourcetype source _raw
```

Confirmed that events were received from:

- Host: kali
- Source: /var/log/auth.log
- Index: main
- Sourcetype: linux:auth

## 3. Linux Failed Authentication Investigation

Generated a controlled failed sudo authentication attempt on Kali Linux.

Reviewed the resulting authentication records and identified:

- Failed password verification.
- PAM authentication failure.
- User associated with the failed attempt.
- Timestamp and originating host.

**SPL query – Failed authentication event:**

```spl
index=main host=kali sourcetype=linux:auth earliest=-24h
"authentication failure"
| table _time host sourcetype source _raw
| sort -_time
```

**SPL query – Failed authentication summary:**

```spl
index=main host=kali sourcetype=linux:auth earliest=-24h
"authentication failure"
| stats count AS failed_authentication_events by host
| where failed_authentication_events >= 1
| sort - failed_authentication_events
```

## 4. Repeated Failed Authentication Detection

Generated multiple controlled failed authentication attempts to test detection logic.

Created a Splunk search to identify three or more failed authentication log events within a five-minute window.

**SPL detection query:**

```spl
index=main host=kali sourcetype=linux:auth earliest=-24h
"authentication failure"
| bin _time span=5m
| stats count AS failed_attempts by _time host
| where failed_attempts >= 3
| sort -_time
```

## 5. Automated Splunk Alert

Created a scheduled Splunk alert named:

**Linux Repeated Failed Authentication**

Alert configuration:

| Setting | Configuration |
|---|---|
| Alert type | Scheduled |
| Search interval | Every 1 minute |
| Search window | Last 5 minutes |
| Detection threshold | 3 or more events |
| Trigger condition | Number of results greater than 0 |
| Trigger frequency | Once per search |

The alert monitors Kali authentication events for repeated failures.

## 6. Evidence and Screenshots

Screenshots are stored in `detection-screenshots/`.

| Screenshot | Description |
|---|---|
| `kali-linux-failed-authentication-event.png` | Individual failed authentication event |
| `kali-linux-failed-authentication-summary.png` | Failed authentication summary |
| `kali-linux-repeated-failed-authentication-detection.png` | Repeated failed authentication detection |
| `kali-linux-repeated-failed-authentication-alert.png` | Scheduled Splunk alert configuration |

## 7. Skills Demonstrated

- Linux system administration
- Linux authentication log analysis
- rsyslog configuration
- Splunk Universal Forwarder configuration
- Centralized log collection
- SPL search and filtering
- Security event investigation
- Failed-login detection
- Threshold-based detection logic
- Scheduled alert configuration
- SIEM monitoring and troubleshooting

## Project Outcome

Successfully configured Kali Linux to forward authentication logs to Splunk Enterprise.

Demonstrated the ability to investigate Linux authentication failures, identify repeated failed authentication events, and configure a scheduled alert for automated security monitoring.

This project forms part of my hands-on SOC Analyst Home Lab portfolio.
