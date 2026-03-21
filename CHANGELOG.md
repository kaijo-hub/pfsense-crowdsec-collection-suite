# Changelog

## 0.1.6 — 2026-03-16
### Improved
- Updated README with precise terminology: consistently using “RFC3164 Logformat” and “RFC5424 Logformat”.
- Reworked parser architecture section to correctly describe Mixed Logformat as a temporary state during log format transitions.
- Clarified installation instructions for pfSense CE and pfSense Plus.
- Added explicit note that pfSense does not provide a native CrowdSec package in its official repository.
- Highlighted the difference to OPNsense, which offers full native CrowdSec integration.
- Improved documentation clarity and accuracy across multiple sections.

### Fixed
- Removed misleading wording implying official pfSense package availability.
- Corrected and refined descriptions related to pfSense log handling and installation workflow.
- No functional changes to parsers or scenarios.

---

## 0.1.5 - 2026-02-14
- Fixed collection structure for Hub compatibility
- Updated version to 0.1.5
- Prepared for installation testing and hubtest validation

---

## 0.1.4 — 2026-02-14
### Added
- Added full Hub test suite:
  - Parser test for `kaijo/sshd-logs-pfsense`
  - Six scenario test suites with sample logs and expected alerts
- Added pfSense SSH log samples for Hub validation

### Improved
- Updated collection structure to match CrowdSec Hub requirements
- Removed unsupported `version:` field from collection file
- Updated documentation and metadata for Hub submission
- Improved parser metadata and descriptions

### Fixed
- Corrected parser config to remove obsolete fields
- Ensured all log_type values are used by at least one scenario

---

## 0.1.3 — 2026-02-12
### Added
- Added six pfSense‑optimized SSH scenarios:
  - ssh-bruteforce-fast-pfsense
  - ssh-bruteforce-slow-pfsense
  - ssh-success-after-bruteforce-pfsense
  - ssh-connection-flood-pfsense
  - ssh-disconnect-abuse-pfsense
  - ssh-conn-closed-scan-pfsense
- Added collection `kaijo/pfsense-ssh`
- Added tags for pfSense, SSH, FreeBSD, security

### Improved
- Parser documentation updated
- Repository structure aligned with CrowdSec Hub standards

---

## 0.1.2
### Fixed
- Minor parser adjustments
- Improved log_type normalization

---

## 0.1.0
### Initial Release
- Initial pfSense SSH parser

