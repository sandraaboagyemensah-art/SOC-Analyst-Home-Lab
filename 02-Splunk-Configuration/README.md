# Project 02 – Splunk Enterprise Configuration and Security Monitoring

## 1. Project Overview

This project focuses on configuring a centralized Security Information and Event Management (SIEM) environment using Splunk Enterprise.

The objective was to establish centralized log collection from multiple virtual machines, configure Splunk Universal Forwarders, verify successful log ingestion, and develop security detections for Windows failed authentication attempts.

The project also demonstrates how to investigate security events, write Splunk Processing Language (SPL) queries, identify repeated failed login attempts, and configure scheduled alerts.

**Project focus:** SIEM administration, log ingestion, Windows security monitoring, threat detection, and alert configuration.

## 2. Lab Environment

The SOC home lab was built using Oracle VirtualBox with a combination of Windows and Linux virtual machines.

| Component | Configuration |
|---|---|
| Virtualization platform | Oracle VirtualBox |
| SIEM platform | Splunk Enterprise |
| Splunk version | 10.6.1.1 |
| Splunk server operating system | Windows Server |
| Splunk server IP | 192.168.56.104 |
| Windows endpoint IP | 192.168.56.105 |
| Kali Linux IP | 192.168.56.103 |
| Splunk receiving port | 9997 |
| Splunk Web interface | http://192.168.56.104:8000 |
| Log forwarding agent | Splunk Universal Forwarder |
| Search language | Splunk Processing Language (SPL) |

### Network Architecture

The lab uses a VirtualBox host-only network to allow communication between the virtual machines.

The Splunk Enterprise server acts as the centralized log collection and analysis platform.

Endpoint machines use Splunk Universal Forwarders to send their log data to the Splunk server.

**Network communication:**

- Windows and Linux endpoints connect to the Splunk server.
- Universal Forwarders send log data to port 9997.
- Splunk Enterprise receives, indexes, and searches the forwarded events.
- Security investigations are performed through Splunk Search & Reporting.

## 3. Project Objectives

The following objectives were completed:

1. Install and verify Splunk Enterprise.
2. Configure Splunk Enterprise to receive forwarded data.
3. Configure Splunk Universal Forwarders on endpoint machines.
4. Verify connectivity between forwarders and the Splunk server.
5. Confirm successful Windows and Linux log ingestion.
6. Enable Windows auditing for successful and failed logon events.
7. Investigate Windows failed authentication events.
8. Develop SPL searches to identify failed login attempts.
9. Create threshold-based detections for repeated failed logins.
10. Configure a scheduled Splunk alert.

## 4. Splunk Enterprise Installation and Configuration

Splunk Enterprise was installed on the Windows Server virtual machine.

The Splunk Web interface was accessed through:

```text
http://192.168.56.104:8000
```

The Splunk service was verified to ensure that the SIEM platform was operational.

**Service verification:**

```powershell
splunk status
```

The Splunk Enterprise service was confirmed to be running.

### Splunk Receiving Configuration

The Splunk server was configured to receive forwarded events on TCP port:

```text
9997
```

This port allows configured Universal Forwarders to transmit log data to Splunk Enterprise.

## 5. Splunk Universal Forwarder Configuration

Splunk Universal Forwarder was used to collect and forward endpoint logs to the centralized Splunk server.

The forwarding destination was configured as:

```text
192.168.56.104:9997
```

### Forwarder Verification

The following command was used to verify the forwarder service:

```powershell
splunk status
```

The forwarding configuration was checked to confirm that endpoint machines were communicating with the Splunk receiving server.

The lab also included verification of active forwarding destinations and successful log ingestion.

## 6. Windows Security Log Collection

Windows Security event logs were collected to monitor authentication activity.

Windows auditing was configured to record successful and failed logon events.

The Windows Security log was used to investigate Event ID:

**4625 – An account failed to log on.**

This event is important in security monitoring because repeated failed authentication attempts may indicate password guessing, brute-force activity, or unauthorized access attempts.

### Windows Failed Login Investigation

A controlled failed login event was generated to test the monitoring environment.

The event was successfully located in Splunk.

The investigation identified the following information:

- Event ID: 4625
- Event category: Windows Security
- Target account: sandra
- Workstation: PERSONALUSE
- Logon Type: 2
- Failure reason: Unknown username or bad password
- Status: 0xC000006D
- Substatus: 0xC000006A
- Source IP: 127.0.0.1

The source IP was identified as the local loopback address. This was a controlled local test and not evidence of an external attacker.

## 7. SPL Detection Queries

Several SPL searches were developed to investigate failed authentication events and identify repeated failures.

### Detection 1 – Basic Windows Failed Login Search

**Purpose:** Identify Windows failed logon events using Event ID 4625.

```spl
index=* earliest=-24h EventCode=4625 LogName=Security
| table _time host sourcetype EventCode LogName Account_Name TargetUserName IpAddress
| sort -_time
```

**Investigation objective:**

- Identify failed Windows logon events.
- Review affected accounts.
- Identify the endpoint generating the event.
- Examine available source IP information.
- Establish a baseline for authentication activity.

### Detection 2 – Failed Login Summary

**Purpose:** Summarize failed login events by host and sourcetype.

