# Project 01 – SOC Analyst Home Lab Architecture

## 1. Project Overview

This project documents the design and initial configuration of a Security Operations Centre (SOC) Analyst Home Lab using Oracle VirtualBox and a combination of Windows and Linux virtual machines.

The purpose of this environment is to simulate a small enterprise network where security logs can be collected from multiple endpoints and forwarded to a centralized Splunk server for monitoring, investigation, and threat detection.

This lab provides the foundation for hands-on cybersecurity training and practical SOC analyst exercises.

## 2. Project Objectives

The objectives of this project are to:

- Build a virtual cybersecurity lab using Oracle VirtualBox.
- Establish communication between multiple virtual machines.
- Configure a centralized Splunk Enterprise server.
- Prepare Windows and Linux endpoints for security log forwarding.
- Install and verify Splunk Universal Forwarders.
- Establish a consistent network architecture.
- Prepare the environment for centralized security monitoring and detection engineering.

## 3. Lab Environment

The lab consists of four virtual machines.

| Virtual Machine | Operating System | Role | IP Address |
|---|---|---|---|
| Splunk Server | Windows Server | Centralized log collection and analysis | `192.168.56.104` |
| Windows VM 1 | Windows | Security event source | `192.168.56.102` |
| Kali Linux | Kali Linux | Linux security event source | `192.168.56.103` |
| Windows VM 2 | Windows | Additional security event source | `192.168.56.105` |

**Virtualization platform:** Oracle VirtualBox

**SIEM platform:** Splunk Enterprise

**Log forwarding port:** TCP `9997`

**Splunk Web interface:** `http://192.168.56.104:8000`

*Note: The IP addresses document the lab addressing used during the associated configuration work. They may change if the VirtualBox network configuration is modified.*

## 4. Network Architecture

The lab uses a virtual network to allow the endpoint machines to communicate with the centralized Splunk server.

The architecture follows a centralized log collection model:

1. Windows and Linux virtual machines generate operating system and security events.
2. Splunk Universal Forwarders collect the selected log data.
3. The forwarders send the data to the Splunk server over TCP port `9997`.
4. Splunk Enterprise receives, indexes, and makes the data available for searching and analysis.

### Network Diagram

```text
                  SOC HOME LAB
                Oracle VirtualBox
                        |
             Virtual Lab Network
                        |
          +-------------+-------------+
          |             |             |
          |             |             |
    Splunk Server   Windows VM 1   Windows VM 2
    Windows Server   Windows OS     Windows OS
    .104              .102           .105
          |
          | TCP 9997
          |
          +-------------------+
                              |
                         Kali Linux
                         .103
                         Linux OS
```

All four virtual machines are intended to communicate through the lab network. The Splunk server acts as the central destination for forwarded security logs.

## 5. Splunk Server Configuration

The Splunk server is hosted on a Windows Server virtual machine.

Its responsibilities include:

- Receiving forwarded event data.
- Indexing collected logs.
- Providing search and investigation capabilities.
- Supporting security monitoring and detection development.

### Server Details

| Configuration | Value |
|---|---|
| Platform | Splunk Enterprise |
| Operating System | Windows Server |
| IP Address | `192.168.56.104` |
| Splunk Web Port | `8000` |
| Receiving Port | `9997` |
| Web Interface | `http://192.168.56.104:8000` |

The Splunk receiving port is configured to accept incoming data from Universal Forwarders.

## 6. Endpoint Configuration

Three endpoint virtual machines are included in the lab.

### Windows VM 1

Windows VM 1 is configured as a security event source.

Planned log sources include:

- Windows Security Event Log.
- Windows System Event Log.
- Windows Application Event Log.

A Splunk Universal Forwarder was installed, and its service was verified as running.

### Kali Linux

Kali Linux provides a Linux-based endpoint for security monitoring exercises.

Potential log sources include:

- Authentication logs.
- System logs.
- SSH-related events.
- Sudo and authentication activity.

A Splunk Universal Forwarder was installed, and its service was verified as running.

### Windows VM 2

Windows VM 2 provides an additional Windows endpoint for generating and investigating security events.

Planned log sources include:

- Windows Security Event Log.
- Windows System Event Log.
- Windows Application Event Log.

A Splunk Universal Forwarder was installed, and its service was verified as running.

## 7. Splunk Universal Forwarder Deployment

Splunk Universal Forwarders were deployed to all three endpoint machines.

The forwarders are responsible for collecting configured log sources and forwarding events to the centralized Splunk server.

### Forwarding Destination

```text
192.168.56.104:9997
```

### Forwarder Verification

The following checks were completed:

| Endpoint | Universal Forwarder Status |
|---|---|
| Windows VM 1 | Running |
| Kali Linux | Running |
| Windows VM 2 | Running |

The forwarder services were confirmed running on all three endpoints.

*Important: A running forwarder service confirms that the software is active. Successful event delivery and indexing are separate checks, documented in the subsequent Splunk configuration and Linux monitoring projects.*

## 8. Initial Network and Service Validation

The initial setup included checks to establish that the required services and network components were available.

Validation activities included:

- Checking that the Splunk service was running.
- Verifying that the Splunk receiving port was listening.
- Checking the status of the Universal Forwarder services.
- Confirming the intended forwarding destination.
- Testing network connectivity between lab components where applicable.

These checks established the foundation for the subsequent log ingestion and detection projects.

## 9. Project Structure

The repository is organized into separate project folders to make the SOC portfolio easier to navigate.

```text
SOC-Analyst-Home-Lab/
│
├── 01-Lab-Architecture/
│   ├── README.md
│   └── [Project 01 supporting files and screenshots]
│
├── 02-Splunk-Configuration/
│   ├── README.md
│   └── detection-screenshots/
│
├── 03-Linux-Security-Monitoring/
│   ├── README.md
│   └── detection-screenshots/
│
└── [Future SOC projects]
```

## 10. Skills Demonstrated

This project demonstrates practical experience with:

- SOC lab architecture and planning.
- Oracle VirtualBox virtualization.
- Windows Server administration.
- Windows and Linux endpoint configuration.
- Splunk Enterprise fundamentals.
- Splunk Universal Forwarder deployment.
- Centralized log collection architecture.
- Network configuration and connectivity troubleshooting.
- Basic service and port verification.
- Technical documentation.

## 11. Project Outcome

The initial SOC home lab architecture was established using four virtual machines.

The Splunk server was prepared as the centralized log collection destination, and Universal Forwarders were installed and verified as running on all three endpoint machines.

This project established the infrastructure needed for the next stages of the SOC portfolio:

- **Project 02:** Splunk Enterprise Configuration and Windows Security Monitoring.
- **Project 03:** Linux Security Monitoring.
- **Project 04:** Windows and Linux Security Event Correlation.

## 12. Future Improvements

Potential improvements to the lab include:

- Expanding the number of monitored endpoints.
- Adding additional Linux distributions.
- Developing more advanced detection rules.
- Building dashboards for security event monitoring.
- Investigating suspicious authentication activity.
- Correlating events across multiple operating systems.
- Documenting incident investigation and response workflows.

---

**Project:** 01 – SOC Analyst Home Lab Architecture  
**Environment:** Oracle VirtualBox  
**SIEM:** Splunk Enterprise  
**Operating Systems:** Windows Server, Windows, Kali Linux  
**Focus:** SOC infrastructure, virtualization, networking, and log collection architecture
