# Project 01 — SOC Lab Network Inventory

## 1. Project Overview

This project documents the network architecture and configuration of my home Security Operations Center (SOC) laboratory.

The objective is to establish a functioning SIEM environment using Splunk Enterprise and multiple Windows and Linux endpoints.

## 2. Lab Environment

| Machine | Operating System | Role |
|---|---|---|
| VM 1 | Windows Server | Splunk Enterprise SIEM |
| VM 2 | Windows | Endpoint monitoring |
| VM 3 | Kali Linux | Linux endpoint monitoring |
| VM 4 | Windows/Linux | Endpoint monitoring |

## 3. Network Configuration

Virtualization Platform: Oracle VirtualBox

Network Type: Host-only Adapter

Network Purpose: Private communication between the SIEM server and endpoint machines.

### IP Address Inventory

| Machine | IP Address |
|---|---|
| Splunk Server | 192.168.56.104 |
| Windows Endpoint | 192.168.56.102 |
| Kali Linux | 192.168.56.103 |
| Windows Endpoint | 192.168.56.105 |

## 4. Splunk Configuration

SIEM Platform: Splunk Enterprise

Splunk Receiving Port: TCP 9997

Splunk Web Interface: TCP 8000

Splunk Management Port: TCP 8089

Splunk Service Status: Running

Receiving Port Status: Listening

## 5. Connectivity Testing

| Test | Result |
|---|---|
| Windows Endpoint to Splunk Server — Ping | Successful |
| Kali Linux to Splunk Server — TCP 9997 | Successful |
| Windows Endpoint to Splunk Server — TCP 9997 | Successful |
| Third Endpoint to Splunk Server — TCP 9997 | Successful |

## 6. Firewall Configuration

A Windows Defender Firewall inbound rule was created for Splunk receiving traffic.

- Direction: Inbound
- Protocol: TCP
- Port: 9997
- Network Profile: Public
- Action: Allow

The rule is restricted to the authorized Windows endpoint IP address.

## 7. Project Objectives

- Establish SIEM infrastructure.
- Configure Splunk Universal Forwarders.
- Collect Windows security events.
- Collect Linux system logs.
- Forward endpoint data to Splunk Enterprise.
- Create security monitoring dashboards.
- Develop detection rules.
- Investigate simulated security incidents.

## 8. Evidence

Screenshots documenting the VirtualBox network configuration, IP addresses, firewall configuration and connectivity tests will be maintained in this project.

## 9. Project Status

Network Infrastructure: Completed
Successfully established network connectivity between the Splunk Enterprise Security server and all three Windows/Linux endpoint VMs using the VirtualBox Host-only network.
Configured Splunk receiving on TCP port 9997 and established endpoint-to-SIEM communication.
Next Phase: Validate centralized log ingestion, verify Windows and Linux event collection, and develop security monitoring dashboards and detection rules.
