# Windows Failed Login Detection — Event ID 4625

## Project Overview

This project demonstrates the detection and investigation of failed Windows authentication attempts using **Splunk Enterprise 10.6.1.1**.

The objective was to generate a controlled failed-login event on a Windows endpoint, verify that Windows recorded the event, confirm that Splunk successfully received it through the Universal Forwarder, and investigate the event using Search Processing Language (SPL).

This exercise demonstrates foundational Security Operations Center (SOC) analyst skills, including Windows event auditing, SIEM log analysis, event filtering, and authentication investigation.

## Lab Environment

| Component | Configuration |
|---|---|
| SIEM platform | Splunk Enterprise 10.6.1.1 |
| Endpoint operating system | Windows |
| Log collection | Splunk Universal Forwarder |
| Windows log source | Security Event Log |
| Event ID | 4625 |
| Detection method | SPL search |
| Analysis period | Last 24 hours |

## Detection Objective

Identify failed Windows login attempts by monitoring Security Event ID **4625**.

Event ID 4625 is generated when a logon request fails. It can help analysts investigate:

- Incorrect password attempts.
- Attempts to access unauthorized accounts.
- Repeated authentication failures.
- Potential password-guessing or brute-force activity.

A single failed login does not establish malicious activity. Analysts should investigate the account, source, frequency, timing, and surrounding events before determining whether an incident has occurred.

## Windows Event Generation and Verification

A controlled incorrect-password attempt was performed on the Windows endpoint.

The Windows audit policy was verified using:

```cmd
auditpol /get /subcategory:"Logon"
```

The result confirmed:

```text
Logon    Success and Failure
```

The Security event was then retrieved using PowerShell:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Security'
    Id = 4625
    StartTime = (Get-Date).AddHours(-24)
} -MaxEvents 10 |
Format-List TimeCreated, Id, ProviderName, Message
```

### Verified Event Details

| Field | Observed value |
|---|---|
| Event ID | 4625 |
| Provider | Microsoft-Windows-Security-Auditing |
| Logon Type | 2 — Interactive |
| Failure reason | Unknown username or bad password |
| Status | `0xC000006D` |
| Substatus | `0xC000006A` — Incorrect password |
| Workstation | PERSONALUSE |
| Source address | `127.0.0.1` |

The event indicates a failed interactive logon involving an incorrect password. The loopback address identifies the local computer, not an external attacker.

## Splunk Log Ingestion Verification

The Windows Security event was successfully forwarded to Splunk and located using Search & Reporting.

### Primary Detection Search

```spl
index=* earliest=-24h EventCode=4625 LogName=Security
| table _time host sourcetype EventCode LogName Account_Name TargetUserName IpAddress
| sort - _time
```

This search filters for Event ID 4625 specifically within the Windows Security log.

### Detection Summary Search

```spl
index=* earliest=-24h EventCode=4625 LogName=Security
| stats count AS failed_logins by host sourcetype
| sort - failed_logins
```

The summary search returned **one verified failed-login event** in the 24-hour search window.

## Event Classification and Investigation

During investigation, an additional result appeared under the Application sourcetype with EventCode 4625.

Inspection of its raw event data showed:

- Log name: Application
- Source: Microsoft-Windows-EventSystem
- Event type: Information
- Message: Windows EventSystem was suppressing duplicate event log entries for 86,400 seconds.

This was not a second failed authentication event.

The investigation demonstrated why analysts should validate the original event source and message instead of relying exclusively on an event-code match.

## Screenshots and Evidence

The following screenshots document the investigation:

| Screenshot | Evidence |
|---|---|
| `windows-failed-logon-event-4625.png` | Windows failed-login event details |
| `windows-failed-logon-detection-summary.png` | Splunk detection summary showing the verified event |

## Skills Demonstrated

- Windows Security auditing and Event Viewer investigation.
- Event ID 4625 identification and interpretation.
- Splunk Universal Forwarder log ingestion verification.
- SPL search construction and filtering.
- Event classification and false-positive investigation.
- Authentication failure analysis.
- Technical documentation and evidence collection.

## Project Status and Next Steps

**Completed:** Controlled failed-login event generation, Windows event verification, Splunk ingestion verification, SPL filtering, and detection summary.

**Planned enhancements:**

1. Develop a threshold-based detection for repeated failed logins.
2. Configure a Splunk alert for suspicious authentication activity.
3. Investigate successful logins following repeated failures.
4. Build a dashboard for Windows authentication monitoring.

## Conclusion

This project demonstrates an end-to-end Windows authentication monitoring workflow using Splunk Enterprise.

It shows how a SOC analyst can generate a security event, validate its origin, confirm SIEM ingestion, write targeted SPL searches, and distinguish genuine authentication failures from unrelated system messages.

The lab provides a foundation for developing more advanced detection and incident investigation capabilities.
