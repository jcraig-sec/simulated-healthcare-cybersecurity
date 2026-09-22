### [Identity Threat Detection and Incident Response Playbook](identity-threat-response-playbook/)

## Project Overview

This case study presents an identity-threat response playbook for a simulated healthcare environment. The scenario involved a compromised privileged administrator account, anomalous movement within the Clinical Core, and an attempted pivot into a dedicated clinical 5G slice.

The response strategy follows NIST SP 800-61 Rev. 3 concepts and emphasizes identity-focused containment, forensic evidence preservation, continuous monitoring, and uninterrupted patient care.

## Security Scenario

An adversary obtained the credentials of a Level 3 administrator through phishing. The compromised identity was then used for suspicious administrative activity and attempted movement across multiple trust boundaries.

A hospital-wide shutdown would have interrupted access to electronic health records and other clinical services. The playbook therefore focuses on isolating the compromised identity while keeping legitimate healthcare systems operational.

## Detection Logic

### Condition 1 – XP-01 Slice Ingress

Generate an alert when a device attempts to enter the clinical 5G slice through XP-01 and either of the following conditions is present:

* The device type is not approved for the clinical slice.
* The device’s SIM identity is not associated with a valid device record.
* The device identity fails required authentication or registration checks.

This detection identifies unauthorized devices attempting to enter the clinical environment.

### Condition 2 – XP-02 Slice-to-Core Traffic

Generate an alert when administrative protocols or tools cross XP-02 from the clinical 5G slice into the core network.

Suspicious activity includes:

* PowerShell
* Remote Desktop Protocol
* Secure Shell
* Windows Remote Management
* PowerShell execution-policy bypass activity

XP-02 is intended for approved EHR and clinical telemetry traffic. Administrative traffic crossing this boundary represents a deviation from the expected clinical data flow.

### Condition 3 – XP-03 Management Plane Access

Generate an alert when a privileged administrator accesses the external management portal through XP-03 and the activity does not correspond with:

* An approved maintenance window
* The current administrator on-call schedule
* An authorized change-management record
* The administrator’s expected device or location

This condition helps identify the unauthorized use of a legitimate privileged account.

## Incident Response Process

### 1. Preserve Volatile Evidence

Before terminating the compromised sessions, the SOC should collect:

* Active session information
* Current network connections
* Authentication and SAML logs
* Endpoint process information
* Available volatile endpoint data
* Relevant XP-01, XP-02, and XP-03 traffic records

Preserving this information prevents important evidence from being destroyed during containment.

### 2. Contain the Compromised Identity

After evidence is captured:

* Disable the compromised administrator account.
* Revoke active sessions and authentication tokens.
* Block further use of the affected credentials.
* Restrict the identity from the 5G management plane.
* Increase monitoring around systems previously accessed by the account.

This approach contains the attacker without disabling the entire clinical network.

### 3. Determine the Scope

Review collected evidence to identify:

* Systems accessed by the threat actor
* Additional compromised accounts
* Unauthorized files or tools
* Persistence mechanisms
* Changes made with privileged permissions
* Attempts to cross clinical 5G trust boundaries

### 4. Remove the Threat

Remove malicious artifacts and persistence mechanisms, reset any additional compromised credentials, and verify that affected systems return to an approved configuration.

### 5. Restore Administrative Access

Provide the legitimate administrator with new credentials only after the affected identity and systems have been validated. Restore access according to least-privilege requirements and apply heightened monitoring.

### 6. Continue Monitoring and Improvement

Continue monitoring XP-01, XP-02, and XP-03 for the defined detection conditions. Use findings from the incident to improve identity controls, alert thresholds, on-call verification, and future response procedures.

## Clinical Continuity Protocol

The containment strategy targets the compromised identity instead of shutting down the hospital network or the entire clinical 5G slice.

During containment:

* Authorized clinical users retain EHR access.
* Clinical devices continue using approved data paths.
* A separate authorized administrator performs necessary emergency administration.
* Backup administrative access is subject to increased monitoring.
* Changes are limited to actions necessary to maintain patient care.
* Manual downtime procedures remain available if an individual clinical service becomes unavailable.

This approach reduces attacker access while avoiding unnecessary interruption to clinical operations.

## Skills Demonstrated

* Identity-threat detection
* Privileged-account incident response
* 5G trust-boundary monitoring
* NIST SP 800-61 Rev. 3 concepts
* Volatile evidence preservation
* Identity and session containment
* Clinical continuity planning
* Security monitoring and analysis
* Technical playbook development

## Key Takeaway

Identity-focused containment can stop an attacker without requiring a disruptive network-wide shutdown. Effective response requires the SOC to preserve volatile evidence, revoke compromised access, verify the scope of the incident, and maintain approved clinical services throughout containment and recovery.

## Disclaimer

This project is an educational case study based on a simulated healthcare environment. All identities, systems, events, and network boundaries are fictional. The project contains no patient information, production configurations, or proprietary organizational data.