```spl
index=* earliest=-24h EventCode=4625 LogName=Security
| stats count AS failed_logins by host sourcetype
| sort - failed_logins
```

**Investigation objective:**

- Count failed login events.
- Identify endpoints generating failed authentication activity.
- Summarize security events for monitoring and reporting.

### Detection 3 – Threshold Validation

**Purpose:** Validate the detection logic by identifying hosts with at least one failed login event within a five-minute time window.

```spl
index=* earliest=-24h sourcetype=WinEventLog:Security EventCode=4625 LogName=Security
| bin _time span=5m
| stats count AS failed_attempts by _time host
| where failed_attempts >= 1
| sort - _time
```

**Investigation objective:**

- Group failed logon events into five-minute intervals.
- Count authentication failures by host.
- Validate threshold-based detection logic.

### Detection 4 – Repeated Failed Login Detection

**Purpose:** Identify five or more failed login events within a five-minute interval.

```spl
index=* earliest=-24h sourcetype=WinEventLog:Security EventCode=4625 LogName=Security
| bin _time span=5m
| stats count AS failed_attempts by _time host
| where failed_attempts >= 5
| sort - _time
```

**Investigation objective:**

- Identify repeated authentication failures.
- Apply a detection threshold.
- Highlight potentially suspicious login activity.
- Support investigation of possible brute-force attempts.

### Detection 5 – Scheduled Alert Search

**Purpose:** Search for five or more failed Windows logon events within the previous five minutes.

```spl
index=* earliest=-5m latest=now sourcetype=WinEventLog:Security EventCode=4625 LogName=Security
| stats count AS failed_attempts by host
| where failed_attempts >= 5
| sort - failed_attempts
```

This search was used to create a scheduled Splunk alert.

## 8. Splunk Alert Configuration

A scheduled alert was created to detect repeated Windows failed login attempts.

**Alert name:**

Windows Repeated Failed Logins

### Alert Settings

| Configuration | Value |
|---|---|
| Alert type | Scheduled |
| Search frequency | Every 1 minute |
| Search time range | Last 5 minutes |
| Detection threshold | 5 or more failed login events |
| Trigger condition | Number of Results greater than 0 |
| Trigger frequency | Once per search |
| Email notification | Not configured |

### Alert Objective

The alert automates the detection process by periodically executing the SPL search.

When the search identifies a host with five or more failed logon events within the configured time window, the alert condition is met.

This demonstrates the use of scheduled security monitoring rather than relying exclusively on manual searches.

## 9. Evidence and Screenshots

Screenshots documenting the configuration, investigation, detection logic, and alert setup are stored in:

```text
detection-screenshots/
```

### Windows Failed Authentication Investigation

| Screenshot | Description |
|---|---|
| `windows-failed-logon-event-4625.png` | Individual Windows Event ID 4625 failed logon event |
| `windows-failed-logon-detection-summary.png` | Summary of failed login events by host and sourcetype |
| `windows-failed-logon-threshold-validation.png` | Validation of failed login detection thresholds |
| `windows-repeated-failed-login-detection.png` | Detection of repeated failed authentication attempts |
| `windows-repeated-failed-login-alert.png` | Scheduled Splunk alert configuration |

Additional screenshots documenting Splunk installation, forwarder status, network configuration, and successful log ingestion are retained in the project documentation where applicable.

## 10. Skills Demonstrated

### SIEM Administration
- Splunk Enterprise installation and verification.
- Splunk receiving configuration.
- Universal Forwarder configuration.
- Centralized log collection.
- Log ingestion troubleshooting.

### Windows Security Monitoring
- Windows Security event log analysis.
- Event ID 4625 investigation.
- Authentication failure investigation.
- Windows audit policy configuration.

### Detection Engineering
- SPL query development.
- Event filtering and aggregation.
- Time-based event correlation.
- Threshold-based detection.
- Repeated failed login identification.

### Security Operations
- Security event investigation.
- Identification of potentially suspicious authentication activity.
- Scheduled alert configuration.
- Security monitoring and documentation.

## 11. Project Outcome

Successfully established a centralized Splunk Enterprise monitoring environment and configured Universal Forwarders to collect endpoint security logs.

Developed and tested SPL searches to identify Windows failed authentication events, summarize login failures, validate detection thresholds, and identify repeated failed login activity.

Configured a scheduled Splunk alert to automate the detection of repeated failed authentication attempts.

This project demonstrates practical SIEM administration, endpoint log collection, security event investigation, detection engineering, and alert configuration skills relevant to an entry-level Security Operations Centre (SOC) Analyst role.

## 12. Future Improvements

Potential improvements to this lab include:

- Developing source-IP-based failed login investigations.
- Creating account-specific authentication detections.
- Building dashboards for Windows authentication activity.
- Investigating successful logons following repeated failures.
- Adding additional Linux authentication detections.
- Correlating Windows and Linux security events.
- Expanding alerting and incident investigation workflows.

---

**Portfolio:** SOC Analyst Home Lab  
**Project:** 02 – Splunk Enterprise Configuration and Security Monitoring  
**Platform:** Splunk Enterprise | Windows Server | Windows Endpoints | Kali Linux | VirtualBox
