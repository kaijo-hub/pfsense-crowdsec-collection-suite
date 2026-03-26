# pfSense CrowdSec Collection Suite

This suite provides a complete and reliable detection pipeline for SSH authentication events on pfSense systems. It includes two pfSense-specific SSH parsers (RFC3164 Logformat and RFC5424 Logformat) and eight SSH security scenarios. The suite is compatible with both pfSense CE and pfSense Plus and is designed to deliver precise, noise-resistant detection for the FreeBSD/OpenSSH environment used by pfSense.

The suite has been validated through an extensive multi-stage hubtest process covering RFC3164 Logformat, RFC5424 Logformat, and temporary Mixed Logformat transitions.

---

## Overview

- Full compatibility with:
  - pfSense CE 2.8.1 (FreeBSD 15, process name "sshd")
  - pfSense Plus 25.11.x (FreeBSD 16, process name "sshd-session")
- Two pfSense-specific SSH parsers:
  - sshd-logs-rfc3164
  - sshd-logs-rfc5424
- Eight pfSense-specific SSH detection scenarios
- Support for IPv4 and IPv6
- Normalized metadata (log_type, source_ip, target_user, service)
- Compatible with CrowdSec enrichers (GeoIP, Dateparse, etc.)
- Designed specifically for pfSense FreeBSD/OpenSSH message variants

---

## Continuous Integration (CrowdSec HubTest)

This collection is validated using a full CrowdSec HubTest pipeline executed on every push and pull request.

The CI performs:

- Full CrowdSec master build (Go, RE2-WASM)
- Complete runtime installation (no portable mode)
- Hub index generation
- Full upstream Hub clone
- Injection of this collection’s parsers, scenarios, and tests
- Execution of all HubTest suites (RFC3164, RFC5424, Mixed)
- GeoIP enrichment validation
- Thousands of assertions across 27 SSH test scenarios

The current CI status:

[![HubTest CI](https://github.com/kaijo-hub/pfsense-crowdsec-collection-suite/actions/workflows/hubtest.yml/badge.svg)](https://github.com/kaijo-hub/pfsense-crowdsec-collection-suite/actions/workflows/hubtest.yml)

---

## Installation

### Part 1 – Installing CrowdSec on pfSense

CrowdSec is **not included in the official pfSense package repository**.  
Unlike OPNsense, which provides a fully integrated CrowdSec package, pfSense currently requires a **manual installation** using the CrowdSec pfSense package available on GitHub:

https://github.com/crowdsecurity/pfSense-pkg-crowdsec/releases

The installation process differs slightly between pfSense CE and pfSense Plus.

---

### pfSense CE 2.8.1 (FreeBSD 15)

- Fully supported
- No workarounds required
- SSH logs use the process name "sshd"
- Both RFC3164 Logformat and RFC5424 Logformat are supported by this suite

Installation:

1. Download the latest pfSense CrowdSec package from GitHub.
2. Install via the pfSense package manager (manual upload).
3. Restart CrowdSec:

service crowdsec.sh restart

---

### pfSense Plus 25.11.x (FreeBSD 16 kernel)

pfSense Plus 25.11.x is based on FreeBSD 16, which is **not yet officially supported by CrowdSec**.  
The pfSense CrowdSec package from GitHub can still be installed, but requires a compatibility flag and a temporary workaround.

To install CrowdSec on pfSense Plus 25.11.x:

1. Install using the FreeBSD 15 compatibility flag:
   sh install-crowdsec.sh --freebsd 15

2. Apply the temporary workaround described here:
   https://github.com/crowdsecurity/pfSense-pkg-crowdsec/issues/121

3. Restart CrowdSec:

service crowdsec.sh restart

---

## Installing this CrowdSec Collection Suite

From the CrowdSec Hub (recommended once published):

cscli collections install kaijo/pfsense-crowdsec-collection-suite

From a local repository clone:

git clone https://github.com/kaijo-hub/pfsense-crowdsec-collection-suite
cscli collections install ./pfsense-crowdsec-collection-suite

Restart CrowdSec afterwards:

service crowdsec.sh restart

---

## Parser Architecture

pfSense can emit SSH logs in two primary formats:

- RFC3164 Logformat
- RFC5424 Logformat

In addition, a Mixed Logformat may appear **temporarily** during a log format transition.  
This occurs when the administrator switches pfSense from RFC3164 Logformat to RFC5424 Logformat (or vice versa). During this changeover period, both formats may appear in the log stream until all services have fully switched to the new format.

To ensure full compatibility with pfSense CE and pfSense Plus, this suite provides two dedicated parsers:

### sshd-logs-rfc3164

- Parses pfSense SSH logs in RFC3164 Logformat
- Supports both "sshd" (CE) and "sshd-session" (Plus)
- Covers all relevant FreeBSD/OpenSSH SSH message variants

### sshd-logs-rfc5424

- Parses pfSense SSH logs in RFC5424 Logformat
- Supports both "sshd" (CE) and "sshd-session" (Plus)
- Required when pfSense sends logs to remote syslog servers in RFC5424 Logformat

### Mixed Logformat compatibility

Both parsers operate correctly even when pfSense temporarily emits a Mixed Logformat during transitions.

---

## Supported SSH Event Types

Both parsers recognize and classify:

- ssh_success - Successful authentication
- ssh_pam_auth_error - PAM authentication failures
- ssh_permission_denied - Permission denied for valid users
- ssh_invalid_user - Attempts with non-existent users
- ssh_closed - Normal connection closure
- ssh_timeout_before_auth - Timeouts before authentication
- ssh_disconnect - Disconnects during or after authentication
- ssh_connection_flood - High-frequency connection attempts
- ssh_conn_closed_scan - Connection-closed enumeration patterns

---

## Scenarios

The suite includes eight pfSense-specific SSH scenarios, each available in:

- RFC3164 Logformat variant
- RFC5424 Logformat variant
- Mixed Logformat variant

These scenarios detect:

- Fast brute-force attacks
- Slow brute-force attacks
- Invalid-user scans
- Connection-closed enumeration
- Connection floods
- Disconnect abuse
- Timeout-before-auth abuse
- Successful login after brute-force attempts

All scenarios rely on the normalized log_type values produced by the parsers.

---

## Test Coverage (Hubtest)

This suite has undergone a three-stage hubtest validation:

1. RFC3164 Logformat test suite  
   All pfSense SSH scenarios and both parsers tested against pure RFC3164 Logformat logs.

2. RFC5424 Logformat test suite  
   All scenarios and both parsers tested against pure RFC5424 Logformat logs.

3. Mixed Logformat test suite  
   All scenarios and both parsers tested against logs containing interleaved RFC3164 and RFC5424 Logformat entries.

### Test assets included

For each scenario and format:

- config.yaml
- parser.assert
- scenario.assert
- Realistic pfSense SSH log files (*.log)

Across all test suites:

- Thousands of assertions
- Zero mismatches
- Deterministic enrichment
- Full compatibility across CE, Plus, and all log formats

---

## Limitations

- Only pfSense native FreeBSD/OpenSSH SSH messages are supported
- RFC5424 structured data elements are not parsed
- Custom syslog pipelines that modify log format are not supported
- External SSH daemons or modified FreeBSD builds are out of scope

---

## Future Extensions

This suite will later include:

- pfSense OpenVPN parser
- pfSense OpenVPN security scenarios

---

## Author

Author: Johann (kaijo)  
GitHub: https://github.com/kaijo-hub
