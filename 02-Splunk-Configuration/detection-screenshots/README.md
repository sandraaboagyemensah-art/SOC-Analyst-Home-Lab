# Windows Failed Login Detection – Splunk

## 1. Project Overview

This project demonstrates how to monitor Windows authentication activity using Splunk Enterprise, identify failed login attempts, investigate Windows Security Event ID **4625**, and configure a detection alert for repeated failed login attempts.

The objective was to demonstrate foundational Security Operations Center (SOC) skills, including Windows event log analysis, security monitoring, SPL queries, threshold-based detection, and alert configuration.

## 2. Lab Environment

| Component | Description |
|---|---|
| SIEM | Splunk Enterprise |
| Log Source | Windows Security Event Log |
| Event ID | 4625 – Failed Logon |
| Log Sourcetype | `WinEventLog:Security` |
| Detection Platform | Splunk Search & Reporting |
| Detection Window | 5 minutes |
| Alert Threshold | 5 or more failed login attempts |

## 3. Windows Audit Policy

Windows auditing was verified using:

```cmd
auditpol /get /subcategory:"Logon"
```

The resulting configuration confirmed:

```text
Logon    Success and Failure
```

This enabled Windows to record both successful and unsuccessful logon attempts.

## 4. Detection Screenshots

The following screenshots document the detection workflow.

### Screenshot 1 – Basic Failed Login Search

**Filename:** `windows-failed-logon-event-4625.png`

Demonstrates the identification of failed Windows authentication events using Event ID 4625.

**SPL Query:**

```spl
index=* earliest=-24h EventCode=4625 LogName=Security
| table _time host sourcetype EventCode LogName Account_Name TargetUserName IpAddress
| sort -_time
```

**Purpose:**
- Identify failed Windows logins.
- Review timestamps and affected hosts.
- Examine account and IP address information.
- Investigate authentication failures.

### Screenshot 2 – Failed Login Summary

**Filename:** `windows-failed-logon-detection-summary.png`

Demonstrates how Splunk aggregates failed login events by host and sourcetype.

**SPL Query:**

```spl
index=* earliest=-24h EventCode=4625 LogName=Security
| stats count AS failed_logins by host sourcetype
| sort - failed_logins
```

**Purpose:**
- Count failed authentication events.
- Summarize activity by host.
- Identify systems generating failed login events.

### Screenshot 3 – Threshold Validation

**Filename:** `windows-failed-logon-threshold-validation.png`

Demonstrates how failed login events can be grouped into five-minute intervals and evaluated against a detection threshold.

**SPL Query:**

```spl
index=* earliest=-24h sourcetype=WinEventLog:Security EventCode=4625 LogName=Security
| bin _time span=5m
| stats count AS failed_attempts by _time host
| where failed_attempts >= 1
| sort - _time
```

**Purpose:**
- Group failed login attempts into five-minute windows.
- Count failed attempts by host.
- Validate that the threshold-based detection logic works.

### Screenshot 4 – Repeated Failed Login Detection

**Filename:** `windows-repeated-failed-login-detection.png`

Demonstrates detection logic for repeated failed login attempts.

**SPL Query:**

```spl
index=* earliest=-24h sourcetype=WinEventLog:Security EventCode=4625 LogName=Security
| bin _time span=5m
| stats count AS failed_attempts by _time host
| where failed_attempts >= 5
| sort - _time
```

**Purpose:**
- Identify hosts with five or more failed login attempts within a five-minute period.
- Demonstrate threshold-based security monitoring.
- Establish a foundation for detecting potential brute-force activity.

A search returning no results indicates that the configured threshold was not met during the selected time range. This is a valid detection outcome.

### Screenshot 5 – Scheduled Failed Login Alert

**Filename:** `windows-repeated-failed-login-alert.png`

Demonstrates the configuration of a scheduled Splunk alert for repeated Windows authentication failures.

**SPL Query:**

```spl
index=* earliest=-5m latest=now sourcetype=WinEventLog:Security EventCode=4625 LogName=Security
| stats count AS failed_attempts by host
| where failed_attempts >= 5
| sort - failed_attempts
```

**Alert Configuration:**

| Setting | Configuration |
|---|---|
| Alert Name | Windows Repeated Failed Logins |
| Alert Type | Scheduled |
| Schedule | Every 1 minute |
| Time Range | Last 5 minutes |
| Trigger Condition | Number of Results greater than 0 |
| Trigger | Once per search |

**Purpose:**
- Automate monitoring for repeated failed logins.
- Identify hosts exceeding the configured threshold.
- Demonstrate basic security alert configuration in Splunk.

The alert is configured to detect qualifying events. Email notifications or other notification actions require separate configuration.

## 5. Event Investigation

A failed authentication event was successfully identified and investigated.

| Field | Observed Value |
|---|---|
| Event ID | 4625 |
| Target Account | sandra |
| Workstation | PERSONALUSE |
| Logon Type | 2 – Interactive |
| Failure Reason | Unknown username or bad password |
| Status | `0xC000006D` |
| Substatus | `0xC000006A` |
| Source IP | `127.0.0.1` |

The substatus indicated an incorrect password. The source address was the local loopback address, so this particular event should not be interpreted as evidence of an external attacker.

## 6. Skills Demonstrated

- Windows Security Event Log monitoring.
- Windows audit policy verification.
- Splunk Enterprise configuration and log analysis.
- Security Information and Event Management (SIEM).
- Search Processing Language (SPL).
- Failed authentication investigation.
- Event aggregation and threshold-based detection.
- Scheduled alert configuration.
- Basic security event triage.
- Security documentation and GitHub portfolio development.

## 7. Future Improvements

Potential enhancements include:

- Monitoring failed logins across multiple Windows endpoints.
- Investigating successful logins following repeated failures.
- Adding account-level and source-IP-level detection.
- Developing additional authentication anomaly detections.
- Improving alert suppression and notification settings.
- Building dashboards to visualize authentication activity.

## 8. Conclusion

This project demonstrates a practical Windows authentication monitoring workflow using Splunk Enterprise.

The lab successfully collected and analyzed Windows failed-login events, implemented SPL-based detection logic, evaluated repeated authentication failures against a threshold, and configured a scheduled alert.

These exercises demonstrate foundational SOC Analyst capabilities in security monitoring, event investigation, detection engineering, and alert management.
