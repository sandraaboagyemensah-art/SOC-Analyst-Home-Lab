# Project 01 – Splunk Configuration & Log Ingestion

## 1. Project Overview

This section documents the configuration and validation of Splunk Enterprise and Splunk Universal Forwarders within a multi-endpoint SOC home lab.

The objective was to establish centralized log collection from Windows and Linux endpoints, forward security-related events to a central Splunk server, and verify successful event ingestion through Splunk Search.

The lab demonstrates a functioning SIEM environment capable of collecting and searching events from multiple operating systems.

## 2. Lab Environment

| Component | Operating System | Hostname | IP Address | Role |
|---|---|---|---|---|
| Splunk Server | Windows Server | Splunk Server | `192.168.56.104` | Central SIEM |
| Windows VM 1 | Windows | `WIN-SVR-ACTIVE` | `192.168.56.102` | Windows event collection |
| Windows VM 2 | Windows | `personaluse` | `192.168.56.105` | Windows event collection |
| Kali Linux | Kali Linux | `kali` | `192.168.56.103` | Linux log collection |

*Verify the Windows VM IP-to-hostname assignments before publishing this inventory. The Windows endpoint IPs were previously reported separately, so they should not be swapped based solely on their order in this table.*

**Network:** VirtualBox Host-Only Network

**Splunk Receiving Port:** TCP `9997`

## 3. Splunk Server Configuration

Splunk Enterprise was configured to receive forwarded events from the endpoint machines.

Configuration and verification included:

- Splunk Enterprise service running.
- TCP port `9997` configured for receiving forwarded events.
- Windows Firewall inbound rule created for the Splunk receiving port.
- Universal Forwarders configured to send data to `192.168.56.104:9997`.

## 4. Universal Forwarder Status

| Endpoint | Forwarder | Destination | Status |
|---|---|---|---|
| Windows VM 1 | Splunk Universal Forwarder | `192.168.56.104:9997` | Successful log ingestion |
| Windows VM 2 | Splunk Universal Forwarder | `192.168.56.104:9997` | Successful log ingestion |
| Kali Linux | Splunk Universal Forwarder | `192.168.56.104:9997` | Successful log ingestion |

All three endpoints have demonstrated successful event delivery and indexing in Splunk.

## 5. Windows Event Log Configuration

Both Windows endpoints were configured to collect the following Windows Event Logs:

- `WinEventLog:Application`
- `WinEventLog:Security`
- `WinEventLog:System`

The input configuration was validated using Splunk's `btool` command.

Example configuration:

```ini
[default]
host = WIN-SVR-ACTIVE

[WinEventLog://Application]
disabled = 0

[WinEventLog://Security]
disabled = 0

[WinEventLog://System]
disabled = 0
```

Successful ingestion was verified through Splunk Search.

Example SPL query:

```spl
index=* earliest=-24h
| stats count by host sourcetype
| sort host
```

The search confirmed Windows Application, Security, and System event data from both Windows endpoints.

## 6. Kali Linux Log Configuration

Kali Linux was configured to forward Linux system activity to Splunk.

This Kali installation uses `systemd-journald` rather than traditional `/var/log/auth.log` and `/var/log/syslog` files.

Journal entries were exported into a readable text file:

`/var/log/kali-journal.log`

The Splunk Universal Forwarder was configured to monitor this file.

Example configuration:

```ini
[monitor:///var/log/kali-journal.log]
disabled = 0
host = kali
index = default
sourcetype = linux:journal
```

The monitored-file configuration was verified using:

```bash
sudo /opt/splunkforwarder/bin/splunk list monitor
```

The forwarding destination was verified using:

```bash
sudo /opt/splunkforwarder/bin/splunk list forward-server
```

Confirmed forwarding destination:

```text
Active forwards:
    192.168.56.104:9997
```

## 7. Successful Log Ingestion

Successful event ingestion was confirmed through Splunk Search.

### Windows Endpoints

Search query:

```spl
index=* earliest=-24h
| stats count by host sourcetype
| sort host
```

Verified Windows sourcetypes:

- `WinEventLog:Application`
- `WinEventLog:Security`
- `WinEventLog:System`

### Kali Linux Endpoint

Search query:

```spl
index=* host=kali earliest=-24h
| stats count by sourcetype
| sort sourcetype
```

Kali log sources included:

- `kali-journal-too_small`
- `linux_bootlog`
- `Xorg`
- `lightdm-too_small`
- `macchanger-too_small`
- `seat0-greeter-too_small`
- `unknown-too_small`

The following query was also used to verify the journal export:

```spl
index=* host=kali source="/var/log/kali-journal.log"
```

**Result:** Events from both Windows endpoints and Kali Linux were successfully received, indexed, and made searchable in Splunk.

## 8. Evidence & Screenshots

Configuration and ingestion evidence is maintained in:

`forwarder-status-screenshots/`

Evidence includes:

| Screenshot | Description |
|---|---|
| Windows VM 1 forwarder status | Forwarder connection verification |
| Windows VM 2 forwarder status | Forwarder connection verification |
| Windows event ingestion | Application, Security, and System event collection |
| Kali forwarder status | Active forwarding to the Splunk server |
| Kali host log ingestion and sourcetypes | Linux event ingestion and sourcetype identification |
| Three-host ingestion summary | Centralized event collection across all endpoints |

## 9. Project Status

**Completed:**

- Splunk Enterprise receiving configuration.
- Splunk receiving port `9997` verification.
- Windows VM 1 Universal Forwarder configuration.
- Windows VM 2 Universal Forwarder configuration.
- Kali Linux Universal Forwarder configuration.
- Windows Application, Security, and System log collection.
- Kali Linux journal export and monitoring.
- Successful event ingestion from all three endpoints.
- Sourcetype identification and event verification through SPL searches.
- GitHub documentation and screenshot evidence.

**Next Phase:**

- Develop Windows failed-login detection searches.
- Investigate suspicious authentication activity.
- Create Splunk alerts.
- Build practical SOC investigation use cases.
- Document detection results and incident investigations.

## 10. Technical Skills Demonstrated

- SIEM deployment and configuration.
- Splunk Enterprise administration.
- Splunk Universal Forwarder deployment.
- Windows Event Log collection.
- Linux system log collection.
- systemd-journald troubleshooting.
- Network connectivity troubleshooting.
- Log source and sourcetype identification.
- SPL (Search Processing Language).
- Centralized security event monitoring.
- Technical documentation and evidence collection.

---

**Project:** SOC Analyst Home Lab  
**SIEM:** Splunk Enterprise  
**Forwarder:** Splunk Universal Forwarder  
**Virtualization:** Oracle VirtualBox  
**Operating Systems:** Windows Server, Windows, Kali Linux  
**Network:** VirtualBox Host-Only Network
