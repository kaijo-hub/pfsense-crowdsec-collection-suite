# pfSense CrowdSec Collection Suite

[![HubTest CI](https://github.com/kaijo-hub/pfsense-crowdsec-collection-suite/actions/workflows/hubtest.yml/badge.svg)](https://github.com/kaijo-hub/pfsense-crowdsec-collection-suite/actions/workflows/hubtest.yml)

> **Continuous Integration:**  
> This collection is validated on every commit using a full CrowdSec HubTest pipeline  
> (RFC3164, RFC5424, Mixed Logformat, CE/Plus compatibility, core sshd-logs parser  
> with FreeBSD/BSD SSH log support, GeoIP enrichment, Dateparse enrichment, full upstream Hub).

---

## Table of Contents

- [Why this collection exists](#why-this-collection-exists)
- [Overview](#overview)
- [Installation](#installation)
  - [pfSense CE 2.8.1](#pfsense-ce-281-freebsd-15)
  - [pfSense Plus 25.11.x](#pfsense-plus-2511x-freebsd-16-kernel)
- [Installing this Collection](#installing-this-crowdsec-collection-suite)
- [Parser Architecture](#parser-architecture)
- [Supported SSH Event Types](#supported-ssh-event-types)
- [Scenarios](#scenarios)
- [Test Coverage (Hubtest)](#test-coverage-hubtest)
- [Limitations](#limitations)
- [Future Extensions](#future-extensions)
- [Author](#author)

---

## Why this collection exists

pfSense differs significantly from Linux-based systems:

- FreeBSD/OpenSSH produces **different SSH log variants**
- pfSense uses **two distinct syslog formats** (RFC3164 and RFC5424)
- pfSense Plus uses a **different SSH process name** (`sshd-session`)
- During logformat transitions, pfSense emits a **Mixed Logformat**, which breaks many parsers
- Official CrowdSec parsers do **not fully support** pfSense-specific SSH logs

This collection exists to provide:

- A **precise, noise-resistant SSH detection pipeline** tailored to pfSense
- Full compatibility with **pfSense CE** and **pfSense Plus**
- Correct normalization of all **FreeBSD/OpenSSH SSH message variants**
- Robust handling of **RFC3164**, **RFC5424**, and **Mixed Logformat**
- A fully validated, deterministic **CrowdSec HubTest suite**

---

## Overview

- Full compatibility with:
  - pfSense CE 2.8.1 (FreeBSD 15, process name `sshd`)
  - pfSense Plus 25.11.x (FreeBSD 16, process name `sshd-session`)
- Two pfSense-specific SSH parsers:
  - `sshd-logs-rfc3164`
  - `sshd-logs-rfc5424`
- Eight pfSense-specific SSH detection scenarios
- IPv4 and IPv6 support
- Normalized metadata (log_type, source_ip, target_user, service)
- Compatible with CrowdSec enrichers (GeoIP, Dateparse, etc.)
- Designed specifically for pfSense FreeBSD/OpenSSH message variants

---

## Crowdsec Installation

### pfSense CE 2.8.1 (FreeBSD 15)

- Fully supported  
- No workarounds required  
- SSH logs use the process name `sshd`  
- Both RFC3164 and RFC5424 formats supported  

Installation:

1. Download the latest pfSense CrowdSec package  
2. Install via pfSense package manager  
3. Restart CrowdSec: 
   service crowdsec.sh restart

---

### pfSense Plus 25.11.x (FreeBSD 16 kernel)

pfSense Plus is based on FreeBSD 16, which is **not yet officially supported** by CrowdSec.

Installation:

1. Install using FreeBSD‑15 compatibility mode:
   install-crowdsec.sh --freebsd 15

2. Apply the temporary workaround:  
   https://github.com/crowdsecurity/pfSense-pkg-crowdsec/issues/121

3. Restart CrowdSec:
   service crowdsec.sh restart

---

## Installing this CrowdSec Collection Suite

The following steps describe how to manually install the collection, parsers, and scenarios into the CrowdSec runtime directories on pfSense.

1. Install Git (required for cloning the repository)

- pkg update
- pkg install git
- git --version

2. Clone the repository onto the pfSense system

- cd /root
- git clone https://github.com/kaijo-hub/pfsense-crowdsec-collection-suite.git

3. Manually install the collection, parsers, and scenarios

- mkdir -p /usr/local/etc/crowdsec/collections/kaijo
- cp /root/pfsense-crowdsec-collection-suite/collections/kaijo/*.yaml* /usr/local/etc/crowdsec/collections/kaijo/
- mkdir -p /usr/local/etc/crowdsec/parsers/s01-parse/kaijo
- cp /root/pfsense-crowdsec-collection-suite/parsers/s01-parse/kaijo/*.yaml /usr/local/etc/crowdsec/parsers/s01-parse/kaijo/
- mkdir -p /usr/local/etc/crowdsec/scenarios/kaijo
- cp /root/pfsense-crowdsec-collection-suite/scenarios/kaijo/*.yaml /usr/local/etc/crowdsec/scenarios/kaijo/

4. Verify installation (no restart required in pfSense)

- cscli collections list | grep kaijo
- cscli parsers list | grep kaijo
- cscli scenarios list | grep kaijo

---

## Parser Architecture

pfSense can emit SSH logs in:

- RFC3164 Logformat  
- RFC5424 Logformat  
- Mixed Logformat (during transitions)

To support all pfSense variants, this suite provides two dedicated parsers:

### sshd-logs-rfc3164

- Parses pfSense SSH logs in RFC3164  
- Supports `sshd` (CE) and `sshd-session` (Plus)  
- Covers all FreeBSD/OpenSSH SSH message variants  

### sshd-logs-rfc5424

- Parses pfSense SSH logs in RFC5424  
- Required for remote syslog servers  
- Supports both pfSense CE and Plus  

### Mixed Logformat compatibility

Both parsers operate correctly even when pfSense temporarily emits mixed RFC3164/RFC5424 logs.

---

## Supported SSH Event Types

Recognized and classified:

- ssh_success  
- ssh_pam_auth_error  
- ssh_permission_denied  
- ssh_invalid_user  
- ssh_closed  
- ssh_timeout_before_auth  
- ssh_disconnect  
- ssh_connection_flood  
- ssh_conn_closed_scan  

---

## Scenarios

Eight pfSense-specific SSH scenarios, each in:

- RFC3164 variant  
- RFC5424 variant  
- Mixed variant  

Detecting:

- Fast brute-force  
- Slow brute-force  
- Invalid-user scans  
- Connection-closed enumeration  
- Connection floods  
- Disconnect abuse  
- Timeout-before-auth  
- Success after brute-force  

---

## Test Coverage (Hubtest)

This suite has undergone a **three-stage HubTest validation**:

1. RFC3164 Logformat  
2. RFC5424 Logformat  
3. Mixed Logformat  

Each test suite includes:

- config.yaml  
- parser.assert  
- scenario.assert  
- realistic pfSense SSH logs  

Across all tests:

- Thousands of assertions  
- Zero mismatches  
- Deterministic enrichment  
- Full compatibility across CE, Plus, and all log formats  

---

## Limitations

- Only pfSense native FreeBSD/OpenSSH SSH messages supported  
- RFC5424 structured data not parsed  
- Custom syslog pipelines not supported  
- External SSH daemons out of scope  

---

## Future Extensions

- pfSense OpenVPN parser  
- pfSense OpenVPN scenarios  

---

## Author

Author: Johann (kaijo)  
GitHub: https://github.com/kaijo-hub


 
    

   

 
   
